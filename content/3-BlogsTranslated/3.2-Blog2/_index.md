---
title: "Blog 2"
date: ""
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
[**AWS for Industries**](https://aws.amazon.com/industries/)

# Energy Virtual Assistant: Transforming Utility Customer Service with AWS Generative AI

*by Darren Roback and Jeremy Cianella on 10 JUL 2025 in  
[Amazon Bedrock](https://aws.amazon.com/bedrock/), [Amazon Bedrock Guardrails](https://aws.amazon.com/bedrock/guardrails/),  
[Amazon CloudWatch](https://aws.amazon.com/cloudwatch/), [Amazon Connect](https://aws.amazon.com/connect/),  
[Amazon DynamoDB](https://aws.amazon.com/dynamodb/), [Amazon Lex](https://aws.amazon.com/lex/),  
[Amazon Timestream](https://aws.amazon.com/timestream/), [AWS Lambda](https://aws.amazon.com/lambda/),  
Energy (Oil & Gas), Industries – Permalink*

In the era of accelerating digital transformation, electric utility providers are facing unprecedented challenges in meeting customer expectations for fast, efficient, and accessible service.

Utility contact centers in particular struggle to deliver efficient customer service. Traditional **Interactive Voice Response (IVR)** systems can handle straightforward, direct requests via keypad input or basic voice commands—such as reporting an outage or checking an account balance. However, more complex, ambiguous, and multi-turn requests often require escalation to live agents, creating bottlenecks in the support process.

This limitation becomes even more serious during severe weather events or large-scale outages, when call volume can spike within minutes—often overwhelming even well-staffed centers. The challenge is further compounded by the need to integrate with multiple backend systems such as **Customer Information Systems (CIS)**, **Billing Information Systems (BIS)**, **Meter Data Management Systems (MDMS)**, **Outage Management Systems (OMS)**, and various knowledge bases required to respond to customer inquiries. All of this results in longer handling times and increased customer frustration.

Modern utility contact centers must effectively manage what we refer to as the **“ABCDs”** of utility customer service. Each core function requires access to different systems and specialized domain expertise:

- **Account management**: Opening, closing, retrieving, and updating customer account information; often requires interaction with CIS.  
- **Billing**: Retrieving account balances, billing history, and processing payments — often requires interaction with BIS.  
- **Consumption**: Analyzing and explaining energy consumption patterns from smart meter data — often requires interaction with MDMS.  
- **Dispatch**: Creating service requests, reporting outages, checking request status, and scheduling maintenance — often requires interaction with OMS.

To effectively handle these functions, customer service representatives must be highly trained and experienced. They must understand complex electricity pricing structures, interpret energy consumption data, explain billing calculations, and navigate complicated policies and procedures. This specialized knowledge takes months to develop, leading to long onboarding times for new hires and difficulty maintaining consistent service quality across all customer interactions.

Limitations of traditional IVR systems and the operational complexity of the utility industry have opened the door for advanced AI-powered solutions. Generative AI—with its ability to understand natural language, process complex queries, and produce human-like responses—offers a promising approach to addressing these challenges.

**Large Language Models (LLMs)** enable us to build virtual assistants that more accurately understand customer intent, handle a wider range of requests without human intervention, and provide more nuanced, context-aware responses.

To address these challenges, we developed the **Energy Virtual Assistant** — an advanced approach to customer service that combines Generative AI with agentic capabilities, intelligent decision-making through chain-of-thought reasoning, and deep integration with utility systems and available enterprise data. This solution blends the power of **LLMs** with domain knowledge and system integration capabilities tailored to the energy industry.

In this article, we discuss the benefits of Generative AI in customer service, present the solution architecture, provide a step-by-step implementation guide, and share recommended next steps for deploying this solution in utility contact centers.

## Benefits of Generative AI in Customer Service

Generative AI represents a major leap forward in customer service technology,  
bringing a comprehensive set of capabilities that reshape how organizations interact  
with their customers.

### Personalization at scale

Unlike traditional systems, generative AI can produce responses that are not only  
accurate but also personalized to each customer's communication style, preferences,  
and interaction history. This transforms ordinary customer service into a nuanced,  
deeply personalized experience, making customers feel genuinely understood.

### Emotional intelligence and sentiment analysis

With advanced sentiment analysis, generative AI can detect subtle nuances in customer  
communication and adjust tone and approach accordingly. Whether a customer is angry,  
confused, or satisfied, the system can deliver empathetic and contextually appropriate  
responses that reflect human-like understanding.

### Multilingual capabilities and cultural awareness

Generative AI breaks down language barriers with strong multilingual support,  
offering near-native fluency across many languages. Beyond translation, it ensures  
culturally appropriate communication, understanding context and subtleties that  
traditional translation tools often miss. This creates a global yet personalized  
customer service experience.

### Unified data integration

Generative AI acts as an intelligent data orchestrator, seamlessly integrating  
information from organizational data silos to generate a holistic view of...

### Operational efficiency and cost optimization

Generative AI can significantly improve cost efficiency by reducing average handling  
time, increasing first-contact resolution rates, fully automating frequent requests,  
and eliminating overtime labor costs due to its 24/7 availability.

### Exceptional scalability

Utility contact centers commonly struggle with fluctuating interaction volumes,  
especially during emergencies like large-scale outages, storms, or wildfires.  
Generative AI-powered contact centers can instantly scale to handle demand spikes,  
maintaining service quality whether processing ten or ten thousand interactions  
simultaneously. This ensures reliability in all situations.

### Always-on availability

Most importantly, generative AI enables continuous 24/7 customer support without  
the high costs associated with traditional staffing models. Customers can access  
assistance anytime, significantly improving service accessibility and satisfaction.

## Solution Overview

The Energy Virtual Assistant is designed to support common customer service  
use cases in the utility industry, such as outage reporting, consumption analytics,  
account information updates, and bill payment processing.

Customers interact with the Energy Virtual Assistant through multiple channels  
(voice or chat), powered by Generative AI to provide fast, accurate, intelligent,  
and personalized responses.

The assistant is built using multiple AWS services, including:

- **[Amazon Connect](https://aws.amazon.com/connect/)** – A cloud-based contact center solution allowing organizations to deliver customer service at any scale. It supports omni-channel communication, skills-based routing, and AI-powered features such as conversational IVR. It serves as the entry point for customer voice interactions with the Energy Virtual Assistant.

- **[Amazon Lex](https://aws.amazon.com/lex/)** – A fully managed AI service for building conversational interfaces (chatbots) using voice or text. It uses the underlying technology of Amazon Alexa and provides the natural language understanding layer for the assistant.

- **[Amazon Bedrock](https://aws.amazon.com/bedrock/)** – A fully managed service providing access to high-performance foundation models (FMs) via a unified API. It enables the Generative AI capabilities of the assistant.

- **[Agents for Amazon Bedrock](https://aws.amazon.com/bedrock/agents/)** – A feature that enables users to create AI agents capable of automatically analyzing and executing complex business tasks by orchestrating FMs, knowledge bases, and APIs. The Energy Virtual Assistant is a Bedrock agent using chain-of-thought reasoning and integrating with utility systems to process customer requests.

- **[AWS Lambda](https://aws.amazon.com/lambda/)** – A serverless compute service that executes code when triggered by events. Lambda provides the compute layer for API integrations to backend utility systems.

- **[Amazon DynamoDB](https://aws.amazon.com/dynamodb/)** – A fully managed serverless NoSQL database used here to simulate utility backend systems such as Account, Billing, and Outage Management.

- **[Amazon Timestream](https://aws.amazon.com/timestream/)** – A fully managed time-series database used to store and analyze energy consumption data, often referred to as MDMS.

- **[Guardrails for Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html)** – A feature providing safety controls such as content filtering, topic blocking, keyword filtering, and redaction of sensitive information to protect interactions between customers and the foundation model.

Before diving into solution architecture, consider how this solution improves typical  
utility customer service scenarios.  
For example, if a customer calls about an unusually high bill, a human agent would previously  
need to check multiple systems: usage history, meter readings, rate structures, and even  
weather data. With our solution, the virtual assistant can automatically collect and  
analyze all of this information and provide context-aware explanations and recommendations —  
all without human intervention.

## **Solution Architecture**

The architecture diagram below provides an overview of the system components.  
Let's walk through each part.

![solution-architecture](https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Figure-1-Solution-architecture.png)

**Figure 1: Solution architecture**

1. **Energy customers** interact with the utility provider through common requests  
via the voice channel. Although this solution is designed for voice, it can be  
extended to support other channels in the future.

2. Customers call into Amazon Connect and are immediately placed into a  
contact flow.

3. An Amazon Lex bot embedded in the Amazon Connect flow acts as the  
speech-to-text interface as customers interact with the Energy Virtual Assistant.

4. The Energy Virtual Assistant is powered by an Amazon Bedrock agent that  
receives, processes, and classifies customer requests.

5. The Amazon Bedrock agent sends customer conversation history, available  
actions, and the current request to a foundation model (FM) for processing.

6. The foundation model uses chain-of-thought reasoning to determine the  
appropriate steps to satisfy the customer request.

7. Action groups represent the actions and system integrations the agent  
can invoke to fulfill customer requests. These include:

   - **Account Action Group** – Opens accounts, retrieves account information,  
     updates account details, or closes accounts.

   - **Billing Action Group** – Retrieves balances, payment histories, and  
     processes customer payments.

   - **Ticketing Action Group** – Creates service tickets, checks ticket status,  
     retrieves all tickets for a specific account, reports outages, and schedules maintenance.

   - **Consumption Action Group** – Retrieves energy consumption data, provides  
     cost estimates based on usage, and explains consumption patterns.

   - **Date/Time Action Group** – Determines current date/time and supports  
     range-based queries needed by the FM.

8. Once required actions are completed, an Amazon Bedrock FM is used to  
process and prepare the final response to the customer.

## **Amazon Connect Call Flow**

The call flow diagram below illustrates the steps performed when customers  
interact with Amazon Connect via the voice channel. Let's walk through each step.

![Amazon Connect Call Flow](https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Figure-2-Amazon-Connect-call-flow.jpg)

**Figure 2: Amazon Connect call flow**

1. When the customer enters the Amazon Connect flow, the **Enable Logging**  
block is used to turn on flow logs stored in **Amazon CloudWatch**.  
Flow logs assist with troubleshooting and tracking the steps taken  
during a customer call.

2. The **Set Language** block configures the language and voice settings  
for text-to-speech (TTS) used in the flow.

3. The **Main Menu** block plays the initial greeting and activates the  
**Amazon Lex** bot (with its configured intents) to begin customer interaction.

---

The intents used in this flow include:

- **EnergyVirtualAgentIntent** – Represents the Amazon Bedrock Energy Virtual  
  Agent and handles all customer requests such as outage reporting,  
  consumption queries, account updates, and bill payments.  
  This intent includes multiple utterances to recognize a wide variety of  
  customer statements.

- **SpeakToAgentIntent** – Used to transfer the customer directly to a live  
  agent in the contact center.  
  Configured with utterances matching phrases commonly used when customers  
  ask to speak to a human.

- **GoodbyeIntent** – Used to end the customer interaction. Configured  
  with common farewell utterances.

4. The **Set Queue** block determines the queue where customers will be  
   placed if they request a live agent.

5. The **Transfer to Queue** block routes callers to the live agent queue.

6. The **Queue at Capacity** block plays a TTS message informing customers  
   that the queue is full.

7. The **Goodbye Prompt** block sends a farewell message via TTS.

8. The **Error Prompt** block notifies the customer if an issue occurs  
   during the call flow.

---
## Prerequisites

Before you begin, ensure that your environment includes the following components:

- [Created an Amazon Connect instance](https://docs.aws.amazon.com/connect/latest/adminguide/amazon-connect-instances.html)  
  which is used to support customer interactions with the Energy Virtual Assistant through the inbound voice channel.

- [Claimed an Amazon Connect phone number](https://docs.aws.amazon.com/connect/latest/adminguide/contact-center-phone-number.html)  
  which is associated with the flow created during deployment.

- [An Amazon Connect BasicQueue](https://docs.aws.amazon.com/connect/latest/adminguide/queues.html)  
  which is created by default with Amazon Connect.

- [Enabled access to Claude 3.5 Haiku](https://aws.amazon.com/bedrock/)  
  in Amazon Bedrock, serving as the foundation model (FM) for the Amazon Bedrock agent.

  - We use the Claude 3.5 inference profile in this solution, which means you must enable model access in the **AWS Regions** us-east-1, us-east-2, and us-west-2.

- [Permissions to deploy the AWS CloudFormation template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-iam.html)

## Deploying the solution

The deployment process for this solution includes the following steps:

1. **Create a CloudFormation stack** to deploy all foundational components.

2. **Configure the EnergyVirtualAgent Lex Bot intent**, because as of this writing, Amazon Lex does not yet support creating intents through CloudFormation or API.

3. **Associate the Amazon Connect phone number with the Amazon Connect flow.**

During deployment, the system automatically generates simulated customer data within the **Account**, **Billing**, and **Consumption** systems.  
Specifically, the datasets created include:

- **AccountData**: five simulated customers, including fields such as  
  **AccountNumber**, CustomerName, EmailAddress, MeterNumber, PhoneNumber, PremiseNumber, RateCode, and ServiceAddress.

- **BillingData**: twelve months of billing data for each customer, including  
  BillingDate, BillingStatus, **DueDate**, **EnergyCharge**, **KWhConsumption**,  
  ServiceCharge, and TotalAmount.

- **IntervalData**: three months of 15-minute interval consumption data for each meter, including  
  L1–L2–L3 Voltage and Current, **InstantaneousPower**, and Frequency.

---

## Creating the CloudFormation stack

Before starting, clone the  
**AWS Samples Energy Virtual Assistant GitHub repository**  
to your local machine, as we will use the CloudFormation YAML template for the first part of the deployment process.

Follow these steps:

1. Open the **AWS CloudFormation console** and select  
   **Create stack > With new resources (standard)**.

2. In the **Prerequisite – Prepare template** section, select **Choose an existing template**, then choose **Upload a template file**.

3. Click **Choose file**, then navigate to the `\deployment` directory inside the cloned repository and upload the file **cloudformation_template.yaml**.

4. Click **Next** to move to the **Specify stack details** screen.

5. In the **Stack name** field, enter **EnergyVirtualAssistant**.

6. In the **ConnectInstanceArn** field, enter the **Amazon Resource Name (ARN)** of your Amazon Connect instance.

7. In the **ConnectQueueArn** field, enter the **ARN** of your **Amazon Connect BasicQueue**.

8. Click **Next** to move to the **Configure stack options** screen.

9. Check the box to allow **AWS CloudFormation** to create **IAM resources** with custom names.

10. Click **Next** to go to the **Review and create** screen.

11. Review all configuration settings, then click **Submit** to create the **CloudFormation stack**.

After submitting, wait for the stack status to update to **CREATE_COMPLETE** before proceeding to the next steps.

---

## Configuring the Lex Bot

When the **CloudFormation stack** deployment is complete, you may proceed to configure the **Amazon Bedrock agent intent** inside the **Amazon Lex bot**.

In this step, you will use the values **EnergyVirtualAgentId** and  
**EnergyVirtualAgentAliasId** generated from the **CloudFormation stack outputs**.

Follow these steps:

1. Open the **Amazon Lex console** and select the **EnergyVirtualAgentLexBot** that was created automatically.

2. Under **Draft version** and **English (US)**, select **Intents**.

3. Select **Add intent**, then choose **Use built-in intent**.

4. Select **AMAZON.BedrockAgentIntent – GenAI feature** as the built-in intent type.

5. In the **Intent name** field, enter **EnergyVirtualAgentIntent** and click **Add**.

6. In the **Description** field, enter:  
   **Intent to handle customer energy requests.**

7. In the **Bedrock Agent ID** field, enter the value **EnergyVirtualAgentId** from the CloudFormation output.

8. In the **Agent Alias ID** field, enter the value **EnergyVirtualAgentAliasId** from the CloudFormation output.

9. Navigate to the `\deployment` directory in the cloned repository and open the file  
   **agent_intent_utterances.md**.

10. In the **Sample utterances** section, select **Plain text** and paste the entire contents of the utterances file.

11. Click **Save intent** at the bottom of the screen.

12. Click **Build** in the upper right corner to apply all changes.

13. Go to the **Bot versions** page and select **Create version**.

14. Click **Create** to generate a new bot version and wait for the process to complete.

15. Under the **Deployment** section, select **Aliases**, then choose the **PROD alias**.

16. Click **Associate version with alias**, then select the new version you created.

17. Click **Associate** to finalize the linkage.

---

## Linking the phone number

You are now ready to associate your previously claimed  
**Amazon Connect phone number** with the **contact flow** created during the CloudFormation deployment.

Follow these steps:

1. Log in to your **Amazon Connect instance** and open the **Phone numbers** page.

2. Select your phone number from the list.

3. In the **Contact flow/IVR** section, enter **EnergyVirtualAgentFlow** into the search field.

4. Select **EnergyVirtualAgentFlow** and click **Save** to assign the contact flow to the phone number.

---

## Testing the solution

In the repository, the **test** directory includes several **test scenarios** that you can use to interact with and validate the **Energy Virtual Assistant**.

To experience and interact with the Energy Virtual Assistant, simply **call the Amazon Connect phone number** that you associated with the contact flow, then ask questions to the **Amazon Bedrock agent**, using the simulated customer data generated during deployment.

---

## Customizing the solution

Readers are encouraged to **customize this solution** to better fit their real-world needs after **cloning the repository** locally.  
The **DynamoDB** and **Timestream** tables in the backend—used as a mock representation of CRM, Billing, Outage, and MDMS systems—may differ from those in a production environment.

First, determine **the types of customer requests** you want the system to process, then customize the backend systems accordingly to support those interactions.

This solution is primarily designed to illustrate the **art of the possible** of **generative-AI-enabled assistants**, while also acting as an **acceleration vehicle** for proof-of-concept (POC) activities.

---

## Cleaning up resources

To **delete the solution** and **avoid additional costs** associated with AWS resources used in this deployment, follow these steps:

1. Log in to your **Amazon Connect instance** and navigate to **Phone numbers**.

2. Select your phone number from the list.

3. In the **Contact flow/IVR** section, enter **EnergyVirtualAgentFlow** into the search field.

4. **Deselect** the **EnergyVirtualAgentFlow** contact flow and click **Save** to remove its association with the phone number.

5. Open the **CloudFormation console** in your AWS account.

6. Locate the **EnergyVirtualAssistant** stack and select **Delete**.

This action will **delete all resources** deployed by the CloudFormation stack, except for the **CloudWatch log groups** created for the Lambda functions and the Amazon Connect instance.  
You may choose to keep or delete these logs depending on your needs.

---

## Summary

In this solution, we demonstrated how to build **agentic solutions** on  
**Amazon Bedrock** and how to integrate **multiple AWS services** to automate customer service workflows.

Implementing **generative AI** in customer service offers several advantages over **traditional IVR**, such as:

- **Eliminating rigid rule-based IVR flows**  
- **Providing 24/7 customer service**  
- **Automatically scaling** during periods of high interaction volume  

We encourage you to **deploy, test, and customize** this solution within your own environment to quickly realize the value of using **generative AI** to **enhance customer experience and service efficiency**.

---

## Darren Roback

<img src="https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Darren-Roback.png" alt="Darren Roback" width="160" style="border-radius: 8px;">

Darren is a Sr. Solutions Architect at Amazon Web Services (AWS), based in St. Louis, Missouri.  
He has over 20 years of experience in the information technology (IT) industry and is passionate about **Data Analytics**, **Generative AI**, **Internet of Things (IoT)**, and **Security and Compliance**.

At AWS, Darren works with customers in the energy and utility sectors to help address business challenges using AWS technologies.  
Outside of work, he enjoys woodworking and spending time with his family.

---

## Jeremy Cianella

<img src="https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Jeremy-Cianella.png" alt="Jeremy Cianella" width="160" style="border-radius: 8px;">

Jeremy Cianella is a Sr. Solutions Architect based in Miami, Florida, supporting customers in the utility and renewable energy sectors.

He has over 15 years of experience in utility operations, enterprise architecture, and cloud technologies, helping customers execute digital transformation initiatives.
