---
title: "Blog 3"
date: ""
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# Building a secure serverless streaming pipeline with Amazon MSK Serverless, Amazon EMR Serverless, and IAM

*by Shubham Purwar, Nitin Kumar, and Prashanthi Chinthala on 02 JUN 2025 in  
[Amazon Athena](https://aws.amazon.com/athena/), [Amazon EMR](https://aws.amazon.com/emr/),  
[Amazon Managed Streaming for Apache Kafka (Amazon MSK)](https://aws.amazon.com/msk/),  
Analytics, AWS Big Data  
Permalink • Comments • Share*

The exponential growth and massive volume of streaming data have made it an essential resource for organizations around the world.  
To fully harness this potential, real-time analytics is critical to extract actionable insights.

Generated from various sources including social networks, Internet of Things (IoT) sensors, and user interactions, streaming data enables businesses to quickly respond to emerging trends and events, make informed decisions, and maintain competitive advantage.

Typically, streaming applications use Apache Kafka for data ingestion and [Apache Spark Structured Streaming](https://spark.apache.org/structured-streaming/) for processing.  
However, integrating and securing these components introduces significant challenges for users.

The complexity of managing certificates, keystores, and TLS configurations required to connect Spark Streaming with Kafka brokers often demands deep technical expertise.

A managed serverless framework can greatly simplify this process, eliminating the need for manual configuration and enabling seamless integration between critical components.

To simplify management and security in traditional streaming architectures, you can use [Amazon Managed Streaming for Apache Kafka](https://aws.amazon.com/msk/) (Amazon MSK).

This fully managed service streamlines the ingestion and processing of data.  
[Amazon MSK Serverless](https://aws.amazon.com/msk/serverless/) removes the need for cluster management and automatic scaling, while also strengthening security by integrating with [AWS Identity and Access Management](https://aws.amazon.com/iam/) (IAM) for authentication and authorization.

This unified approach replaces the complex process of managing certificates and security keys required by TLS client authentication through [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/), simplifying operations and enhancing data protection.

For example, when a client attempts to send data into a cluster, MSK Serverless verifies the identity and access permissions of the client through IAM.

To efficiently process data, you can use [Amazon EMR Serverless](https://aws.amazon.com/emr/serverless/) along with a Spark application built using Spark Structured Streaming, enabling near real-time processing.  
This configuration seamlessly handles large volumes of data from MSK Serverless, using IAM authentication to ensure high performance and strong security during processing.

This blog presents a comprehensive end-to-end solution for processing data from MSK Serverless using EMR Serverless Spark Streaming jobs secured with IAM authentication.  
Additionally, it demonstrates how to query processed data using [Amazon Athena](https://aws.amazon.com/athena/), providing an integrated and streamlined data processing and analytics workflow.

This solution enables near real-time querying of the latest processed data from MSK Serverless and EMR Serverless through Athena, delivering instant insights and analytics.

## Solution overview

The diagram below illustrates the architecture you will build in this blog.  
![architecture-diagram](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-1.png)

The workflow consists of the following steps:

1. The architecture begins with an MSK Serverless cluster configured with IAM authentication.  
   An [Amazon Elastic Compute Cloud](https://aws.amazon.com/ec2/) (Amazon EC2) instance running a Python script `producer.py` acts as the data producer, sending sample data to a Kafka topic in the cluster.

2. A Spark Streaming job reads data from the Kafka topic, stores it in [Amazon Simple Storage Service](https://aws.amazon.com/s3/) (Amazon S3), and creates corresponding tables in the [AWS Glue Data Catalog](https://aws.amazon.com/glue/).  
   While continuously consuming data from the Kafka topic, the job keeps updating new incoming data.  
   With checkpointing enabled, the job tracks processed records and can resume from the last checkpoint in case of failures, ensuring seamless data processing.

3. To analyze the data, users can leverage Athena — a serverless query service.  
   Athena allows interactive SQL queries to be run directly on data stored in Amazon S3, without requiring complex infrastructure.

## Prerequisites

Before starting, ensure that you have:

- An active AWS account with billing enabled
- An IAM user with administrator access (AdministratorAccess policy) or specific permissions to create and manage resources such as VPC, subnet, security group, IAM roles, NAT gateway, internet gateway, EC2 client, MSK Serverless, EMR Serverless, [Amazon EMR Studio](https://aws.amazon.com/emr/features/emr-studio/), and S3 buckets
- Sufficient VPC capacity in your selected AWS Region

Although using an IAM user with administrator access works fine,  
it is recommended that in production environments you follow the principle of least privilege — by creating custom IAM policies containing only required permissions.  
The IAM user created for this tutorial is granted AdministratorAccess, but such elevated permissions are not strictly necessary.

For this blog, we will create the solution resources in the **us-east-2** Region using [AWS CloudFormation](https://aws.amazon.com/cloudformation/) templates.  
In the following sections, we guide you through configuring resources and deploying the solution.

---

# Create MSK Serverless and EMR Serverless resources

The **vpc-msk-emr-serverless-studio.yaml** stack will create the following resources:  
VPC, subnets, security groups, IAM roles, NAT gateway, internet gateway, EC2 client, MSK Serverless, EMR Serverless, EMR Studio, and S3 buckets.

To create the resources for this solution, follow these steps:

1. **Launch the vpc-msk-emr-serverless-studio stack** using the CloudFormation template.  
   [Amazon Web Services Sign-In](https://signin.aws.amazon.com/)

2. **Provide parameter values** as listed in the table below:

| Parameters | Description | Sample value |
|-----------|-------------|--------------|
| **EnvironmentName** | Environment name prefix for resources. | msk-emr-serverless-pipeline |
| **InstanceType** | EC2 instance type for the **Amazon MSK client**. | t2.micro |
| **LatestAmiId** | Latest **Amazon Linux 2023 AMI** ID for EC2. You may use default value. | /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64 |
| **VpcCIDR** | IP address range (**CIDR notation**) for the VPC. | 10.192.0.0/16 |

| Parameters | Description | Sample value |
|-----------|-------------|--------------|
| **PublicSubnet1CIDR** | IP range for the **public subnet** in the **first Availability Zone**. | 10.192.10.0/24 |
| **PublicSubnet2CIDR** | IP range for the **public subnet** in the **second Availability Zone**. | 10.192.11.0/24 |
| **PrivateSubnet1CIDR** | IP range for the **private subnet** in the **first Availability Zone**. | 10.192.20.0/24 |

| Parameters | Description | Sample value |
|-----------|-------------|--------------|
| **PrivateSubnet2CIDR** | IP range for the private subnet in the second Availability Zone. | 10.192.21.0/24 |

The stack creation process may take about 10 minutes.

After the stack is created, check the **Outputs** tab to view the stack output values.

![Outputs](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-4.jpg)

Next, you will configure the data ingestion process to send data into a Kafka topic from the Kafka EC2 instance.

---

# Produce records to Kafka topic

Follow these steps to configure data ingestion:

1. Access the Amazon EC2 console and navigate to the EC2 instance created via the CloudFormation template.

   ![EC2 console](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-5.jpeg)

2. Log in to the EC2 instance using Session Manager, a feature of AWS Systems Manager.

3. Select the instance named **msk-emr-serverless-blog**, then choose **Connect**.

   ![Session Manager](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-6.jpg)

4. Create a **Kafka topic** in **MSK Serverless** from the EC2 instance.

**a.** In the export command below, replace **my-endpoint** with the value of **MSKBootstrapServers** displayed in the CloudFormation Outputs:

```
$ sudo su - ec2-user
$ BS=<your-msk-serverless-endpoint (e.g.) boot-xxxxxx.yy.kafka-serverless.us-east-2.amazonaws.com:9098>
```

**b.** Run the following command on the EC2 instance to create a Kafka topic named `sales_data_topic`.

Kafka client is already installed in the directory `/home/ec2-user` for the ec2-user, along with the MSK IAM Authentication JAR and the client configuration file located at:

```
/home/ec2-user/kafka_2.12-2.8.1/bin/client.properties
```

The following shows the content of `client.properties`:

```
security.protocol=SASL_SSL
sasl.mechanism=AWS_MSK_IAM
sasl.jaas.config=software.amazon.msk.auth.iam.IAMLoginModule required;
sasl.client.callback.handler.class=software.amazon.msk.auth.iam.IAMClientCallbackHandler
```

Create topic command:

```
/home/ec2-user/kafka_2.12-2.8.1/bin/kafka-topics.sh \
--bootstrap-server $BS \
--command-config /home/ec2-user/kafka_2.12-2.8.1/bin/client.properties \
--create --topic sales_data_topic \
--partitions 10
```

Output:

```
Created topic sales_data_topic.
```

5. Run the following command to produce records into the Kafka topic using the Python script `syntheticSalesDataProducer.py` available in the EC2 instance.  
   Update the **Region** value based on the AWS Region you are using.

```
nohup python3 -u syntheticSalesDataProducer.py --num_records 1000 \
--sales_data_topic sales_data_topic --bootstrap_server $BS \
--region=us-east-2 > syntheticSalesDataProducer.log &
```

---

# Understanding Amazon MSK IAM authentication with EMR Serverless

Amazon MSK IAM authentication enables secure authentication and authorization for Kafka clusters (MSK Serverless) using IAM roles.

When integrated with EMR Serverless Spark Streaming, Amazon MSK IAM authentication allows Spark jobs to safely access Kafka topics, using IAM roles for fine-grained access control.  
This ensures highly secure data processing and streaming.

---

## Configuring IAM policy

To allow EMR Serverless jobs to authenticate with the MSK Serverless cluster via IAM, you must attach Kafka-related permissions to the EMR Serverless job execution role.

These permissions enable the job to perform necessary operations on the Kafka cluster, Kafka topics, and consumer groups.

The following IAM policy must be attached to the EMR Serverless job execution role:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "kafka-cluster:Connect",
                "kafka-cluster:DescribeCluster"
            ],
            "Resource": [
                "arn:aws:kafka:<AWS-REGION>:<ACCOUNTID>:cluster/<SERVERLESS_CLUSTER_NAME>/<ID>"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "kafka-cluster:CreateTopic",
                "kafka-cluster:DescribeTopic",
                "kafka-cluster:WriteData",
                "kafka-cluster:ReadData"
            ],
            "Resource": [
                "arn:aws:kafka:<AWS-REGION>:<ACCOUNTID>:topic/<SERVERLESS_CLUSTER_NAME>/*/*"
            ],
            "Effect": "Allow"
        },
        {
            "Action": [
                "kafka-cluster:AlterGroup",
                "kafka-cluster:DescribeGroup"
            ],
            "Resource": [
                "arn:aws:kafka:<AWS-REGION>:<ACCOUNTID>:group/<SERVERLESS_CLUSTER_NAME>/*/*"
            ],
            "Effect": "Allow"
        }
    ]
}
```

The configuration above relates to the following **actions**:

- **Connect, DescribeCluster** – Required to **initiate secure connections** and retrieve metadata.
- **DescribeTopic, ReadData, WriteData** – Allow **reading and writing data** (consuming and producing data).
- **CreateTopic (optional)** – Allows **dynamic topic creation** if necessary.
- **AlterGroup, DescribeGroup** – Required for **managing consumer groups** in **streaming jobs**.

These permissions ensure that the Spark Streaming job can **authenticate and interact securely** with MSK Serverless resources using its IAM role.

---

## Required dependent libraries

To enable Amazon MSK IAM authentication in Spark (especially on EMR Serverless), include the following JAR dependencies in the Spark Streaming job via the **sparkSubmitParameters**:

- [spark-sql-kafka-0-10_2.12](https://mvnrepository.com/artifact/org.apache.spark/spark-sql-kafka-0-10_2.12) – Kafka connector for Spark Structured Streaming, providing DataFrame APIs to read and write Kafka data.
- [aws-msk-iam-auth](https://mvnrepository.com/artifact/software.amazon.msk/aws-msk-iam-auth) – Provides IAM authentication mechanism required to connect to MSK Serverless using **AWS_MSK_IAM SASL mechanism**.

You can include these dependencies directly using the **--packages** option when submitting the EMR Serverless job.  
Example:

```
packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.1,software.amazon.msk:aws-msk-iam-auth:2.2.0
```

EMR Serverless automatically downloads these JARs from Maven Central (or another configured repository) at runtime.  
You do **not** need to manually bundle these JARs unless offline use or strict version requirements apply.

---

## Configuring Spark Streaming job for Amazon MSK IAM authentication

In your Spark Streaming application, configure the Kafka source with SASL properties to enable IAM-based authentication.  
The following code snippet demonstrates the required configuration:

```
topic_df = (spark.readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", kafka_bootstrap_servers)
    .option("subscribe", topic_input)
    .option("startingOffsets", "earliest")
    .option("kafka.security.protocol","SASL_SSL")
    .option("kafka.sasl.mechanism","AWS_MSK_IAM")
    .option("kafka.sasl.jaas.config","software.amazon.msk.auth.iam.IAMLoginModule required;")
    .option("kafka.sasl.client.callback.handler.class","software.amazon.msk.auth.iam.IAMClientCallbackHandler")
    .load()
    .selectExpr("CAST(value AS STRING)")
)
```

Key properties:

- **kafka.security.protocol = SASL_SSL** – Enables encrypted communication via SSL with SASL authentication.
- **kafka.sasl.mechanism = AWS_MSK_IAM** – Instructs Kafka to use IAM-based SASL authentication.
- **kafka.sasl.jaas.config = software.amazon.msk.auth.iam.IAMLoginModule required;** – Specifies AWS-provided login module for IAM integration.
- **kafka.sasl.client.callback.handler.class = software.amazon.msk.auth.iam.IAMClientCallbackHandler** – Handles signing and actual IAM authentication.

With this configuration, Spark uses IAM credentials associated with the EMR Serverless job execution role to authenticate with MSK Serverless — no additional credentials, certificates, or secrets are required.

---

# Processing data using EMR Serverless streaming job with Amazon MSK IAM authentication

Follow these steps to submit a Spark Streaming job that processes data from MSK Serverless:

1. Submit the Spark Streaming job to EMR Serverless using the AWS Command Line Interface (AWS CLI), which is pre-installed on the EC2 instance.

2. Log in to the EC2 instance via Session Manager.  
   - Select the instance named **msk-emr-serverless-blog**, then choose **Connect**.

3. Run the following command to submit the streaming job:  
   - Provide the parameters from the CloudFormation stack output.

```
sudo su - ec2-user

aws emr-serverless start-job-run \
--application-id <APPLICATION ID> \
--execution-role-arn <EXECUTION ROLE ARN> \
--mode 'STREAMING' \
--job-driver '{
"sparkSubmit": {
"entryPoint": "s3://<EMR BLOG SCRIPT BUCKET>/emr_pyspark_streaming_script/pysparkStreamingBlog.py",
"entryPointArguments":["--topic_input","sales_data_topic","--kafka_bootstrap_servers","<BOOTSTRAP URL WITH PORT>","--output_s3_path","s3://<EMR STREAMING OUTPUT BUCKET>/output/sales-order-data/","--checkpointLocation","s3://<EMR STREAMING OUTPUT BUCKET>/checkpointing/checkpoint-sales-order-data/","--database_name","emrblog","--table_name","sales_order_data"],
"sparkSubmitParameters": "--conf spark.hadoop.hive.metastore.client.factory.class=com.amazonaws.glue.catalog.metastore.AWSGlueDataCatalogHiveClientFactory --conf spark.executor.cores=2 --conf spark.executor.memory=5g --conf spark.driver.cores=2 --conf spark.driver.memory=5g --conf spark.executor.instances=5 --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.1,software.amazon.msk:aws-msk-iam-auth:2.2.0"
}}'
```

4. After submitting the job, log in to **EMR Studio** using the URL from the **EmrServerlessStudioURL** CloudFormation output.

5. In the navigation pane, choose **Applications** under Serverless.

6. Select the application ID matching the value  
   **EmrServerlessSparkApplicationID** from the CloudFormation stack output.

7. In the **Streaming job runs** tab, verify that the job was successfully submitted and wait for it to start running.

![Streaming job runs screenshot](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-7.jpeg)

---

# Validating data in Athena

After the EMR Serverless Spark Streaming job has run and created the processed data table in the Data Catalog, follow these steps to validate the data using Athena:

1. Open the Athena console and access the query editor.

2. Select Data Catalog as the data source.

3. Select the **emrblog** database — created by the streaming job.

4. To validate the data, run the following query:

```sql
SELECT
    DATE_TRUNC('minute', date) AS minute_window,
    ROUND(SUM(total_amount), 2) AS total_amount
FROM
    emrblog.sales_order_data
WHERE
    DATE_TRUNC('day', date) = CURRENT_DATE
GROUP BY
    DATE_TRUNC('minute', date)
ORDER BY  
    minute_window DESC;
```

![athena-query-result](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-8.jpg)

---

# Clean up

To delete the resources and avoid charges, follow these steps:

1. Log in to EMR Studio using the URL shown in the **EmrServerlessStudioURL** CloudFormation output.

2. In the **navigation pane**, choose **Applications** under Serverless.

3. Select the **application ID** matching the value  
   **EmrServerlessSparkApplicationID** in the CloudFormation output.

4. In the **Streaming job runs** tab, select the running job and **cancel** it.

5. Access the **AWS CloudFormation console** and delete the stack named  
   **vpc-msk-emr-serverless-studio**.

---

# Conclusion

In this blog, we presented a serverless pipeline for processing streaming data with IAM authentication, helping you focus on extracting analytics insights instead of managing complex infrastructure.

You can customize your Spark Streaming code in EMR Serverless to apply transformations and filters, ensuring that clean data is loaded into Amazon S3.

This solution combines the power of Amazon EMR Serverless Spark Streaming and MSK Serverless, integrated securely through IAM authentication — enabling you to simplify your streaming workflow without managing the integration between Amazon MSK and Amazon EMR Spark Streaming.

---

# About the authors

![shubham-purwar](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2023/09/06/shubham-100.jpeg)

**Shubham Purwar** is an AWS Analytics Specialist Solution Architect.  
He helps organizations maximize their data potential by designing and implementing analytics solutions that are scalable, secure, and high-performance on AWS.  
With deep expertise in AWS analytics services, Shubham works closely with customers to understand unique business requirements and build customized solutions that deliver actionable insights and drive business growth.  
In his free time, Shubham enjoys spending time with family and traveling around the world.

![nitin-kumar](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/Nitinnew-100x111.jpg)

**Nitin Kumar** is a Cloud Engineer (ETL) at AWS, specializing in AWS Glue.  
With over 10 years of experience, he excels at helping customers manage large-scale data workloads, focusing on data processing and analytics.  
He is dedicated to helping customers overcome ETL-related challenges and build scalable data processing and analytics pipelines on AWS.  
In his free time, Nitin enjoys watching movies, cooking, and spending time with his family.

![prashanthi-chinthala](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/WhatsApp-Image-2025-05-23-at-9.40.42-PM-100x103.jpeg)

**Prashanthi Chinthala** is a Cloud Engineer (DIST) at AWS.  
She helps customers solve challenges related to Amazon EMR and build scalable data processing and analytics pipelines on AWS.
