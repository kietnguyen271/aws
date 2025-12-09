---
title: "5.1 Introduction"
date: ""
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Module 1: Giới Thiệu Hạ Tầng Smoking Cessation Platform

## Mục tiêu Module

- Hiểu kiến trúc AWS toàn bộ hệ thống
- Nắm các thành phần chính của nền tảng
- Tìm hiểu flow dữ liệu giữa các service
- Chuẩn bị cho các module tiếp theo

---

## Kiến Trúc Hệ Thống Tổng Quát (Hybrid - EC2 + Lambda)

### Sơ đồ Kiến Trúc AWS

**Architecture Type**: Hybrid (EC2 + Serverless Lambda)
**Deployment Pattern**: EC2 for stateful services, Lambda for event-driven tasks

![AWS Hybrid Architecture Diagram](../assets/01-architecture-diagram.png)
[workshop\assets\architecture.png]

---

## Các Thành Phần Chính

### 1. Frontend Layer (React + Vite)

Hosting: S3 + CloudFront

Features:
- Responsive UI cho Web & Mobile
- Real-time Chat với WebSocket
- User Authentication (Cognito)
- Progress Tracking Dashboard
- Coach Management Interface

### 2. API Gateway Layer

REST API: /api/v1/* endpoints
WebSocket: /ws endpoints cho real-time chat

Responsibilities:
- Request routing
- Authentication validation
- Rate limiting
- CORS handling

### 3. Backend Layer (Hybrid: EC2 + Lambda)

EC2 Application Servers (Always-on):
- user-cessation service (Port 8000)
  - User profiles, progress tracking
  - Coaching session management
  - Statistics & analytics

- social-media service (Port 8000)
  - Social features
  - Notifications
  - Community management

Lambda Functions (Event-driven):
- File Upload Lambda
  - Handle image/file uploads to S3

- Payment Processing Lambda
  - Process payments & subscriptions

- Specific Trigger Functions
  - Webhooks
  - Scheduled tasks

### 4. WebSocket & Real-time Layer

NLB (Network Load Balancer):
- Handles persistent WebSocket connections
- Port 443 (HTTPS)
- Distributes real-time chat traffic
- Integration with EC2 backend servers

### 5. Database Layer (EC2-hosted)

PostgreSQL Server (DB-PG):
- User profiles & authentication
- Progress tracking data
- Coaching session records
- Relational data

MongoDB Server (DB-Mongo):
- Chat message history
- Social media content
- Message metadata
- Flexible schema data

### 6. Security Layer

- AWS Cognito: User authentication & authorization
- IAM Roles: Service-to-service permissions
- VPC: Network isolation (private subnets for databases)
- Security Groups: Firewall rules for EC2 & databases
- SSL/TLS: Data encryption in transit & at rest
- NLB Security Group: Restricts access to WebSocket port

### 7. Monitoring & Logging

- CloudWatch: Logs & Metrics for all services
- CloudTrail: API audit trail
- EC2 Instance Monitoring: CPU, memory, disk usage
- Database Monitoring: Query performance, connections
- Alarms: Performance & health alerts

---

## User Journeys & Data Flow

### Journey 1: User Registration & Login

```
1. User đến trang đăng ký
   ↓
2. Frontend gửi credentials → API Gateway → Lambda (Auth Service)
   ↓
3. Lambda validate & create user trong PostgreSQL (EC2)
   ↓
4. AWS Cognito tạo user account
   ↓
5. Lambda return access token & refresh token
   ↓
6. Frontend lưu token → có quyền truy cập API
```

### Journey 2: Real-time Chat

```
1. User A gửi message
   ↓
2. Message được gửi → API Gateway WebSocket endpoint
   ↓
3. Lambda (Chat Service) xử lý message
   ↓
4. Lưu message vào MongoDB (EC2)
   ↓
5. WebSocket broadcast message đến User B (connected)
   ↓
6. User B nhận message real-time
```

### Journey 3: Progress Tracking

```
1. User update progress data (e.g., smoke-free days)
   ↓
2. Frontend gửi → API Gateway → Lambda (User Service)
   ↓
3. Lambda validate & update PostgreSQL (EC2)
   ↓
4. Coach nhận notification (thông qua WebSocket)
   ↓
5. Dashboard cập nhật real-time
```

---

## Các Công Nghệ & Services

| Thành Phần | Service | Chi Tiết |
|-----------|---------|---------|
| Frontend Hosting | S3 + CloudFront | Static website hosting + CDN |
| Authentication | Cognito | User authentication & SSO |
| API Management | API Gateway | REST API routing |
| Compute (Always-on) | EC2 (t4g.small) | Main application servers |
| Compute (Event-driven) | Lambda | Specific functions (upload, payment) |
| Real-time | NLB + WebSocket | WebSocket connections & messaging |
| Database (SQL) | EC2 + PostgreSQL | User data, relational data |
| Database (NoSQL) | EC2 + MongoDB | Chat history, social data |
| Storage | S3 | File uploads & assets |
| Security | VPC, Security Groups, IAM | Network isolation & access control |
| Monitoring | CloudWatch | Logs, metrics, alarms |
| CDN | CloudFront | Content delivery network |

---

## Các Module Tiếp Theo

1. Module 2: Prerequisites - Chuẩn bị tài khoản & tools
2. Module 3: Setup Cognito - User authentication
3. Module 4: Setup Lambda - Backend functions
4. Module 5: Setup API Gateway - API endpoints
5. Module 6: Verify EC2 Servers & Databases - PostgreSQL + MongoDB on EC2
6. Module 7: Setup S3 + CloudFront - Frontend hosting
7. Module 8: Setup VPC & Security - Network security
8. Module 9: Monitoring & Logging - System observability
9. Module 10: Cleanup - Xóa resources & cost optimization

---

## Checklist

- [ ] Hiểu kiến trúc AWS tổng quát
- [ ] Nắm 6 thành phần chính
- [ ] Hiểu 3 user journeys chính
- [ ] Sẵn sàng cho Module 2

---

## Notes

- Hệ thống sử dụng Serverless Architecture - không cần quản lý servers
- Mọi service đều auto-scaling theo demand
- Pay-as-you-go pricing model
- Highly available & disaster recovery ready
