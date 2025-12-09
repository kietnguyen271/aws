---
title: "Cleanup & Cost Optimization"
date: ""
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

# Module 10: Cleanup & Cost Optimization

## Mục tiêu Module

- Identify unused/underutilized resources
- Delete test resources
- Optimize costs
- Archive important data
- Backup before deletion
- Documentation & lessons learned

---

## Phần 1: Resource Inventory & Assessment

### Current AWS Resources

List tất cả resources từ AWS Console:

**Lambda Functions:**
- Go to Lambda console
- List functions in each region (us-east-1, ap-southeast-1)
- Note: Function names, creation dates, memory allocation

**API Gateways:**
- Go to API Gateway console
- List all REST APIs in ap-southeast-1
- Note: API names, deployment status

**EC2 Database Instances:**
- Go to EC2 console
- List all instances in ap-southeast-1
- Filter for database instances (DB-PG, DB-Mongo)
- Note: Instance IDs, instance types, IPs, creation dates

**S3 Buckets:**
- Go to S3 console
- List all buckets
- Note: Bucket names, creation dates, storage used

**CloudFront Distributions:**
- Go to CloudFront console
- List all distributions
- Note: Domain names, distribution IDs

**VPC Resources:**
- Go to VPC console
- List all VPCs in ap-southeast-1
- Note: VPC IDs, CIDR ranges

Create inventory spreadsheet:
- Resource name & type
- Region
- Creation date
- Current usage
- Estimated monthly cost

---

## Phần 2: Identify Unused Resources

### Check Resource Usage

#### Lambda Functions

To check Lambda invocations:

1. Go to CloudWatch console
2. Click "Metrics" → "Lambda"
3. Select each function
4. Choose metric: "Invocations"
5. Set time range: Last 7 days
6. Check if Sum = 0 (unused function)

If no invocations in last 7 days = unused function (consider deletion)

#### API Gateways

To check API request count:

1. Go to CloudWatch console
2. Click "Metrics" → "API Gateway"
3. Select your API
4. Choose metric: "Count"
5. Set time range: Last 30 days
6. Check if Sum = 0 (unused API)

#### S3 Buckets

To check bucket sizes:

1. Go to S3 console
2. Click on each bucket
3. Go to "Storage" tab
4. See "Storage used" value
5. Review storage class (Standard, Infrequent Access, Glacier)
6. Consider moving old objects to cheaper storage classes

S3 storage class pricing:
- Standard: $0.023/GB/month
- Infrequent Access: $0.0125/GB/month
- Glacier: $0.004/GB/month

#### CloudFront

To check cache hit ratio:

1. Go to CloudWatch console
2. Click "Metrics" → "CloudFront"
3. Select your distribution
4. Choose metric: "CacheHitRate"
5. Set time range: Last 30 days
6. If cache hit ratio < 50%, may need optimization

#### EC2 Database Instances

To check CPU & memory usage:

1. Go to CloudWatch console
2. Click "Metrics" → "EC2"
3. Select your database instance (DB-PG or DB-Mongo)
4. Choose metric: "CPUUtilization"
5. Set time range: Last 30 days
6. Check Average and Maximum values

If CPU < 10% consistently: May downsize instance type (saves money on EC2)

---

## Phần 3: Cost Optimization Strategies

### 1. Right-Sizing EC2 Database Instances

Current: t4g.small EC2 instances (PostgreSQL + MongoDB)
Cost: ~$30-40/month per instance

Options:
- t4g.nano: ~$3-5/month (for test/dev)
- t4g.micro: ~$8-10/month (for light production)
- t4g.small: Keep as-is (recommended for production)

Check: EC2 CPU < 10% on average → downsize to save money

### 2. Reserve Capacity (if stable usage)

For production, consider AWS EC2 Reserved Instances:
- 1-year: ~30% discount
- 3-year: ~50% discount

To purchase reserved EC2 instances:

1. Go to EC2 console
2. Click "Reserved Instances" (left menu)
3. Click "Purchase Reserved Instances"
4. Configure:
   - Instance type: t4g.small
   - Term length: 1-year or 3-year
5. Click "Purchase"

### 3. Optimize Data Transfer

Costs:
- S3 to CloudFront: Free
- S3 to Internet: $0.09/GB
- S3 to Lambda (same region): Free

Recommendation: Keep CloudFront caching enabled

### 4. Lambda Optimization

Current: 256 MB average
Cost: ~$0.0000002 per invocation

If high invocation rate:
- Use Lambda provisioned concurrency: Higher cost but predictable
- Monitor: Check if memory increase helps reduce duration (saves cost)

### 5. Database Optimization

Recommendations:
- Enable automated backup (already done)
- Setup read replicas if scaling reads (cost: ~50% of main instance)
- Archive old logs (CloudWatch retention: 30 days by default)

### 6. CloudFront Optimization

If cache hit ratio < 50%:
1. Increase TTL for static assets
2. Add more cache behaviors
3. Use cache policies with parameters

---

## Phần 4: Backup & Archive

### Bước 1: Backup EC2 Databases

To backup PostgreSQL and MongoDB on EC2:

**PostgreSQL (DB-PG: 172.0.8.55):**

1. SSH into the EC2 instance
2. Create backup: `pg_dump smokingcessation > backup_$(date +%Y%m%d).sql`
3. Compress: `gzip backup_*.sql`

**MongoDB (DB-Mongo: 172.0.8.124):**

1. SSH into the EC2 instance
2. Create backup: `mongodump --db smokingcessation --out backup_$(date +%Y%m%d)`
3. Compress: `tar czf backup_*.tar backup_*`

### Bước 2: Upload Database Backups to S3

To export database backups for archival:

1. SSH into database EC2 instance
2. Upload to S3:
   - Via AWS CLI (if configured): `aws s3 sync /backups s3://smoking-cessation-backups/`
   - Or manually download and upload via S3 console

### Bước 3: Archive S3 Data

For old data (> 90 days), setup lifecycle policies:

1. Go to S3 console
2. Click on bucket name (e.g., "leaflungs-images")
3. Go to "Management" tab
4. Click "Create lifecycle rule"
5. Configure:
   - Name: "archive-old-data"
   - Filter: Prefix "chat/"
   - Transitions: Move to Glacier after 90 days
   - Expiration: Delete after 365 days
6. Click "Create rule"

---

## Phần 5: Test Resource Cleanup (Non-Production)

### Bước 1: Delete Test Lambda Functions

If any test functions exist:

1. Go to Lambda console
2. Click on test function name
3. Click "Actions" → "Delete"
4. Type function name to confirm
5. Click "Delete"

### Bước 2: Delete Unused API Gateway

If multiple APIs for testing:

1. Go to API Gateway console
2. Click on API name
3. Click "Actions" → "Delete API"
4. Confirm deletion

### Bước 3: Delete Empty S3 Buckets

To delete a bucket:

1. Go to S3 console
2. Click on bucket name
3. Click "Empty" to remove all objects
4. Confirm emptying
5. Once empty, click "Delete" button
6. Type bucket name to confirm
7. Click "Delete bucket"

### Bước 4: Delete Unused CloudFront Distributions

To delete a CloudFront distribution:

1. Go to CloudFront console
2. Click on distribution
3. Click "Disable" (if enabled)
4. Wait 15 minutes for propagation
5. Once status shows "Disabled", click "Delete"
6. Confirm deletion

---

## Phần 6: Delete Production Resources (if shutting down)

WARNING: This is destructive and cannot be undone!

### Backup Checklist Before Deletion

- [ ] PostgreSQL data backed up (pg_dump)
- [ ] MongoDB data backed up (mongodump)
- [ ] Database backups uploaded to S3
- [ ] S3 data backed up externally (if needed)
- [ ] CloudTrail logs reviewed & archived
- [ ] Code backed up to GitHub
- [ ] DNS records updated (if redirecting)
- [ ] Team notified

### Bước 1: Delete EC2 Database Instances

To delete database EC2 instances:

1. Go to EC2 console
2. Click "Instances"
3. Select database instance (DB-PG or DB-Mongo)
4. Click "Instance State" → "Terminate"
5. Confirm termination
6. Wait for instance to terminate
7. Verify EBS volumes are deleted (optional: create snapshots first if needed)
8. Repeat for second database instance

### Bước 2: Delete Lambda Functions

To delete each Lambda function:

1. Go to Lambda console
2. Click on function name
3. Click "Actions" → "Delete"
4. Type function name to confirm
5. Click "Delete"
6. Repeat for each function

### Bước 3: Delete API Gateways

To delete each API Gateway:

1. Go to API Gateway console
2. Click on API name (e.g., "LeafLungs-UserInfo-API")
3. Click "Actions" → "Delete API"
4. Confirm deletion
5. Repeat for second API (leaflungs-chat-api)

### Bước 4: Delete Cognito User Pool

To delete Cognito User Pool:

1. Go to Cognito console
2. Click "User pools"
3. Click on your user pool
4. Click "Delete user pool" (bottom right)
5. Type the user pool name to confirm
6. Click "Delete"

### Bước 5: Delete S3 Buckets (and contents)

To delete each S3 bucket:

1. Go to S3 console
2. For each bucket (leaflungs-frontend-new, leaflungs-images, leaflungs-images-sg):
   - Click on bucket name
   - Click "Empty" to remove all objects
   - Confirm emptying
   - Once empty, click "Delete" button
   - Type bucket name to confirm
   - Click "Delete bucket"

### Bước 6: Delete CloudFront Distribution

To delete CloudFront distribution:

1. Go to CloudFront console
2. Click on distribution (if not already disabled)
3. If enabled, click "Disable" first
4. Wait 15 minutes for propagation
5. Once status shows "Disabled", click "Delete"
6. Confirm deletion

---

## Phần 7: Cost Analysis & Reporting

### Monthly Cost Breakdown

Typical costs for this architecture:

| Service | Usage | Cost |
|---------|-------|------|
| Lambda | 100K invocations/month | ~$2 |
| API Gateway | 10M requests/month | ~$50 |
| EC2 (2x DB instances) | 2 x t4g.small for PostgreSQL + MongoDB | ~$60-70 |
| EC2 (2x App instances) | 2 x t4g.small for applications | ~$60-70 |
| S3 | 100 GB storage | ~$2 |
| CloudFront | 1 TB/month transfer | ~$85 |
| Cognito | 1K users | ~$0 (free tier) |
| NAT Gateway | 10 GB transfer | ~$5 |
| **Total** | | ~**$300-350/month** |

Cost optimization opportunities:
1. Use t4g.nano/micro for light databases: Saves ~$20-30/month
2. Cache more aggressively: Saves ~$30/month
3. Reserved EC2 instances: Saves ~$60-80/month
4. Total savings: ~$110-140/month (35-40% reduction)

### AWS Cost Explorer

1. AWS Console → Billing → Cost Explorer
2. View costs by:
   - Service
   - Linked account
   - Region
   - Usage type
3. Set up cost anomaly detection
4. Review trends

---

## Phần 8: Documentation & Lessons Learned

### Create Post-Mortem Document

```markdown
# Workshop Completion Report

## Architecture Summary
- Services deployed: Lambda, API Gateway, EC2 (PostgreSQL + MongoDB), S3, CloudFront, Cognito, NLB
- Regions: us-east-1 (Cognito), ap-southeast-1 (main)
- Total users: 1000+
- Monthly costs: $300-350

## Key Learnings

1. Regional considerations
   - Cognito must be in us-east-1 for CloudFront
   - Main services in ap-southeast-1 for latency

2. Security best practices implemented
   - VPC isolation
   - Security group restrictions
   - IAM least-privilege
   - Secrets Manager for credentials

3. Cost optimization
   - CloudFront caching important
   - EC2 instances can be right-sized or use Reserved Instances
   - Reserved instances save 30-50%

4. Monitoring critical
   - CloudWatch alarms prevented many issues
   - X-Ray helped debug performance

## Recommendations for Next Phase

1. Setup automated backups for EC2 databases (pg_dump/mongodump cronjobs)
2. Implement CI/CD for automated deployments
3. Add comprehensive test suite
4. Setup on-call rotation & runbooks
5. Quarterly cost reviews
```

---

## Checklist - Cleanup Decision

Before final cleanup, verify:

- [ ] All data backed up
- [ ] Snapshots created
- [ ] CloudTrail logs archived
- [ ] Code pushed to GitHub
- [ ] Cost analysis completed
- [ ] Team trained on infrastructure
- [ ] Documentation complete
- [ ] Stakeholders approved
- [ ] DNS cutover plan (if applicable)
- [ ] Rollback plan documented

---

## Final Recommendations

1. Keep infrastructure running (you've built it correctly)
2. Implement automated scaling if needed
3. Setup CI/CD pipeline for updates
4. Add comprehensive testing
5. Plan quarterly cost reviews
6. Train team on operational procedures
7. Consider disaster recovery plan

---

## Summary & Next Steps

Congratulations! You've successfully:

1. ✓ Verified/setup Cognito authentication
2. ✓ Verified/setup Lambda functions
3. ✓ Verified/setup API Gateways
4. ✓ Verified EC2 databases (PostgreSQL + MongoDB)
5. ✓ Verified S3 & CloudFront
6. ✓ Verified VPC & Security
7. ✓ Implemented monitoring & logging
8. ✓ Optimized costs

This infrastructure can support:
- 1000+ concurrent users
- 100K+ requests/day
- Highly available (multi-AZ capable)
- Scalable (auto-scale Lambda, upgrade EC2 instance types)
- Secure (VPC isolation, encryption, IAM)
- Observable (comprehensive logging & monitoring)

---

## Kết Quả Đạt Được

Sau Module 10:

1. Resource inventory & usage analyzed
2. Cost optimization strategies identified
3. Unused resources identified for cleanup
4. Backup procedures implemented
5. Cost reduction plan created (up to 36% savings possible)
6. Post-mortem & lessons learned documented
7. Recommendations for next phase
8. Workshop complete & infrastructure production-ready
