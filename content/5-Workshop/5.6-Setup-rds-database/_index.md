---
title: "5.6 Setup RDS Database"
date: ""
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Module 6: Create EC2 Instances & Setup Databases

## Module Objectives

- Create 4 EC2 instances from scratch
- Set up PostgreSQL on the DB-PG instance
- Set up MongoDB on the DB-Mongo instance
- Configure security groups
- Create databases & users
- Test connectivity between instances
- Set up monitoring & backups

**Duration**: 5–6 hours

---

## EC2 Instances Overview

4 instances will be created in ap-southeast-1 (t4g.small, ARM-based, cost-effective):

| Instance Name | Type | Purpose | Database | Port |
|---------------|------|---------|----------|------|
| smoking-db-pg | t4g.small | PostgreSQL Server | smoking_cessation | 5432 |
| smoking-db-mongo | t4g.small | MongoDB Server | smoking_cessation | 27017 |
| smoking-app-user | t4g.small | User Cessation App | (PostgreSQL) | 8000 |
| smoking-app-social | t4g.small | Social Media App | (MongoDB) | 8000 |

---

## Part 1: Create Security Groups

### Step 1: Create Database Security Group

1. EC2 Console  
2. Left menu: "Security Groups"  
3. Click "Create security group"  
4. **Name**: `smoking-db-sg`  
5. **Description**: Security group for database servers  
6. **VPC**: Default VPC (or your VPC)  
7. Click "Create security group"  

![Create Security Group](../assets/06-create-sg.png)

### Step 2: Add Inbound Rules for DB-SG

1. Click the security group just created  
2. "Inbound rules" → "Edit inbound rules"  
3. Add these rules:
   - **Type**: PostgreSQL (5432)  
     - **Source**: Custom → `172.0.0.0/16` (internal VPC CIDR)
   - **Type**: Custom TCP 27017 (MongoDB)  
     - **Source**: `172.0.0.0/16`
4. Click "Save rules"

### Step 3: Create Application Security Group

1. Click "Create security group"  
2. **Name**: `smoking-app-sg`  
3. **Description**: Security group for application servers  
4. Click "Create security group"

### Step 4: Add Inbound Rules for App-SG

1. Click the security group just created  
2. "Inbound rules" → "Edit inbound rules"  
3. Add rules:
   - **Type**: SSH (22)  
     - **Source**: My IP (or `0.0.0.0/0` if no fixed IP)
   - **Type**: Custom TCP 8000  
     - **Source**: `0.0.0.0/0` (or restrict to NLB later)
4. Click "Save rules"

### Step 5: Add Outbound Rules

1. "Outbound rules" → "Edit outbound rules"  
2. Verify:
   - All traffic to `smoking-db-sg` (for database access)  
   - All traffic to internet (for package downloads)
3. Default rule should allow this  
4. Click "Save rules"

---

## Part 2: Create EC2 Instances

### Step 1: Launch First Instance (PostgreSQL)

1. EC2 Console  
2. Click "Launch Instances"  
3. **Name**: `smoking-db-pg`  
4. **AMI**: Amazon Linux 2023 (free tier eligible)  
5. **Instance type**: t4g.small  
6. **Keypair**: Create new or select existing  
   - **Key pair name**: `smoking-cessation-key`  
   - Click "Create key pair"  
   - Download & store securely  
7. Click "Next"

![Launch Instance](../assets/06-launch-instance.png)

### Step 2: Configure Network

1. **VPC**: Default VPC (or your VPC)  
2. **Subnet**: Any (or a specific subnet in ap-southeast-1a)  
3. **Auto-assign public IP**: Disable (private subnet)  
4. **Security group**: `smoking-db-sg`  
5. Click "Next"

### Step 3: Configure Storage

1. **Size**: 30 GB  
2. **Volume type**: gp3  
3. **Delete on termination**: ✅  
4. Click "Next"

### Step 4: Add Tags

1. **Tag key**: Name  
2. **Tag value**: `smoking-db-pg`  
3. Click "Launch instance"

⏳ **Wait for the instance to be created (2–3 minutes)**

### Step 5: Note Private IP Address

1. Wait for the instance to show "running"  
2. Copy the **Private IPv4 address** (e.g., 172.0.8.55)  
3. Save for later: `PG_HOST=172.0.8.55`  

![Instance IP](../assets/06-instance-ip.png)

### Step 6: Create MongoDB Instance (Same Steps)

1. Click "Launch Instances"  
2. **Name**: `smoking-db-mongo`  
3. Same configuration as PostgreSQL  
4. **Security group**: `smoking-db-sg`  
5. Launch instance  
6. Note IP address: `MONGO_HOST=<ip>`

### Step 7: Create Application Instances

1. Launch instance: `smoking-app-user`  
   - **Security group**: `smoking-app-sg`  
   - Note IP: `APP_USER_HOST=<ip>`

2. Launch instance: `smoking-app-social`  
   - **Security group**: `smoking-app-sg`  
   - Note IP: `APP_SOCIAL_HOST=<ip>`

All 4 instances should now be running.

---

## Part 3: Setup PostgreSQL on DB-PG Instance

### Step 1: Connect to Instance

Using AWS Session Manager (recommended) or SSH:

```bash
# Via SSH (if you have keypair)
ssh -i smoking-cessation-key.pem ec2-user@<PRIVATE_IP>

# Or use Session Manager in EC2 Console
# Click instance → Connect → Session Manager → Connect
```
### Step  2: Update System

```bash
sudo yum update -y
sudo yum upgrade -y
```

### Step  3: Install PostgreSQL

```bash
# Add PostgreSQL repository
sudo tee /etc/yum.repos.d/pgdg.repo > /dev/null <<EOF
[pgdg15]
name=PostgreSQL 15 for RHEL/CentOS 9 - x86_64
baseurl=https://download.postgresql.org/pub/repos/yum/15/rhel/rhel-9-x86_64
enabled=1
gpgcheck=1
gpgkey=https://download.postgresql.org/pub/repos/yum/RPM-GPG-KEY-PGDG
EOF

# Install PostgreSQL
sudo yum install -y postgresql15-server postgresql15-contrib
```

### Step  4: Initialize Database Cluster

```bash
# Initialize cluster
sudo /usr/pgsql-15/bin/initdb -D /var/lib/pgsql/15/data

# Create system user if needed
sudo useradd postgres || true

# Change permissions
sudo chown -R postgres:postgres /var/lib/pgsql/15/data
```

### Step  5: Start PostgreSQL Service

```bash
# Enable service to start on boot
sudo systemctl enable postgresql-15

# Start service
sudo systemctl start postgresql-15

# Verify status
sudo systemctl status postgresql-15
```

### Step  6: Configure PostgreSQL for Network Access

```bash
# Edit config file
sudo nano /var/lib/pgsql/15/data/postgresql.conf

# Find and change these lines:
# listen_addresses = 'localhost'  → listen_addresses = '*'
# port = 5432  → keep as is

# Save: Ctrl+O, Enter, Ctrl+X
```

### Step  7: Configure Client Authentication

```bash
# Edit pg_hba.conf
sudo nano /var/lib/pgsql/15/data/pg_hba.conf

# Add this line at the end (before any reject lines):
# host    all             all             172.0.0.0/16            md5

# This allows connections from VPC CIDR 172.0.0.0/16
```

### Step  8: Restart PostgreSQL

```bash
sudo systemctl restart postgresql-15
sudo systemctl status postgresql-15
```

### Step  9: Create smoking_cessation Database

```bash
# Switch to postgres user
sudo su - postgres

# Connect to psql
psql

# Create database
CREATE DATABASE smoking_cessation;

# Create application user
CREATE USER app_user WITH PASSWORD 'YourSecurePassword123!';

# Grant privileges
GRANT ALL PRIVILEGES ON DATABASE smoking_cessation TO app_user;

# Connect to database
\c smoking_cessation

# Grant schema privileges
GRANT ALL ON SCHEMA public TO app_user;
GRANT ALL ON ALL TABLES IN SCHEMA public TO app_user;
GRANT ALL ON ALL SEQUENCES IN SCHEMA public TO app_user;

# Verify
\du  # List users
\l   # List databases

# Exit
\q
exit
```

### Step  10: Verify PostgreSQL is Listening

```bash
# Check if listening on port 5432
sudo netstat -tlnp | grep 5432

# Or use ss command
sudo ss -tlnp | grep 5432

# Output should show: LISTEN ... 0.0.0.0:5432 or :::5432
```

---

## Part  4: Setup MongoDB on DB-Mongo Instance

### Step  1: Connect to Instance

```bash
ssh -i smoking-cessation-key.pem ec2-user@<MONGO_PRIVATE_IP>
# Or use Session Manager
```

### Step  2: Update System

```bash
sudo yum update -y
sudo yum upgrade -y
```

### Step  3: Install MongoDB

```bash
# Create MongoDB repository
sudo tee /etc/yum.repos.d/mongodb-org.repo > /dev/null <<EOF
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2023/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-7.0.asc
EOF

# Install MongoDB
sudo yum install -y mongodb-org
```

### Step 4: Configure MongoDB for Network Access

```bash
# Edit MongoDB config
sudo nano /etc/mongod.conf

# Find these sections and modify:

# network:
#   port: 27017
#   bindIp: localhost  → Change to bindIp: 0.0.0.0

# Save: Ctrl+O, Enter, Ctrl+X
```

### Step  5: Start MongoDB Service

```bash
# Enable service to start on boot
sudo systemctl enable mongod

# Start service
sudo systemctl start mongod

# Verify status
sudo systemctl status mongod
```

### Step  6: Create MongoDB Database & User

```bash
# Connect to MongoDB
mongosh

# Switch to admin database
use admin

# Create admin user
db.createUser({
  user: "admin",
  pwd: "YourAdminPassword123!",
  roles: ["root"]
})

# Exit mongosh
exit

# Restart with authentication
sudo nano /etc/mongod.conf

# Find security section and uncomment:
# security:
#   authorization: enabled

# Save and restart
sudo systemctl restart mongod
```

### Step  7: Create Application Database & User

```bash
# Connect with authentication
mongosh -u admin -p YourAdminPassword123!

# Switch to smoking_cessation database
use smoking_cessation

# Create application user
db.createUser({
  user: "app_user",
  pwd: "AppPassword123!",
  roles: [{role: "readWrite", db: "smoking_cessation"}]
})

# Verify user created
show users

# Exit
exit
```

### Step  8: Verify MongoDB is Listening

```bash
# Check if listening on port 27017
sudo netstat -tlnp | grep 27017

# Or
sudo ss -tlnp | grep 27017

# Output should show LISTEN on port 27017
```

---

## Part  5: Test Database Connectivity

### Step  1: Test PostgreSQL from App Instance

Connect to application instance:

```bash
ssh -i smoking-cessation-key.pem ec2-user@<APP_USER_HOST>

# Install PostgreSQL client
sudo yum install -y postgresql15

# Test connection to PostgreSQL
psql -h <PG_HOST_IP> -U app_user -d smoking_cessation -c "SELECT 1"

# Expected output: Should show "1" (success)
```

### Step  2: Test MongoDB from App Instance

```bash
# Install MongoDB tools
sudo yum install -y mongodb-mongosh

# Test connection to MongoDB
mongosh --host <MONGO_HOST_IP>:27017 --username app_user --password AppPassword123! --authenticationDatabase smoking_cessation --eval "db.adminCommand('ping')"

# Expected output: { ok: 1 }
```

### Step  3: Test Inter-Instance Ping

```bash
# From any instance, test network connectivity
ping -c 3 <TARGET_IP>

# Expected: Low latency (< 5ms in same VPC)
```

---

## Part  6: Create Database Tables (PostgreSQL)

### Step  1: Connect to Database

From app instance or via psql:

```bash
# Connect to smoking_cessation database
psql -h <PG_HOST_IP> -U app_user -d smoking_cessation
```

### Step  2: Create Tables

```sql
-- Create users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  cognito_id VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL,
  role VARCHAR(50) DEFAULT 'user',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create coaches table
CREATE TABLE coaches (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  specialization VARCHAR(255),
  bio TEXT,
  hourly_rate DECIMAL(10, 2),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create coaching_sessions table
CREATE TABLE coaching_sessions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  coach_id UUID REFERENCES coaches(id) ON DELETE SET NULL,
  status VARCHAR(50) DEFAULT 'active',
  started_at TIMESTAMP,
  ended_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes for performance
CREATE INDEX idx_users_cognito_id ON users(cognito_id);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_coaches_user_id ON coaches(user_id);
CREATE INDEX idx_sessions_user_id ON coaching_sessions(user_id);
CREATE INDEX idx_sessions_coach_id ON coaching_sessions(coach_id);

-- Grant permissions to app_user
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO app_user;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO app_user;

-- Verify tables
\dt
```

### Step  3: Exit psql

```sql
\q
```

---

## Part  7: Setup Application Servers (EC2 Instances)

### Step  1: Connect to Application Instance

```bash
ssh -i smoking-cessation-key.pem ec2-user@<APP_USER_HOST>
```

### Step  2: Install Node.js & npm

```bash
# Update system
sudo yum update -y

# Install Node.js (Amazon Linux version)
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -
sudo yum install -y nodejs

# Verify installation
node --version
npm --version
```

### Step  3: Create Application Directory

```bash
# Create app directory
sudo mkdir -p /opt/smoking-cessation
sudo chown -R ec2-user:ec2-user /opt/smoking-cessation

# Change to directory
cd /opt/smoking-cessation

# Create basic package.json
cat > package.json << 'EOF'
{
  "name": "smoking-cessation-app",
  "version": "1.0.0",
  "description": "Smoking cessation user app",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "pg": "^8.10.0"
  }
}
EOF

# Install dependencies
npm install
```

### Step  4: Create Basic Express Server

```bash
# Create server.js
cat > server.js << 'EOF'
const express = require('express');
const { Pool } = require('pg');

const app = express();
const port = 8000;

// Database connection pool
const pool = new Pool({
  user: 'app_user',
  password: process.env.DB_PASSWORD || 'AppPassword123!',
  host: process.env.DB_HOST || 'localhost',
  port: 5432,
  database: 'smoking_cessation'
});

app.use(express.json());

// Health check endpoint
app.get('/health', (req, res) => {
  res.json({ status: 'ok', service: 'user-app' });
});

// Test database connection
app.get('/db-test', async (req, res) => {
  try {
    const result = await pool.query('SELECT NOW()');
    res.json({ message: 'Database connected', time: result.rows[0] });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.listen(port, () => {
  console.log(`App listening on port ${port}`);
});
EOF
```

### Step  5: Test Application

```bash
# Start server
node server.js

# In another terminal, test endpoints
curl http://localhost:8000/health
curl http://localhost:8000/db-test

# Ctrl+C to stop
```

### Step  6: Create Systemd Service (Optional)

```bash
# Create service file
sudo tee /etc/systemd/system/smoking-app.service > /dev/null <<EOF
[Unit]
Description=Smoking Cessation Application
After=network.target

[Service]
Type=simple
User=ec2-user
WorkingDirectory=/opt/smoking-cessation
ExecStart=/usr/bin/node server.js
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

# Enable and start service
sudo systemctl daemon-reload
sudo systemctl enable smoking-app
sudo systemctl start smoking-app
sudo systemctl status smoking-app
```

---

## Part  8: Setup Monitoring & CloudWatch

### Step  1: Enable Detailed Monitoring

1. EC2 Console
2. Select each instance
3. Right-click → "Monitoring and troubleshooting"
4. Click "Enable detailed monitoring"

This provides 1-minute metrics instead of default 5-minute.

### Step  2: Create CloudWatch Alarms

For high CPU on database instances:

1. CloudWatch Console
2. "Alarms" → "Create alarm"
3. **Metric**:
   - Namespace: AWS/EC2
   - Metric: CPUUtilization
   - Dimension: Instance ID (select DB-PG)
4. **Conditions**:
   - Statistic: Average
   - Period: 5 minutes
   - Threshold: > 80%
5. **Notification**: Create SNS topic for alerts
6. Click "Create alarm"

Repeat for all instances.

### Step  3: Create Dashboard

1. CloudWatch → Dashboards
2. "Create dashboard"
3. **Name**: `smoking-cessation-infrastructure`
4. Add widgets:
   - EC2 CPU Utilization (all instances)
   - Network In/Out
   - Disk usage (if CloudWatch agent installed)
5. Click "Create dashboard"

---

## Part  ần 9: Setup Database Backups

### Step  1: Create Backup Directory

On each database instance:

```bash
# Create backup directory
sudo mkdir -p /backups
sudo chown -R postgres:postgres /backups  # For PostgreSQL
```

### Step  2: PostgreSQL Automated Backup

```bash
# Connect as ec2-user
ssh -i smoking-cessation-key.pem ec2-user@<PG_HOST>

# Create backup script
cat > ~/backup-pg.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/smoking_cessation_$TIMESTAMP.sql"

sudo -u postgres pg_dump -d smoking_cessation -h localhost > "$BACKUP_FILE"

# Keep only last 7 days of backups
find $BACKUP_DIR -name "smoking_cessation_*.sql" -mtime +7 -delete

echo "Backup completed: $BACKUP_FILE"
EOF

# Make executable
chmod +x ~/backup-pg.sh

# Test backup
./backup-pg.sh

# Add to crontab for daily backups at 3 AM
crontab -e
# Add: 0 3 * * * /home/ec2-user/backup-pg.sh >> /var/log/postgres-backup.log 2>&1
```

### Step  3: MongoDB Automated Backup

```bash
# Connect as ec2-user
ssh -i smoking-cessation-key.pem ec2-user@<MONGO_HOST>

# Create backup script
cat > ~/backup-mongo.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_PATH="$BACKUP_DIR/smoking_cessation_$TIMESTAMP"

mongodump --username=admin --password="YourAdminPassword123!" --db smoking_cessation --out "$BACKUP_PATH"

# Keep only last 7 days
find $BACKUP_DIR -maxdepth 1 -type d -mtime +7 -exec rm -rf {} \;

echo "MongoDB backup completed: $BACKUP_PATH"
EOF

# Make executable
chmod +x ~/backup-mongo.sh

# Test backup
./backup-mongo.sh

# Add to crontab for daily backups at 4 AM
crontab -e
# Add: 0 4 * * * /home/ec2-user/backup-mongo.sh >> /var/log/mongo-backup.log 2>&1
```

### Step  4: Upload Backups to S3 (Optional)

To backup off-instance (safer):

```bash
# Configure AWS CLI on instance (requires IAM role)
aws configure

# Modify backup scripts to upload to S3:
# aws s3 cp $BACKUP_FILE s3://smoking-cessation-backups/
```

---

## Environment Variables Summary

Save these for Lambda functions & applications:

```env
# PostgreSQL
PG_HOST=<DB-PG_PRIVATE_IP>
PG_USER=app_user
PG_PASSWORD=AppPassword123!
PG_DATABASE=smoking_cessation
PG_PORT=5432

# MongoDB
MONGO_HOST=<DB-Mongo_PRIVATE_IP>
MONGO_USERNAME=app_user
MONGO_PASSWORD=AppPassword123!
MONGO_DATABASE=smoking_cessation
MONGO_PORT=27017
MONGO_URI=mongodb://app_user:AppPassword123!@<MONGO_HOST>:27017/smoking_cessation

# Application Instances
APP_USER_HOST=<APP_USER_PRIVATE_IP>
APP_SOCIAL_HOST=<APP_SOCIAL_PRIVATE_IP>
```

---

## Checklist

- [ ] 4 EC2 instances launched (DB-PG, DB-Mongo, App-User, App-Social)
- [ ] Security groups created with proper inbound/outbound rules
- [ ] PostgreSQL installed, configured, and accessible
- [ ] MongoDB installed, configured, and accessible
- [ ] smoking_cessation database created in both databases
- [ ] Application users created with proper permissions
- [ ] Database tables created (users, coaches, sessions)
- [ ] Application instances can connect to databases
- [ ] Inter-instance connectivity tested (ping, port access)
- [ ] Node.js & npm installed on app instances
- [ ] Express server created and tested
- [ ] CloudWatch detailed monitoring enabled
- [ ] CloudWatch alarms configured for high CPU
- [ ] CloudWatch dashboard created
- [ ] Database backup scripts created and tested
- [ ] Backup scripts added to crontab
- [ ] All database credentials saved securely
- [ ] Sẵn sàng cho Module 7 (Create S3 & CloudFront)

---

## Troubleshooting

### Cannot Connect to PostgreSQL

**Issue**: Connection refused on port 5432

**Solution**:
```bash
# Check PostgreSQL is running
sudo systemctl status postgresql-15

# Check listening on port 5432
sudo netstat -tlnp | grep 5432

# Check security group rules allow 5432
# From app instance, verify: telnet <PG_HOST> 5432

# Check pg_hba.conf allows VPC CIDR
sudo nano /var/lib/pgsql/15/data/pg_hba.conf
```

### Cannot Connect to MongoDB

**Issue**: Connection refused on port 27017

**Solution**:
```bash
# Check MongoDB is running
sudo systemctl status mongod

# Check port listening
sudo netstat -tlnp | grep 27017

# Check mongod.conf bindIp is correct
sudo nano /etc/mongod.conf

# Verify authentication enabled correctly
mongosh -u admin -p <password>
```

### Network Connectivity Issues

**Issue**: Instances cannot ping each other

**Solution**:
1. Verify all instances are in same VPC
2. Check security group rules allow traffic between groups
3. Check Network ACLs don't block traffic
4. Verify instances have route table entries

### High CPU on Database Instance

**Issue**: CPU Utilization > 80%

**Solution**:
```bash
# Check top processes
top -b -n 1 | head -20

# For PostgreSQL, check long-running queries:
psql -h localhost -U postgres -d smoking_cessation
SELECT pid, usename, query, query_start FROM pg_stat_activity WHERE query != 'autovacuum';

# Consider: Scale up instance type, optimize queries, or add replication
```

---

## Cost Analysis

**Current costs** (4 × t4g.small at $0.0252/hour):

- Compute: ~$30/month (4 instances × 730 hours)
- Storage: ~$10/month (4 × 30GB EBS volumes)
- Data transfer: ~$5/month
- **Total: ~$45/month**

**Optimization options**:
- Use Reserved Instances for 30% savings (~$31/month)
- Right-size if low usage (t4g.micro: ~$10/month)
- Consolidate to 2 instances if possible

---

## Next Steps

1. Configure auto-scaling for application instances (optional)
2. Setup read replicas for PostgreSQL (optional)
3. Enable database replication for high availability (optional)
4. Configure point-in-time recovery (PITR) for databases (optional)
5. Setup monitoring alerts (Module 9)

---
## Achieved Results

After completing Module 6, you will have:

1. ✅ 4 EC2 instances created & running  
2. ✅ Security groups configured with proper rules  
3. ✅ PostgreSQL server fully set up & database created  
4. ✅ MongoDB server fully set up & database created  
5. ✅ Application users created in both databases  
6. ✅ Database tables created & indexed  
7. ✅ Application server configuration completed  
8. ✅ Inter-instance connectivity tested  
9. ✅ CloudWatch monitoring enabled  
10. ✅ CloudWatch alarms configured  
11. ✅ Database backup scripts set up  
12. ✅ All databases operational & accessible  
13. ✅ Ready for Module 7 (Create S3 & CloudFront)
