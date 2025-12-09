---
title: "Proposal"
date: ""
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Smoking Cessation Support System Proposal

## 1. Executive Summary

This proposal outlines the design and implementation of a cloud-based **Smoking Cessation Support Platform** aimed at helping users quit smoking through data tracking, behavioral insights, AI coaching, and community engagement.  
The system integrates a modern, scalable backend infrastructure deployed on **AWS Cloud**, ensuring high availability, security, and seamless user experience.  

The goal is to provide an intelligent, personalized journey for users to monitor, plan, and achieve their smoking cessation goals—while giving administrators and health coaches the tools to support and guide them.

---

## 2. System Objectives

- Help users build and follow personalized quit-smoking plans.  
- Track smoking behavior and health progress in real-time.  
- Offer AI-driven coaching, reminders, and motivational feedback.  
- Enable social interaction and encouragement among members.  
- Provide a secure, cloud-native infrastructure for scalability and reliability.  

---

## 3. Key Features

### User-Oriented Features

- **Registration & Membership Plans:** Users can register, select subscription tiers, and make payments for premium features.  
- **Smoking Status Tracking:** Log daily cigarette consumption, cost, and frequency.  
- **Personalized Cessation Plans:** Create and adjust quitting plans based on user habits and goals.  
- **Progress Tracking:** Display statistics such as smoke-free days, money saved, and health improvements.  
- **Motivational Notifications:** Automated reminders and encouraging messages delivered periodically.  
- **Achievements & Badges:** Unlock milestones such as “7 Days Smoke-Free” or “₫100K Saved”.  
- **Community Interaction:** Share achievements, advice, and encouragement within a supportive network.  
- **AI Coaching Agent:** Personalized guidance and advice powered by machine learning.  
- **Health Device Integration:** Collect data from wearable or IoT health devices for progress tracking.  

### Admin & Operator Features

- **Dashboard & Reports:** Monitor user metrics, engagement, and health impact analytics.  
- **Coach Portal:** Health coaches can interact with users via chat or video for counseling.  
- **Feedback & Rating Management:** Track and respond to user satisfaction metrics.  
- **Payment & Subscription Management:** Manage fee packages and user subscriptions.  

---

## 4. System Architecture (AWS Cloud)

The system leverages AWS-managed services for scalability and security, as visualized in the architecture diagram.

### Frontend Layer

- **Amazon S3** hosts the static website (React or Angular frontend).  
- **Amazon CloudFront** distributes the web content globally and handles SSL/TLS encryption.

### Authentication & Authorization

- **Amazon Cognito** manages user sign-up, sign-in, and identity federation, ensuring secure access for both end-users and coaches.

### Application Layer

- **AWS Lambda** powers serverless services such as payment handling or lightweight API operations.  
- **Network Load Balancer (NLB)** distributes requests across backend EC2 instances.  
- **EC2 Instances (Private Subnet)** host core microservices:
  - **User Service**
  - **Cessation Service**
  - **Social Media Service**

### Data Layer

- **PostgreSQL Databases** for user and cessation data (on EC2 or RDS).  
- **MongoDB** for social features and unstructured data.  
- **S3 Bucket (Backup)** stores periodic encrypted database backups.

### DevOps Pipeline

- **GitLab CI/CD Pipeline** automates deployment to **Amazon ECR** (Elastic Container Registry) and EC2 instances.  
- **VPC Endpoint** ensures secure communication with AWS services without exposing traffic to the public internet.  
- **EC2 Instance Connect Endpoint** enables controlled administrative access.
![Architect](/images/2-Proposal/P.jpg)
**Figure 1 – AWS-based cloud architecture for the Smoking Cessation Platform.**
---

## 5. Security and Compliance

- **Data Encryption:** All sensitive data encrypted in transit (TLS) and at rest (AES-256).  
- **IAM Policies:** Fine-grained access control for different system roles.  
- **Private Subnets:** Backend and databases are isolated from public exposure.  
- **VPC Link & Endpoints:** Secure internal communication between services.  
- **Backup Strategy:** Automated daily backups to S3 with versioning and lifecycle policies.  

---

## 6. Scalability and Performance

- **Auto Scaling:** EC2 and Lambda functions scale based on user demand.  
- **CDN Caching:** CloudFront caches static content for faster global delivery.  
- **Load Balancing:** NLB ensures traffic distribution and fault tolerance.  
- **Decoupled Microservices:** Modular design allows independent scaling of user, cessation, and social services.

---

## 7. Future Enhancements

- Integration with **mobile apps** for Android and iOS.  
- Advanced **AI predictive relapse detection** using user patterns.  
- Real-time chat and video counseling.  
- Integration with **third-party payment gateways**.  
- **Gamified health challenges** and reward systems.

---

## 8. Expected Outcomes

- Increased smoking cessation success rates.  
- Improved user motivation and engagement.  
- Scalable platform capable of supporting large user bases.  
- Secure, compliant, and maintainable cloud infrastructure.

---
[👉 Download Word File](/docs/internship-report.docx)
