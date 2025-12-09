---
title: "5.5 Setup API Gateway"
date: ""
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# Module 5: Create API Gateway REST APIs

## Module Objectives

- Create 2 REST APIs from scratch  
- Create resources & methods  
- Integrate with Lambda functions  
- Configure authentication & authorization  
- Set up CORS & rate limiting  
- Deploy & test APIs  

**Duration**: 3–4 hours

---

## API Gateway Overview

Two REST APIs will be created:

| API Name | Region | Purpose | Resources | Methods |
|----------|--------|---------|-----------|---------|
| smoking-cessation-user-api | ap-southeast-1 | User management, Admin operations | /admin/coaches, /api/user-info, /upload | GET, POST, PUT, DELETE |
| smoking-cessation-chat-api | ap-southeast-1 | Chat & WebSocket | /chat/rooms, /chat/messages, /ws | GET, POST, WebSocket |

---

## Part 1: Create First REST API (User Management)

### Step 1: Go to API Gateway Console

1. Log in to AWS Console  
2. Search for "API Gateway"  
3. Click "API Gateway" service  
4. Select region: **ap-southeast-1**  
5. Click "Create API"  

![API Gateway Console](../assets/05-api-gateway-console.png)

### Step 2: Choose API Type

1. Select **REST API**  
2. Click **Build**

![Choose API Type](../assets/05-choose-api-type.png)

### Step 3: Configure API Details

1. **API name**: `smoking-cessation-user-api`  
2. **Description**: REST API for user management and admin operations  
3. **Endpoint type**: Regional  
4. Click **Create API**

⏳ Wait ~1–2 minutes for API creation.

![Create API](../assets/05-create-api.png)

### Step 4: View API Dashboard

After the API is created, you will see:

- Resources tree (currently only the root `/`)  
- Methods available for the root  
- Integration settings  

![API Created](../assets/05-api-created.png)

---

## Part 2: Create /admin Resource & /admin/coaches Sub-resource

### Step 1: Create /admin Resource

1. Click the root `/`  
2. Click **Create Resource**  
3. **Resource name**: `admin`  
4. **Resource path**: `/admin`  
5. ✅ Enable API Gateway CORS  
6. Click **Create Resource**

![Create Admin Resource](../assets/05-create-admin-resource.png)

### Step 2: Create /admin/coaches Sub-resource

1. Click the `/admin` resource  
2. Click **Create Resource**  
3. **Resource name**: `coaches`  
4. **Resource path**: `coaches` → becomes `/admin/coaches`  
5. ✅ Enable API Gateway CORS  
6. Click **Create Resource**

![Create Coaches Resource](../assets/05-create-coaches-resource.png)

### Step 3: Create GET Method for /admin/coaches

1. Click `/admin/coaches`  
2. Click **Create Method**  
3. Select **GET**  
4. Click the checkmark  

![Create GET Method](../assets/05-create-get-method.png)

### Step 4: Configure GET Method Integration

1. **Integration type**: Lambda Function  
2. **Lambda Region**: ap-southeast-1  
3. **Lambda Function**: `smoking-cessation-admin-manage-coaches`  
4. ✅ Use Lambda Proxy integration  
5. Click **Save**  
6. Confirm permission popup  

![Configure Lambda Integration](../assets/05-configure-lambda-integration.png)

### Step 5: Create POST Method for /admin/coaches

1. Click **Create Method**  
2. Select **POST**  
3. Same Lambda integration as GET  
4. Click **Save**

### Step 6: Create PUT Method for /admin/coaches

1. Click **Create Method**  
2. Select **PUT**  
3. Use same Lambda integration  
4. Click **Save**

### Step 7: Create DELETE Method for /admin/coaches

1. Click **Create Method**  
2. Select **DELETE**  
3. Same integration  
4. Click **Save**

**Result**: `/admin/coaches` now has GET, POST, PUT, DELETE methods.

---
## Part 3: Create /api/user-info Resource & Sub-resources

### Step 1: Create /api Resource

1. Click root "/"
2. Click "Create Resource"
3. **Resource name**: `api`
4. **Resource path**: `/api`
5. ✅ "Enable API Gateway CORS"
6. Click "Create Resource"

### Step 2: Create /api/user-info Resource

1. Click the `/api` resource
2. Click "Create Resource"
3. **Resource name**: `user-info`
4. **Resource path**: `user-info`
5. ✅ "Enable API Gateway CORS"
6. Click "Create Resource"

### Step 3: Create Methods for /api/user-info

Create GET, POST, PUT, DELETE methods (same as /admin/coaches):

1. For each method:
   - Click "Create Method"
   - Select method type
   - Integration: `smoking-cessation-admin-manage-coaches` Lambda
   - ✅ Lambda Proxy integration
   - Save

### Step 4: Create /{id} Resource (Path Parameter)

1. Click the `/api/user-info` resource
2. Click "Create Resource"
3. **Resource name**: `{id}`
4. **Resource path**: `{id}`
5. Click "Create Resource"

### Step 5: Create GET Method for /api/user-info/{id}

1. Click the `/{id}` resource
2. Click "Create Method"
3. Select "GET"
4. Integration: `smoking-cessation-admin-manage-coaches` Lambda
5. Save

### Step 6: Create /by-user-id Resource

1. Click the `/api/user-info` parent resource
2. Click "Create Resource"
3. **Resource name**: `by-user-id`
4. **Resource path**: `by-user-id`
5. Click "Create Resource"
6. Add GET method with Lambda integration

### Step 7: Create /empty Resource

1. Click the `/api/user-info` parent resource
2. Click "Create Resource"
3. **Resource name**: `empty`
4. **Resource path**: `empty`
5. Click "Create Resource"
6. Add GET method with Lambda integration

**Result**: /api/user-info resource tree created with all sub-resources

---

## Part 4: Create /upload Resource

### Step 1: Create /upload Resource

1. Click root "/"
2. Click "Create Resource"
3. **Resource name**: `upload`
4. **Resource path**: `/upload`
5. ✅ "Enable API Gateway CORS"
6. Click "Create Resource"

### Step 2: Create POST Method

1. Click the `/upload` resource
2. Click "Create Method"
3. Select "POST"
4. **Integration type**: Lambda Function
5. **Lambda Function**: `smoking-cessation-image-upload`
6. ✅ "Use Lambda Proxy integration"
7. Click "Save"

---

## Part 5: Configure Authentication & Authorization

### Step 1: Create Cognito Authorizer

1. Left menu: Click "Authorizers"
2. Click "Create Authorizer"
3. **Authorizer name**: `cognito-user-pool-authorizer`
4. **Type**: Cognito
5. **Cognito User Pool**: `smoking-cessation-users` (created in Module 3)
6. **Token source**: `Authorization`
7. Click "Create authorizer"

![Create Authorizer](../assets/05-create-authorizer.png)

### Step 2: Test Authorizer (Optional)

1. **Token**: enter a valid JWT token from your Cognito user pool
2. Click "Test authorizer"
3. Verify the response shows user claims

### Step 3: Add Authorization to Methods

For critical endpoints (e.g., /admin/coaches):

1. Click the `/admin/coaches` resource
2. Click "GET" method
3. Click "Method Request"
4. **Authorization**: Select `cognito-user-pool-authorizer`
5. Click the checkmark to save  
6. Repeat for POST, PUT, DELETE methods

![Add Authorization](../assets/05-add-authorization.png)

---

## Part 6: Setup Request Validators

### Step 1: Create Request Validator

1. Left menu: Click "Request Validators"
2. Click "Create"
3. **Name**: `validate-body-and-params`
4. ✅ "Validate request body"
5. ✅ "Validate query string parameters and headers"
6. Click "Create"

### Step 2: Apply Validator to POST Methods

1. Click `/admin/coaches` → "POST" method
2. Click "Method Request"
3. **Request Validator**: Select `validate-body-and-params`
4. Save
## Part 7: Setup CORS

### Step 1: Enable CORS for All Resources

1. Click root "/"
2. Click "Enable CORS"
3. Review default settings
4. Click "Enable CORS and replace existing CORS headers"

![Enable CORS](../assets/05-enable-cors.png)

### Step 2: Configure CORS Headers

CORS headers automatically configured:

- **Access-Control-Allow-Headers**: Content-Type, X-Amz-Date, Authorization, X-Api-Key, X-Amz-Security-Token
- **Access-Control-Allow-Methods**: GET, POST, PUT, DELETE, OPTIONS
- **Access-Control-Allow-Origin**: * (for dev, restrict in production)

---

## Part 8: Create Second API - Chat API (WebSocket)

### Step 1: Create WebSocket API

1. Click "Create API"
2. Select "WebSocket API"
3. Click "Build"

![Create WebSocket API](../assets/05-create-websocket.png)

### Step 2: Configure WebSocket API

1. **API name**: `smoking-cessation-chat-api`
2. **Description**: WebSocket API for chat functionality
3. **Route Selection Expression**: `$request.body.action`
4. Click "Create API"

⏳ **Wait for the API to be created**

### Step 3: Create Routes

Create default routes:

1. **$default** route:
   - Integration: Lambda Function
   - Function: `smoking-cessation-websocket-authorizer`

2. **$connect** route:
   - Integration: Lambda Function
   - Function: `smoking-cessation-websocket-authorizer`

3. **$disconnect** route:
   - Integration: Lambda Function
   - Function: `smoking-cessation-websocket-authorizer`

---

## Part 9: Deploy APIs

### Step 1: Create Deployment Stage

For User API:

1. Click "Deploy API"
2. **Stage name**: `prod`
3. **Stage description**: Production environment
4. Click "Deploy"

![Deploy API](../assets/05-deploy-api.png)

### Step 2: Note API Endpoint

After deployment, you will see:

- **Invoke URL**: `https://{api-id}.execute-api.ap-southeast-1.amazonaws.com/prod`

📌 Save this URL.

### Step 3: Deploy Chat API

1. Go to Chat API
2. Click "Deploy API"
3. **Stage name**: `prod`
4. Click "Deploy"
5. Note the WebSocket Invoke URL

---

## Part 10: Test REST API Endpoints

### Step 1: Test GET /admin/coaches

1. In the API Gateway console:
   - Select the User API
   - Click `/admin/coaches` → "GET"
   - Click "Test"

2. **Method**: GET  
3. **Path**: /admin/coaches  
4. **Headers**:
```
   Authorization: Bearer {your-cognito-token}
   Content-Type: application/json
   ```
5. **Body**:
   ```json
   {
     "name": "Coach Name",
     "email": "coach@example.com",
     "specialization": "Smoking Cessation"
   }
   ```
6. Click "Test"
7. **Expected**: Status 200 with created coach data

### Step 3: Test /upload Endpoint

1. Click `/upload` → "POST"
2. Click "Test"
3. **Method**: POST
4. **Headers**:
   ```
   Authorization: Bearer {your-cognito-token}
   Content-Type: multipart/form-data
   ```
5. Click "Test"
6. **Expected**: Status 200 with S3 URL

### Step 4: Test with cURL (Optional)

```bash
# Get list of coaches
curl -X GET \
  https://{api-id}.execute-api.ap-southeast-1.amazonaws.com/prod/admin/coaches \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json"

# Create new coach
curl -X POST \
  https://{api-id}.execute-api.ap-southeast-1.amazonaws.com/prod/admin/coaches \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Coach John",
    "email": "john@example.com",
    "specialization": "Smoking Cessation"
  }'
```

---
## Part 11: Setup CloudWatch Logging

### Step 1: Enable Full Request/Response Logging

1. Click "Settings" (left menu)
2. **CloudWatch log role ARN**:
   - Click "Edit"
   - Select or create an IAM role for API Gateway logging
   - Role should have `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` permissions
3. Click "Save changes"

### Step 2: Set Log Level

1. Go to "Stages" → "prod"
2. Click "Logs"
3. ✅ "Enable CloudWatch Logs"
4. **Log level**: INFO (or DEBUG for troubleshooting)
5. **Data trace enabled**: ✅
6. **Full request/response data**: ✅
7. Click "Save changes"

### Step 3: View Logs

1. Search for "CloudWatch"
2. Click "CloudWatch" service
3. Left menu: "Logs" → "Log groups"
4. Search for `API-Gateway-Execution-Logs_{api-id}`
5. View recent requests

![CloudWatch Logs](../assets/05-cloudwatch-logs.png)

---

## Part 12: Setup Rate Limiting (Usage Plans)

### Step 1: Create API Key

1. Left menu: Click "API Keys"
2. Click "Create API Key"
3. **Name**: `mobile-app-key`
4. **Description**: API key for mobile app
5. Click "Create API Key"
6. Copy and save the key

### Step 2: Create Usage Plan

1. Left menu: Click "Usage Plans"
2. Click "Create"
3. **Name**: `free-tier-plan`
4. **Description**: Free tier plan with rate limiting
5. Click "Next"

### Step 3: Configure Throttling & Quota

1. **Rate**: 100 requests per second
2. **Burst**: 200 requests
3. **Quota**: 1,000,000 requests per month
4. Click "Next"

### Step 4: Associate API to Plan

1. **Associated API Stages**:
   - Select User API
   - Select stage: `prod`
2. Click "Next"

### Step 5: Associate API Key to Plan

1. **API Keys**:
   - Search and select `mobile-app-key`
2. Click "Finish"

---

## Part 13: Setup Resource-Based Policy (Optional)

To restrict API access to specific IAM users/roles:

### Step 1: Add Resource Policy

1. Left menu: Click "Resource Policy"
2. Click "Edit"
3. Paste policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "execute-api:Invoke",
      "Resource": "execute-api:/*"
    }
  ]
}
```
4. Click "Save"
---

## Part 14: Enable API Caching (Optional)

### Step 1: Enable Cache for GET Endpoints

1. Go to stage: "prod"
2. Click "Settings"
3. **Cache cluster enabled**: ✅
4. **Cache cluster size**: 0.5 (smallest)
5. Click "Save changes"

### Step 2: Configure Cache per Method

1. Click `/admin/coaches` → "GET"
2. Click "Integration Response"
3. Expand the "200" response
4. **Cache settings**: ✅ "Enable method caching"
5. **Cache time to live (TTL)**: 300 seconds (5 minutes)
6. Click the checkmark

---

## Environment Variables Summary

Save these URLs after API deployment:

```env
# User Management API
USER_API_ENDPOINT=https://{api-id-1}.execute-api.ap-southeast-1.amazonaws.com/prod
USER_API_ID={api-id-1}

# Chat API (WebSocket)
CHAT_API_ENDPOINT=wss://{api-id-2}.execute-api.ap-southeast-1.amazonaws.com/prod
CHAT_API_ID={api-id-2}

# Authorizer
COGNITO_AUTHORIZER_ID=cognito-user-pool-authorizer

# Rate Limiting
API_KEY={your-api-key}
```

---

## Checklist

- [ ] User API (smoking-cessation-user-api) created
- [ ] /admin/coaches resource with GET, POST, PUT, DELETE methods created
- [ ] /api/user-info resource with sub-resources created
- [ ] /upload resource with POST method created
- [ ] Chat API (smoking-cessation-chat-api) created
- [ ] Cognito authorizer configured
- [ ] CORS enabled for all resources
- [ ] Request validators configured
- [ ] Both APIs deployed to `prod` stage
- [ ] CloudWatch logging enabled
- [ ] Rate limiting with Usage Plans configured
- [ ] All endpoints tested successfully
- [ ] API endpoints saved to environment variables
- [ ] Sẵn sàng cho Module 6 (Create EC2 Instances & Databases)

---

## Troubleshooting

### CORS Errors in Browser

**Issue**: "Access to XMLHttpRequest blocked by CORS policy"

**Solution**:
1. Verify CORS is enabled for resource
2. Check Access-Control-Allow-Origin header
3. For development, use `*` wildcard
4. For production, restrict to specific domain

### 403 Unauthorized Errors

**Issue**: "User is not authorized to perform: execute-api:Invoke"

**Solution**:
1. Verify JWT token is valid
2. Check authorizer is configured on method
3. Verify user has required role in Cognito
4. Review API resource policy

### Lambda Function Not Found

**Issue**: "Invalid Lambda function ARN specified"

**Solution**:
1. Verify Lambda function exists in same region
2. Check Lambda function name spelling
3. Verify IAM role has Lambda invoke permissions
4. Check CloudWatch logs for integration errors

### Rate Limiting Not Working

**Issue**: "Requests not throttled according to plan"

**Solution**:
1. Verify API key is passed in request
2. Check Usage Plan is associated with stage
3. Verify API key is associated with Usage Plan
4. Wait for throttling to take effect (may take a few minutes)

### High Latency

**Issue**: "API requests taking too long"

**Solution**:
1. Enable caching for GET requests
2. Increase Lambda memory (Module 4)
3. Check database connection from Lambda (Module 6)
4. Monitor CloudWatch metrics

---

## Next Steps

1. Implement API Gateway request/response transformations (optional)
2. Setup WAF (Web Application Firewall) for security (optional)
3. Configure CloudFront for API caching (Module 7)
4. Setup API documentation (optional)
5. Test end-to-end with frontend application

---
## Achieved Results

After Module 5, you will have:

1. ✅ 2 REST APIs created (User API & Chat API)
2. ✅ All resources & methods configured
3. ✅ Lambda integrations setup
4. ✅ Cognito authorization configured
5. ✅ CORS enabled for all endpoints
6. ✅ Request validators configured
7. ✅ Both APIs deployed to `prod` stage
8. ✅ CloudWatch logging enabled
9. ✅ Rate limiting configured
10. ✅ All endpoints tested & verified
11. ✅ API endpoints documented
12. ✅ Ready for Module 6 (Create EC2 Instances & Databases)
