---
title: "Workshop"
date: ""
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


# WORKSHOP

# XÂY DỰNG HỆ THỐNG SMOKING-CESSATION TRÊN AWS TỪ A → Z

Workshop này hướng dẫn triển khai **toàn bộ hệ thống thực tế** bao gồm: Backend (Lambda + API Gateway), Databases (PostgreSQL + MongoDB), Authentication (Cognito), Infrastructure (VPC, EC2, Security), Frontend hosting (S3 + CloudFront), Monitoring (CloudWatch), và Cleanup.

Mỗi module tương ứng với một phần kiến trúc quan trọng trong hệ thống.

---

## 🎯 Mục tiêu tổng quan

Sau toàn bộ workshop, bạn sẽ nắm được:

- Xây dựng hệ thống microservices trên AWS  
- Quản lý user authentication bằng Cognito  
- Tạo & vận hành Lambda serverless backend  
- Publish API bằng API Gateway  
- Tạo database server trên EC2  
- Cấu hình VPC, subnet, NAT Gateway, Security Groups  
- Host frontend bằng S3 + CloudFront  
- Theo dõi & giám sát bằng CloudWatch  
- Tối ưu chi phí & dọn dẹp tài nguyên

---

## 🧩 Kiến trúc tổng quan hệ thống

**Full architecture bao gồm:**

- **8 Lambda Functions**  
- **2 REST APIs (User API + Chat API)**  
- **2 Databases trên EC2** (PostgreSQL + MongoDB)  
- **S3 + CloudFront** cho frontend  
- **Cognito User Pool** để đăng ký / đăng nhập  
- **VPC hoàn chỉnh** với NAT, IGW, NLB  
- **CloudWatch Monitoring + Alarms**  
- **Secrets Manager** cho sensitive data  

---

## 📚 Nội dung Workshop (10 Modules)

> Mỗi module đều có hướng dẫn *step-by-step* kèm hình minh họa.

---

### **1️⃣ [Giới thiệu Workshop](5.1-Introduction/)**  
Phác thảo mục tiêu dự án, kiến trúc tổng quan và các thành phần AWS sẽ sử dụng.

---

### **2️⃣ [Điều kiện tiên quyết](5.2-Prerequisites/)**  
Chuẩn bị môi trường AWS, IAM user, VS Code, SSH key, CLI tools, cấu trúc thư mục dự án.

---

### **3️⃣ [Cấu hình Cognito](5.3-Setup-cognito/)**  
Tạo User Pool, App Client, Password Policy, Post-confirmation Trigger, Email Verification.

---

### **4️⃣ [Cấu hình Lambda Functions](5.4-setup-lambda/)**  
Tạo 5 Lambda functions cho hệ thống, gán IAM Role, thêm environment variables, kết nối Secrets Manager.

---

### **5️⃣ [Cấu hình API Gateway](5.5-Setup-api-gateway/)**  
Tạo 2 REST APIs, mapping với Lambda, bật CORS, request validation, throttling, test API end-to-end.

---

### **6️⃣ [Cấu hình RDS & Database EC2](5.6-Setup-rds-database/)**  
Tạo 2 EC2 database servers, cài PostgreSQL + MongoDB, tạo user, schema, SSH hardening, backup scripts.

---

### **7️⃣ [Cấu hình S3 + CloudFront](5.7-setup-s3-cloudfront/)**  
Tạo S3 hosting cho frontend React, cấu hình OAC, tạo CloudFront distribution, SSL, caching, invalidation.

---

### **8️⃣ [Cấu hình VPC & Security](5.8-setup-vpc-security/)**  
Khởi tạo VPC, subnets, route tables, NAT Gateway, IGW, SGs, NLB cho WebSocket, GuardDuty + Flow Logs.

---

### **9️⃣ [Monitoring & Logging](5.9-Monitoring-logging/)**  
Tạo CloudWatch dashboards, alarms, SNS notifications, CloudTrail, X-Ray distributed tracing.

---

### **🔟 [Cleanup & Cost Optimization](5.10-cleanup/)**  
Kiểm tra tài nguyên còn dùng, phân tích chi phí, backup databases, xóa API, EC2, Lambda, S3, CloudFront.

---

## ✔ Kết luận

Workshop này giúp bạn xây dựng **một hệ thống hoàn chỉnh, bảo mật, tối ưu chi phí** theo đúng chuẩn AWS Production.

Bạn đã sẵn sàng bắt đầu với **Module 5.1 — Introduction**.

---
