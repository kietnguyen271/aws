---
title: "5.2 Prerequisites"
date: ""
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

# Module 2: Prerequisites - Preparing Account & Tools

## Module Objectives

- Create & configure AWS account
- Set up IAM roles & policies
- Verify required access permissions
- Set up cost monitoring & alerts

---

## Part 1: AWS Account Setup

### Step 1: Create AWS Account

If you do not have an AWS account:

1. Access https://aws.amazon.com/
2. Click "Create an AWS Account"
3. Enter email, password, account name
4. Choose Support Plan (Free tier available)
5. Verify email & set up billing information

![AWS Account Creation Page](../assets/02-aws-account-creation.png)

### Step 2: Log in to AWS Console

1. Access https://console.aws.amazon.com/
2. Enter root account email & password
3. Verify MFA (Multi-Factor Authentication) if required

**Note**: It is recommended to enable MFA for the root account for security

---

## Part 2: IAM Setup - Create Admin User

### Step 1: Access IAM Dashboard

1. From AWS Console, search for "IAM"
2. Click "IAM" from the services list
3. Click "Users" in the navigation menu

![IAM Users Page](../assets/02-iam-users-page.png)

### Step 2: Create IAM User for Workshop

1. Click "Create user"
2. Enter User name: "workshop-admin"
3. Check "Provide user access to the AWS Management Console - optional"
4. Check "Users must create a new password at next sign-in - Recommended"
5. Click "Next"

![Create User Dialog](../assets/02-create-iam-user-dialog.png)

### Step 3: Assign Permissions

1. Select "Attach policies directly"
2. Search and check the following policies:
   - AdministratorAccess (allows all services)

3. Click "Next"

![Permissions Page](../assets/02-iam-permissions-selection.png)

### Step 4: Review & Create

1. Review the user information
2. Click "Create user"
3. Download the ".csv" file containing credentials

![User Created Confirmation](../assets/02-iam-user-created.png)

### Step 5: Login using IAM User

1. Copy the User sign-in link from the confirmation screen
2. Open the link in a new browser
3. Login using user name & password

![IAM User Sign-in](../assets/02-iam-user-signin.png)

---

## Part 3: Verify Permissions

### Verify access permissions for AWS Services

1. Login to AWS Console using IAM user
2. Access each service to verify permissions:
   - Cognito: https://console.aws.amazon.com/cognito/
   - Lambda: https://console.aws.amazon.com/lambda/
   - EC2: https://console.aws.amazon.com/ec2/
   - API Gateway: https://console.aws.amazon.com/apigateway/
   - S3: https://console.aws.amazon.com/s3/
   - VPC: https://console.aws.amazon.com/vpc/

**Verification**: You should see "Create application" (not an error message)

![Permission Verification](../assets/02-permissions-verification.png)

---

## Part 4: Resources Naming Convention

To manage resources easily, use the following naming convention:

{project-name}-{service}-{environment}

Examples:
- smoking-cessation-cognito-dev
- smoking-cessation-lambda-auth-dev
- smoking-cessation-db-pg-dev (PostgreSQL on EC2)
- smoking-cessation-db-mongo-dev (MongoDB on EC2)
- smoking-cessation-api-dev
- smoking-cessation-frontend-dev
- smoking-cessation-vpc-dev

**Benefit**: Easy to search & manage resources in the console

## Troubleshooting

### Cannot create IAM User

- Check: Are you logged in using the root account or another IAM user?
- Solution: Log in again using the root account to create an IAM user

### Permission Denied errors

- Verify: IAM policies attached to the user
- Check: Do the policies include the service you are using?
- Contact AWS support if elevated permissions are needed

---

## Notes

- From now on, all actions use the IAM user, not the root account
- Each service will have a specific IAM role (created in later modules)
- Free Tier provides sufficient resources for learning

---

## Achieved Results

After Module 2, you will have:

1. An activated AWS account  
2. IAM user "workshop-admin" with admin permissions  
3. Access to all required AWS services  
4. Ready for Module 3 (Setup Cognito)
