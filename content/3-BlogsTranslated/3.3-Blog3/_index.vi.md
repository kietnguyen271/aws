---
title: "Blog 3"
date: ""
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# Xây dựng đường ống streaming serverless bảo mật với Amazon MSK Serverless, Amazon EMR Serverless và IAM

*by Shubham Purwar, Nitin Kumar, and Prashanthi Chinthala on 02 JUN 2025 in  
[Amazon Athena](https://aws.amazon.com/athena/), [Amazon EMR](https://aws.amazon.com/emr/),  
[Amazon Managed Streaming for Apache Kafka (Amazon MSK)](https://aws.amazon.com/msk/),  
Analytics, AWS Big Data  
Permalink • Comments • Share*

Sự tăng trưởng theo cấp số nhân và khối lượng khổng lồ của dữ liệu streaming đã khiến nó trở thành một nguồn tài nguyên quan trọng đối với các tổ chức trên toàn thế giới.  
Để khai thác tối đa tiềm năng này, phân tích thời gian thực (real-time analytics) là yếu tố cần thiết để trích xuất các thông tin có thể hành động (actionable insights).

Được tạo ra từ nhiều nguồn khác nhau, bao gồm mạng xã hội, cảm biến Internet of Things (IoT) và tương tác của người dùng, dữ liệu streaming giúp doanh nghiệp phản ứng nhanh với các xu hướng và sự kiện mới, đưa ra quyết định sáng suốt, và duy trì lợi thế cạnh tranh.

Thông thường, các ứng dụng streaming sử dụng Apache Kafka để thu nhận dữ liệu (data ingestion) và [Apache Spark Structured Streaming](https://spark.apache.org/structured-streaming/) để xử lý.  
Tuy nhiên, việc tích hợp và bảo mật các thành phần này đòi hỏi đa nhiều thách thức đáng kể cho người dùng.

Sự phức tạp trong việc quản lý chứng chỉ (certificates), keystore, và cấu hình TLS để kết nối Spark Streaming với Kafka brokers đôi khi đòi hỏi chuyên môn kỹ thuật cao.

Một nền tảng serverless được quản lý (managed serverless framework) sẽ giúp đơn giản hóa đáng kể quy trình này, loại bỏ nhu cầu cấu hình thủ công và giúp tích hợp liền mạch giữa các thành phần quan trọng.

Để đơn giản hóa việc quản lý và bảo mật trong kiến trúc streaming truyền thống, bạn có thể sử dụng [Amazon Managed Streaming for Apache Kafka](https://aws.amazon.com/msk/) (Amazon MSK).

Dịch vụ được quản lý toàn phần (fully managed) này giúp đơn giản hóa quá trình thu nhận và xử lý dữ liệu.  
[Amazon MSK Serverless](https://aws.amazon.com/msk/serverless/) loại bỏ nhu cầu quản lý cụm (cluster management)
và tự động mở rộng (scaling), đồng thời tăng cường bảo mật bằng cách tích hợp với [AWS Identity and Access Management](https://aws.amazon.com/iam/) (IAM) để xác thực (authentication) và ủy quyền (authorization).

Cách tiếp cận hợp nhất này thay thế quy trình phức tạp trong quản lý chứng chỉ và khóa bảo mật được yêu cầu bởi xác thực client TLS (TLS client authentication) thông qua [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/), giúp tinh gọn vận hành và nâng cao bảo vệ dữ liệu.

Ví dụ: khi một client cố gắng gửi dữ liệu vào cluster, MSK Serverless sẽ xác minh danh tính (identity) và quyền truy cập (permissions) của client thông qua IAM.

Để xử lý dữ liệu hiệu quả, bạn có thể sử dụng [Amazon EMR Serverless](https://aws.amazon.com/emr/serverless/) cùng với ứng dụng Spark được xây dựng trên Spark Structured Streaming framework, cho phép xử lý dữ liệu gần thời gian thực (near real-time).  
Cấu hình này giúp xử lý liền mạch các khối dữ liệu lớn từ MSK Serverless, sử dụng IAM authentication để đảm bảo tốc độ và bảo mật cao trong quá trình xử lý.

Bài viết này trình bày một giải pháp toàn diện đầu-cuối (end-to-end solution) để xử lý dữ liệu từ MSK Serverless bằng EMR Serverless Spark Streaming job, được bảo mật bằng IAM authentication.  
Ngoài ra, bài viết còn minh họa cách truy vấn dữ liệu đã xử lý bằng [Amazon Athena](https://aws.amazon.com/athena/), mang đến quy trình xử lý và phân tích dữ liệu tích hợp, liền mạch.

Giải pháp này cho phép truy vấn dữ liệu gần thời gian thực (near real-time querying) của dữ liệu mới nhất được xử lý từ MSK Serverless và EMR Serverless thông qua Athena, mang lại phân tích và thông tin tức thời (instant insights & analytics).

## Tổng quan giải pháp

Sơ đồ dưới đây minh họa kiến trúc (architecture) mà bạn sẽ triển khai trong bài viết này.
![architecture-diagram](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-1.png)
Quy trình làm việc bao gồm các bước sau:

1. Kiến trúc bắt đầu với MSK Serverless cluster được cấu hình xác thực bằng IAM.  
   Một [Amazon Elastic Compute Cloud](https://aws.amazon.com/ec2/) (Amazon EC2) instance chạy tập lệnh Python `producer.py` đóng vai trò là trình tạo dữ liệu (data producer), gửi dữ liệu mẫu đến Kafka topic trong cluster.

2. Spark Streaming job lấy dữ liệu từ Kafka topic, lưu trữ vào [Amazon Simple Storage Service](https://aws.amazon.com/s3/) (Amazon S3) và tạo bảng tương ứng trong [AWS Glue Data Catalog](https://aws.amazon.com/glue/).  
   Trong quá trình tiêu thụ dữ liệu liên tục từ Kafka topic, job luôn cập nhật dữ liệu mới nhất.  
   Với checkpointing được bật, job theo dõi các bản ghi đã xử lý, cho phép tiếp tục từ điểm dừng trước đó trong trường hợp có sự cố, đảm bảo quá trình xử lý dữ liệu liền mạch.

3. Để phân tích dữ liệu, người dùng có thể sử dụng Athena — một dịch vụ truy vấn serverless. Athena cho phép thực hiện các truy vấn SQL tương tác trực tiếp trên dữ liệu trong Amazon S3, mà không cần quản lý cơ sở hạ tầng phức tạp.

## Điều kiện tiên quyết

Trước khi bắt đầu, hãy đảm bảo rằng bạn đã có:
- Tài khoản AWS đang hoạt động với tính năng thanh toán (billing) được bật

- Người dùng IAM có quyền administrator access (AdministratorAccess policy) hoặc các quyền cụ thể để tạo và quản lý các tài nguyên như: virtual private cloud (VPC), subnet, security group, IAM roles, NAT gateway, internet gateway, EC2 client, MSK Serverless, EMR Serverless, [Amazon EMR Studio](https://aws.amazon.com/emr/features/emr-studio/) và S3 buckets

- Dung lượng VPC (VPC capacity) đủ trong AWS Region bạn chọn

Mặc dù việc sử dụng IAM user có administrator access sẽ hoạt động tốt,  
nhưng khuyến nghị rằng trong môi trường production, bạn nên tuân theo nguyên tắc phân quyền tối thiểu (principle of least privilege) — bằng cách tạo custom IAM policies chỉ bao gồm các quyền cần thiết.  
Người dùng IAM mà chúng ta tạo trong hướng dẫn này có gán AdministratorAccess policy, tuy nhiên bạn không nhất thiết cần quyền cao như vậy.

Đối với bài viết này, chúng ta sẽ tạo các tài nguyên của giải pháp trong Region **us-east-2**, sử dụng [AWS CloudFormation](https://aws.amazon.com/cloudformation/) templates.  
Trong các phần tiếp theo, chúng tôi sẽ hướng dẫn bạn cách cấu hình tài nguyên và triển khai giải pháp.

---

# Create MSK Serverless and EMR Serverless resources

Stack **vpc-msk-emr-serverless-studio.yaml** sẽ tạo các tài nguyên sau:  
VPC, subnet, security group, IAM roles, NAT gateway, internet gateway, EC2 client, MSK Serverless, EMR Serverless, EMR Studio, và S3 buckets.

Để tạo các tài nguyên cho giải pháp này, hãy thực hiện các bước sau:

1. **Khởi chạy stack vpc-msk-emr-serverless-studio** bằng CloudFormation template.  
   [Amazon Web Services Sign-In](https://signin.aws.amazon.com/)

2. **Cung cấp các giá trị tham số (parameter values)** như được liệt kê trong bảng sau.
| Parameters       | Description                                                                 | Sample value                                    |
|------------------|------------------------------------------------------------------------------|--------------------------------------------------|
| **EnvironmentName** | Tên môi trường được thêm làm tiền tố (prefix) cho các tên tài nguyên.        | msk-emr-serverless-pipeline                      |
| **InstanceType**    | Loại EC2 instance dành cho **Amazon MSK client**.                          | t2.micro                                         |
| **LatestAmiId**     | ID của **Amazon Linux 2023 AMI** mới nhất cho EC2 instance. Bạn có thể sử dụng giá trị mặc định. | /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-6.1-x86_64 |
| **VpcCIDR**         | Phạm vi địa chỉ IP (**CIDR notation**) cho VPC này.                        | 10.192.0.0/16                                    |
| Parameters            | Description                                                                 | Sample value     |
| **PublicSubnet1CIDR** | Phạm vi địa chỉ IP (CIDR notation) cho **public subnet** trong **Availability Zone đầu tiên**. | 10.192.10.0/24   |
| **PublicSubnet2CIDR** | Phạm vi địa chỉ IP (CIDR notation) cho **public subnet** trong **Availability Zone thứ hai**. | 10.192.11.0/24   |
| **PrivateSubnet1CIDR** | Phạm vi địa chỉ IP (CIDR notation) cho **private subnet** trong **Availability Zone đầu tiên**. | 10.192.20.0/24   |
| Parameters             | Description                                                                 | Sample value     |
| **PrivateSubnet2CIDR** | Phạm vi địa chỉ IP (CIDR notation) cho private subnet trong Availability Zone thứ hai. | 10.192.21.0/24   |

Quá trình tạo stack có thể mất khoảng 10 phút để hoàn tất.

Sau khi stack được tạo xong, bạn có thể kiểm tra thông tin trong tab Outputs  
để xem các giá trị đầu ra (outputs) của stack.
![Outputs](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-4.jpg)

Tiếp theo, bạn sẽ thiết lập quá trình thu nhận dữ liệu (data ingestion) vào Kafka topic từ Kafka EC2 instance

## Produce records to Kafka topic

Thực hiện các bước sau để thiết lập data ingestion:

1. Truy cập Amazon EC2 console, đến EC2 instance mà bạn đã tạo bằng CloudFormation template.

   ![EC2 console](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-5.jpeg)

2. Đăng nhập vào EC2 instance bằng Session Manager, một tính năng của AWS Systems Manager.

3. Chọn instance có tên **msk-emr-serverless-blog**, sau đó chọn **Connect** để kết nối.

   ![Session Manager](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-6.jpg)

4. Tạo **Kafka topic** trong **MSK Serverless** từ EC2 instance.


 a.Trong lệnh export sau, hãy thay thế my-endpoint bằng giá trị MSKBootstrapServers được hiển thị trong Outputs của CloudFormation stack:$ sudo su - ec2-user
$ BS=<your-msk-serverless-endpoint (e.g.) boot-xxxxxx.yy.kafka-serverless.us-east-2.amazonaws.com:9098>



 b.Chạy lệnh sau trên EC2 instance để tạo một Kafka topic có tên sales_data_topic.


Kafka client đã được cài đặt sẵn trong thư mục /home/ec2-user của người dùng ec2-user, kèm theo MSK IAM Authentication JAR và tệp cấu hình client tại đường dẫn
 /home/ec2-user/kafka_2.12-2.8.1/bin/client.properties — trong đó đã bao gồm các thuộc tính cấu hình IAM authentication.
Mã sau đây hiển thị nội dung của tệp client.properties:
security.protocol=SASL_SSL
sasl.mechanism=AWS_MSK_IAM
sasl.jaas.config=software.amazon.msk.auth.iam.IAMLoginModule required;
sasl.client.callback.handler.class=software.amazon.msk.auth.iam.IAMClientCallbackHandler

/home/ec2-user/kafka_2.12-2.8.1/bin/kafka-topics.sh \
--bootstrap-server $BS \
--command-config /home/ec2-user/kafka_2.12-2.8.1/bin/client.properties \
--create --topic sales_data_topic \
--partitions 10

Created topic sales_data_topic.



5.Chạy lệnh sau để gửi dữ liệu (produce records) vào Kafka topic bằng tập lệnh Python syntheticSalesDataProducer.py có sẵn trong EC2 instance.
 Hãy cập nhật giá trị Region cho phù hợp với khu vực AWS bạn đang sử dụng.


nohup python3 -u syntheticSalesDataProducer.py --num_records 1000 \
--sales_data_topic sales_data_topic --bootstrap_server $BS \
--region=us-east-2 > syntheticSalesDataProducer.log &

## Tìm hiểu về Amazon MSK IAM authentication với EMR Serverless

Amazon MSK IAM authentication cho phép xác thực (authentication) và ủy quyền (authorization) an toàn cho các Kafka cluster (MSK Serverless) bằng cách sử dụng IAM roles.

Khi được tích hợp với EMR Serverless Spark Streaming, Amazon MSK IAM authentication cho phép các Spark job truy cập Kafka topics một cách an toàn, sử dụng IAM roles để kiểm soát quyền truy cập chi tiết (fine-grained access control). Điều này đảm bảo quy trình xử lý và truyền dữ liệu (data processing and streaming) được bảo mật cao.

---

## Cấu hình IAM policy

Để cho phép EMR Serverless jobs xác thực với MSK Serverless cluster thông qua IAM, bạn cần gắn (attach) các quyền liên quan đến Kafka vào IAM role của EMR Serverless job execution.

Các quyền này cho phép job thực hiện những thao tác cần thiết trên Kafka cluster, Kafka topics, và consumer groups.

Chính sách IAM policy sau đây cần được gắn vào EMR Serverless job execution role để cung cấp các quyền cần thiết:
	
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

Mã cấu hình trên liên quan đến các **hành động (actions)** sau:

- **Connect, DescribeCluster** – Cần thiết để **khởi tạo kết nối bảo mật** và lấy thông tin metadata.

- **DescribeTopic, ReadData, WriteData** – Cho phép **đọc và ghi dữ liệu** (tiêu thụ và sản xuất dữ liệu).

- **CreateTopic (tùy chọn)** – Cho phép **tạo topic động** khi cần.

- **AlterGroup, DescribeGroup** – Cần thiết để **quản lý consumer group** trong các **streaming jobs**.

Các quyền này đảm bảo rằng Spark Streaming job có thể **xác thực (authenticate)** và tương tác an toàn với MSK Serverless resources bằng IAM role của nó.

---

## Các thư viện phụ thuộc cần thiết

Để bật Amazon MSK IAM authentication trong Spark (đặc biệt là trên EMR Serverless), bạn cần bao gồm các JAR phụ thuộc sau trong Spark Streaming job thông qua tham số **sparkSubmitParameters**:
- [spark-sql-kafka-0-10_2.12](https://mvnrepository.com/artifact/org.apache.spark/spark-sql-kafka-0-10_2.12) – Trình Kafka connector cho Spark Structured Streaming. Cung cấp DataFrame API để đọc và ghi dữ liệu từ/đến Kafka.

- [aws-msk-iam-auth](https://mvnrepository.com/artifact/software.amazon.msk/aws-msk-iam-auth) – JAR này cung cấp cơ chế xác thực IAM cần thiết để kết nối với MSK Serverless thông qua cơ chế **AWS_MSK_IAM SASL mechanism**.

Bạn có thể thêm trực tiếp các thư viện phụ thuộc này bằng cách chỉ định chúng trong đối số **--packages** khi gửi EMR Serverless job.  
Ví dụ:
packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.1,software.amazon.msk:aws-msk-iam-auth:2.2.0


Khi job được gửi đi, EMR Serverless sẽ tự động tải xuống các JAR này từ Maven Central (hoặc từ kho lưu trữ khác mà bạn đã cấu hình) trong quá trình chạy (runtime).  
Bạn không cần gói (bundle) thủ công các JAR này, trừ khi bạn cần sử dụng ngoại tuyến (offline) hoặc yêu cầu phiên bản cụ thể.
## Cấu hình Spark Streaming job cho Amazon MSK IAM authentication

Trong ứng dụng Spark Streaming của bạn, hãy cấu hình Kafka source với các thuộc tính SASL để kích hoạt IAM-based authentication.  
Đoạn mã sau minh họa cấu hình cần thiết:

topic_df = (spark.readStream
    .format("kafka")
    .option("kafka.bootstrap.servers", kafka_bootstrap_servers)
    .option("subscribe", topic_input)
    .option("startingOffsets", "earliest")
    .option("kafka.security.protocol","SASL_SSL")
    option("kafka.sasl.mechanism","AWS_MSK_IAM")
    .option("kafka.sasl.jaas.config","software.amazon.msk.auth.iam.IAMLoginModule required;")
    .option("kafka.sasl.client.callback.handler.class","software.amazon.msk.auth.iam.IAMClientCallbackHandler")
    .load()
    .selectExpr("CAST(value AS STRING)")
)

Các thuộc tính chính bao gồm:

- **kafka.security.protocol = SASL_SSL** – Kích hoạt truyền thông mã hóa (encrypted communication) qua SSL với SASL authentication.

- **kafka.sasl.mechanism = AWS_MSK_IAM** – Chỉ định Kafka sử dụng cơ chế xác thực dựa trên IAM (IAM-based SASL mechanism).

- **kafka.sasl.jaas.config = software.amazon.msk.auth.iam.IAMLoginModule required;**  
  – Xác định module đăng nhập (login module) do AWS cung cấp để tích hợp IAM.

- **kafka.sasl.client.callback.handler.class = software.amazon.msk.auth.iam.IAMClientCallbackHandler**  
  – Xử lý việc ký và xác thực thực tế bằng IAM role.
Với các thiết lập này, Spark sẽ sử dụng IAM credentials được gán với EMR Serverless job execution role để xác thực với MSK Serverless, mà không cần thêm bất kỳ credentials, chứng chỉ (certificates) hoặc bí mật (secrets) nào khác.

---

## Xử lý dữ liệu bằng EMR Serverless streaming job với Amazon MSK IAM authentication

Thực hiện các bước sau để chạy Spark Streaming job nhằm xử lý dữ liệu từ MSK Serverless:

1. Gửi (submit) Spark Streaming job đến EMR Serverless bằng AWS Command Line Interface (AWS CLI) — công cụ này đã được cài sẵn trên EC2 instance.

2. Đăng nhập vào EC2 instance bằng Session Manager.  

   - Chọn instance có tên **msk-emr-serverless-blog**, sau đó chọn **Connect**.

3. Chạy lệnh sau để gửi (submit) streaming job.  

   - Cung cấp các tham số (parameters) lấy từ CloudFormation stack output.

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
4. Sau khi bạn gửi (submit) job, hãy đăng nhập vào EMR Studio bằng URL được hiển thị trong giá trị **EmrServerlessStudioURL** từ CloudFormation stack output.

5. Trong thanh điều hướng (navigation pane), chọn **Applications** trong phần Serverless.

6. Chọn application ID tương ứng với giá trị  
   **EmrServerlessSparkApplicationID** từ CloudFormation stack output.

7. Trong tab **Streaming job runs**, xác minh rằng job đã được gửi thành công, và chờ cho đến khi job bắt đầu chạy.

![Streaming job runs screenshot](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/blog-4406-image-7.jpeg)

---

## Xác thực dữ liệu trong Athena

Sau khi EMR Serverless Spark Streaming job đã chạy và tạo bảng dữ liệu đã xử lý (processed data table) trong Data Catalog, hãy thực hiện các bước sau để xác thực dữ liệu bằng Athena:

1. Mở Athena console, truy cập query editor.

2. Chọn Data Catalog làm nguồn dữ liệu (data source).

3. Chọn database **emrblog** — được tạo bởi streaming job.

4. Để xác thực dữ liệu, chạy truy vấn (query) sau:

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

## Clean up

Để xóa các tài nguyên và tránh phát sinh chi phí, hãy thực hiện các bước sau:

1. **Đăng nhập vào EMR Studio** bằng URL được hiển thị trong giá trị  
   **EmrServerlessStudioURL** của CloudFormation stack output.

2. Trong **thanh điều hướng (navigation pane)**, chọn **Applications** trong phần Serverless.

3. Chọn **application ID** tương ứng với giá trị  
   **EmrServerlessSparkApplicationID** trong CloudFormation stack output.

4. Trong tab **Streaming job runs**, chọn job đang chạy và **hủy (cancel)** job đó.

5. Truy cập **AWS CloudFormation console** và xóa stack có tên  
   **vpc-msk-emr-serverless-studio**.

---

## Conclusion
Trong bài viết này, chúng tôi đã trình bày một pipeline serverless để xử lý dữ liệu streaming với IAM authentication, giúp bạn tập trung vào việc khai thác thông tin phân tích (analytics insights) thay vì quản lý hạ tầng phức tạp.

Bạn có thể tùy chỉnh mã Spark Streaming trong EMR Serverless để áp dụng các chuyển đổi (transformations) và bộ lọc (filters), đảm bảo rằng dữ liệu hợp lệ được tải vào Amazon S3.

Giải pháp này kết hợp sức mạnh của Amazon EMR Serverless Spark Streaming và MSK Serverless, được tích hợp an toàn thông qua IAM authentication — cho phép bạn đơn giản hóa quy trình streaming mà không cần lo về việc quản lý tích hợp giữa Amazon MSK và Amazon EMR Spark Streaming.

---

## Về các tác giả

![shubham-purwar](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2023/09/06/shubham-100.jpeg)

**Shubham Purwar** là AWS Analytics Specialist Solution Architect.  
Anh hỗ trợ các tổ chức khai thác tối đa tiềm năng dữ liệu của họ bằng cách thiết kế và triển khai các giải pháp phân tích (analytics solutions) có khả năng mở rộng (scalable), bảo mật (secure) và hiệu năng cao (high-performance) trên nền tảng AWS.  
Với kiến thức chuyên sâu về các dịch vụ phân tích của AWS, Shubham hợp tác chặt chẽ với khách hàng để hiểu rõ yêu cầu kinh doanh riêng biệt và xây dựng các giải pháp tùy chỉnh (customized solutions) mang lại thông tin có thể hành động (actionable insights), góp phần thúc đẩy tăng trưởng doanh nghiệp.  
Trong thời gian rảnh, Shubham thích dành thời gian cho gia đình và du lịch khắp thế giới.

![nitin-kumar](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/Nitinnew-100x111.jpg)

**Nitin Kumar** là Cloud Engineer (ETL) tại AWS, chuyên về AWS Glue.  
Với hơn 10 năm kinh nghiệm, anh có thể mạnh trong việc hỗ trợ khách hàng xử lý khối lượng dữ liệu lớn (big data workloads), tập trung vào xử lý dữ liệu (data processing) và phân tích dữ liệu (analytics).  
Anh luôn tận tâm giúp khách hàng vượt qua các thách thức liên quan đến ETL, đồng thời xây dựng các pipeline xử lý và phân tích dữ liệu có khả năng mở rộng (scalable) trên AWS.  
Trong thời gian rảnh, Nitin thích xem phim, nấu ăn và dành thời gian cho gia đình.
![prashanthi-chinthala](https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2025/05/26/WhatsApp-Image-2025-05-23-at-9.40.42-PM-100x103.jpeg)

**Prashanthi Chinthala** là Cloud Engineer (DIST) tại AWS.  
Cô hỗ trợ khách hàng giải quyết các thách thức liên quan đến Amazon EMR và xây dựng các pipeline xử lý và phân tích dữ liệu (data processing & analytics pipelines) có khả năng mở rộng (scalable) trên nền tảng AWS.
