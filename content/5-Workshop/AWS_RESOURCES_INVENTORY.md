# AWS Resources Inventory - Smoking Cessation Platform

Cập nhật: 2025-12-03

## Architecture Type: HYBRID (EC2 + Lambda + Managed Services)

This platform uses a hybrid architecture combining:
- Always-on EC2 servers for main applications
- Serverless Lambda for event-driven tasks
- Managed services for CDN, authentication, load balancing

---

## Account Information

Account ID: 140570829989
User: AdminUser (IAM)
Regions: us-east-1 (Cognito), ap-southeast-1 (main infrastructure)

---

## S3 Buckets

| Bucket Name | Region | Created | Purpose |
|------------|--------|---------|---------|
| leaflungs-frontend-new | us-east-1 | 2025-11-24 | Frontend hosting |
| leaflungs-images | ap-southeast-1 | 2025-11-25 | Chat/message images |
| leaflungs-images-sg | ap-southeast-1 | 2025-11-30 | Image storage |

---

## Cognito

User Pool:
- ID: us-east-1_dskUsnKt3
- Name: leaflungs-user-pool
- Region: us-east-1
- Created: 2025-11-24
- Last Modified: 2025-11-25

---

## EC2 Instances (Hybrid Architecture)

### ap-southeast-1 (4 instances - t4g.small)

Database Tier (leaflungs-db-sg):
| Instance ID | Name | IP Address | Type | Status | Created |
|------------|------|------------|------|--------|---------|
| i-0d82a626b99a2fecd | DB-PG | 172.0.8.55 | t4g.small | running | 2025-11-30 |
| i-0374ff6972fd306fe | DB-Mongo | 172.0.8.124 | t4g.small | running | 2025-12-02 |

Application Tier (leaflungs-backend-sg):
| Instance ID | Name | IP Address | Type | Status | Created |
|------------|------|------------|------|--------|---------|
| i-01dd1a4b2b8b4a41f | user-cessation | 172.0.3.240 | t4g.small | running | 2025-11-30 |
| i-059fae7766eb52ae3 | social-media | 172.0.3.236 | t4g.small | running | 2025-12-02 |

---

## Lambda Functions (Event-driven)

### us-east-1 (1 function)

| Function Name | Runtime | Last Modified | Role |
|--------------|---------|----------------|------|
| CognitoPostConfirmationTrigger | nodejs20.x | 2025-11-24 | CognitoPostConfirmationLambdaRole |

### ap-southeast-1 (4 functions)

| Function Name | Runtime | Last Modified | Role | Purpose |
|--------------|---------|----------------|------|---------|
| AdminManageCoachesFunction | nodejs20.x | 2025-12-01 | AdminOperationsLambdaRole | Admin operations |
| PaymentFunction | nodejs24.x | 2025-11-30 | ? | Payment processing |
| leaflungs-websocket-authorizer | nodejs20.x | 2025-12-02 | ? | WebSocket auth |
| image-upload-lambda | nodejs20.x | 2025-11-30 | ImageUploadLambdaRole | File uploads |

---

## API Gateways (ap-southeast-1)

| API Name | ID | Created | Type |
|----------|-----|---------|------|
| LeafLungs-UserInfo-API | v7agf76rrh | 2025-11-24 | REST API |
| leaflungs-chat-api | vuds39de1b | 2025-12-02 | REST API + WebSocket |

---

## Load Balancers (ap-southeast-1)

| Name | Type | Status | Purpose |
|------|------|--------|---------|
| leaflungs-userinfo-nlb | Network | active | WebSocket chat endpoint |

---

## VPC & Networking (ap-southeast-1)

### VPC
- VPC ID: vpc-046dc916dde2fb93f
- Name: project-bundau-milo
- CIDR: 172.0.0.0/18

### Security Groups
| Group ID | Name | Purpose |
|----------|------|---------|
| sg-012293e2687464913 | launch-wizard-1 | Default |
| sg-0cf34158cb7f5440b | leaflungs-backend-sg | Backend Lambda |
| sg-0adb5d7ea1fe0f6bb | leaflungs-nlb-sg | NLB (WebSocket) |
| sg-027ef04aa3c769ecf | leaflungs-db-sg | EC2 Database Instances |
| sg-0d2dbd7d32700d8c8 | default | Default |

### EC2 Database Servers
Status: DEPLOYED

| Instance ID | Name | Type | IP | Database | Status |
|-------------|------|------|----|---------|----|
| i-01dd1a4b2b8b4a41f | DB-PG | t4g.small | 172.0.8.55 | PostgreSQL | Running |
| i-012ab3c4d5e6f7g8h0 | DB-Mongo | t4g.small | 172.0.8.124 | MongoDB | Running |

---

## CloudFront

| Distribution ID | Domain | Status |
|------------------|--------|--------|
| E1NREZDKTJH6Y9 | d2yo2hr161ib8h.cloudfront.net | Deployed |

---

## IAM Roles

| Role Name | Created |
|-----------|---------|
| AdminOperationsLambdaRole | 2025-12-01 |
| AWSServiceRoleForAPIGateway | 2025-11-23 |
| CognitoPostConfirmationLambdaRole | 2025-11-24 |
| ImageUploadLambdaRole | 2025-11-25 |

---

## Environment Variables Status

From .env file:

```
COGNITO_USER_POOL_ID=us-east-1_dskUsnKt3 ✓ EXISTS
COGNITO_CLIENT_ID=4175kqc33olfjinhkll4jme379 ✓ EXISTS
COGNITO_REGION=us-east-1 ✓

S3_BUCKET_NAME=leaflungs-frontend-new ✓ EXISTS
CLOUDFRONT_DISTRIBUTION_ID=E1NREZDKTJH6Y9 ✓ EXISTS
CLOUDFRONT_DOMAIN=d2yo2hr161ib8h.cloudfront.net ✓

VITE_API_GATEWAY_URL=https://v7agf76rrh.execute-api.ap-southeast-1.amazonaws.com/prod ✓ EXISTS
VITE_CHAT_API_URL=https://vuds39de1b.execute-api.ap-southeast-1.amazonaws.com/prod ✓ EXISTS
VITE_CHAT_WS_URL=https://leaflungs-userinfo-nlb-3c1d58c7a3d41477.elb.ap-southeast-1.amazonaws.com/ws ✓ EXISTS
```

---

## Outstanding Items Needed

1. EC2 Database configuration verification - DEPLOYED
2. Verify PaymentFunction Lambda role - UNKNOWN
3. Verify leaflungs-websocket-authorizer Lambda role - UNKNOWN
4. Verify API Gateway resources & methods
5. Verify WebSocket integration
6. Verify Cognito client configuration
7. Database schema & migration scripts - CHECK IF EXISTS

---

## Next Steps

1. Module 3: Verify Cognito configuration
2. Module 4: Verify Lambda functions & roles
3. Module 5: Verify API Gateway setup
4. Module 6: Verify EC2 Database Servers (PostgreSQL + MongoDB)
5. Module 7: Verify S3 & CloudFront
6. Module 8: Verify VPC & Security Groups
7. Module 9: Setup Monitoring (CloudWatch, CloudTrail)
8. Module 10: Cost optimization & cleanup

---

## Notes

- Mixed regions: Cognito in us-east-1, most services in ap-southeast-1
- WebSocket via NLB (not API Gateway WebSocket)
- All major resources already created - workshop will focus on verification & documentation
