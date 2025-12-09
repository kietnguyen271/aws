---
title: "Workshop"
date: ""
weight: 5
chapter: false
pre: " <b> 5. </b> "
---





# BUILDING THE SMOKING-CESSATION SYSTEM ON AWS (END-TO-END)

This workshop provides a full, practical, step-by-step guide to building a complete AWS-based system for the **Smoking-Cessation Application**, including Backend (Lambda + API Gateway), Databases (PostgreSQL + MongoDB), Authentication (Cognito), Infrastructure (VPC, EC2, Security), Frontend Hosting (S3 + CloudFront), Monitoring (CloudWatch), and final Cleanup.

Each module corresponds to a major architectural component of the system.

---

## 🎯 Workshop Objectives

By completing all modules, you will understand how to:

- Build a microservices-based system on AWS  
- Implement user authentication with Cognito  
- Create and operate Lambda serverless functions  
- Publish REST APIs through API Gateway  
- Deploy databases on EC2 (PostgreSQL + MongoDB)  
- Configure VPC, subnets, NAT Gateway, routing, and security  
- Host a frontend application using S3 and CloudFront  
- Monitor logs, metrics, errors, and costs using CloudWatch  
- Optimize cost and clean up unused infrastructure  

---

## 🧩 System Architecture Overview

This workshop will guide you through building a full production-grade system consisting of:

- **8 Lambda functions**  
- **2 REST APIs** (User API & Chat API)  
- **PostgreSQL + MongoDB** databases on EC2  
- **S3 + CloudFront** for frontend hosting  
- **Cognito User Pool** for authentication  
- **Custom VPC** with public/private subnets, NAT Gateway, and NLB  
- **CloudWatch Dashboards & Alarms**  
- **Secrets Manager** for secure credential storage  

---

## 📚 Workshop Structure (10 Modules)

> Each module contains detailed step-by-step instructions with screenshots.

---

### **1️⃣ [Introduction](5.1-Introduction/)**  
Overview of the project goals, AWS services used, and system architecture.

---

### **2️⃣ [Prerequisites](5.2-Prerequisites/)**  
Prepare your AWS environment, IAM user, VS Code, SSH keys, and project directory structure.

---

### **3️⃣ [Cognito Setup](5.3-Setup-cognito/)**  
Create User Pool, App Client, password policy, verification email, and post-confirmation Lambda trigger.

---

### **4️⃣ [Lambda Setup](5.4-setup-lambda/)**  
Create all backend Lambda functions, assign IAM roles, configure environment variables, and connect to Secrets Manager.

---

### **5️⃣ [API Gateway Setup](5.5-Setup-api-gateway/)**  
Create two REST APIs, integrate with Lambda, configure authorization, enable CORS, set request validation and throttling.

---

### **6️⃣ [RDS & Database Setup (EC2)](5.6-Setup-rds-database/)**  
Create two EC2 database servers, install PostgreSQL & MongoDB, create database users, configure instances for production.

---

### **7️⃣ [S3 + CloudFront Setup](5.7-setup-s3-cloudfront/)**  
Configure S3 buckets, static website hosting, OAC, CloudFront distribution, HTTPS, caching, and invalidation.

---

### **8️⃣ [VPC & Security Setup](5.8-setup-vpc-security/)**  
Create a custom VPC, subnets, routing tables, NAT Gateway, Internet Gateway, Security Groups, and Network Load Balancer.

---

### **9️⃣ [Monitoring & Logging](5.9-Monitoring-logging/)**  
Set up CloudWatch dashboards, logs, alerts, SNS notifications, CloudTrail auditing, and X-Ray tracing.

---

### **🔟 [Cleanup & Cost Optimization](5.10-cleanup/)**  
Analyze costs, remove unused resources, back up databases, archive S3 objects, and clean up all AWS infrastructure safely.

---

## ✔ Final Notes

This workshop equips you with the knowledge to build a **complete, secure, scalable, and cost-efficient AWS architecture** from scratch.

You may now continue with **Module 5.1 — Introduction**.

---
