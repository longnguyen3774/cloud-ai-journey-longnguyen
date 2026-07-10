---
title: "Day 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

> **Day 3 - Monday, May 4, 2026:** AWS CLI fundamentals, NoSQL with DynamoDB, CloudFront & Lambda@Edge, plus hands-on deployment of a web server on EC2 with a custom VPC.

---

### Objectives for the Day

- Master the **AWS CLI** for programmatic resource management directly from the terminal.
- Understand **NoSQL database** concepts with Amazon DynamoDB and when to choose it over relational databases.
- Study **CDN** architecture with CloudFront and edge computing with Lambda@Edge.
- Deploy a complete **web server** on EC2 within a custom VPC - including networking setup from scratch.

---

### Theory: AWS CLI (Command Line Interface)

**AWS CLI** is the unified command-line tool for interacting with all AWS services directly from a terminal - enabling automation, scripting, and programmatic management of cloud resources without touching the Console.

**Key concepts learned:**
- Install and configure AWS CLI using `aws configure` (Access Key ID, Secret Access Key, default Region, Output format).
- Run common commands: `aws ec2 describe-instances`, `aws s3 ls`, `aws iam list-users`.
- Use the `--query` flag (JMESPath syntax) and `--output` flag (json, table, text) to filter and format results.
- Create **named profiles** to manage multiple AWS accounts: `aws configure --profile my-profile`.
- Use `--dry-run` flag to validate permissions without actually executing a command.

```bash
# Example: List all S3 buckets
aws s3 ls

# Example: Describe running EC2 instances in a specific region
aws ec2 describe-instances \
  --region ap-southeast-1 \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[].Instances[].{ID:InstanceId,Type:InstanceType}'
```

> **Lesson:** AWS CLI is an indispensable tool for DevOps engineers - every resource manageable in the Console can be automated via CLI. Mastering it is the first step toward building deployment pipelines.

---

### Theory: NoSQL with Amazon DynamoDB

**Amazon DynamoDB** is a serverless, fully managed NoSQL database delivering single-digit millisecond performance at any scale - no capacity planning, no maintenance windows.

**Core concepts:**
- **Data model:** Tables, Items (rows), Attributes (columns) - no fixed schema required.
- **Primary Keys:**
  - *Partition Key only* (simple): e.g., `UserId`
  - *Partition Key + Sort Key* (composite): e.g., `UserId` + `OrderDate` for time-series data
- **Read/Write capacity modes:**
  - *Provisioned*: Fixed capacity, predictable workloads, cheaper at consistent scale
  - *On-Demand*: Auto-scales instantly, pay per request - ideal for unpredictable traffic
- **DynamoDB Streams:** Capture item-level changes in order - power event-driven architectures (e.g., trigger Lambda on every data change).
- **Global Tables:** Multi-region, multi-master replication for sub-10ms latency anywhere in the world.
- **DAX (DynamoDB Accelerator):** In-memory cache that reduces read latency from milliseconds to **microseconds**.

> **Lesson:** Choose DynamoDB when you need horizontal scalability, flexible schema, and consistent millisecond latency - perfect for leaderboards, session stores, IoT telemetry, and shopping carts. Choose RDS when you need complex joins, ACID transactions, and strict schema.

---

### Theory: CloudFront & Lambda@Edge

**Amazon CloudFront** is AWS's global **CDN (Content Delivery Network)** - caches and serves content from **Edge Locations** closest to the end user, dramatically reducing latency for static assets, APIs, and streaming media.

**Lambda@Edge** extends AWS Lambda to run at CloudFront edge locations - allowing request/response customization without routing traffic back to the origin server.

**Key concepts:**
- **Distributions:** CloudFront delivery configurations pointing to **origins** (S3, ALB, EC2, custom HTTP endpoints).
- **Behaviors:** Rules mapping URL patterns to specific origin and cache configurations.
- **Cache policies & Origin request policies:** Fine-grained control over what gets cached and what's forwarded to the origin.
- **HTTPS enforcement:** Force all traffic to HTTPS via custom SSL certificates through AWS Certificate Manager (ACM).
- **Lambda@Edge triggers:**
  - *Viewer Request*: Modify the request before CloudFront checks cache
  - *Origin Request*: Modify what's forwarded to origin (cache miss)
  - *Origin Response*: Modify the response from origin before it's cached
  - *Viewer Response*: Modify the final response sent to the client
- **Use cases:** URL rewrites, A/B testing at the edge, JWT authentication without a backend, image optimization.

---

### Hands-On: Deploy a Web Server on EC2 with Custom VPC

#### Step 1: Create VPC and Networking

| Resource | Configuration |
|----------|--------------|
| VPC | CIDR: `10.0.0.0/16` |
| Public Subnet | CIDR: `10.0.1.0/24`, AZ: `ap-southeast-1a` |
| Internet Gateway | Attached to VPC |
| Route Table | Route `0.0.0.0/0` → Internet Gateway |

Creating a custom VPC from scratch rather than using the default VPC builds a deeper understanding of how AWS networking actually works.

#### Step 2: Launch EC2 Instance as Web Server

1. Go to **EC2 Console** → **Launch Instance**.
2. Select **Amazon Linux 2023 AMI** (free tier eligible).
3. Instance type: `t2.micro`.
4. Place instance in the custom VPC and public subnet.
5. Configure **Security Group**: Allow inbound `HTTP (port 80)` and `SSH (port 22)`.
6. Add **User Data script** to automatically install and start Apache on boot:
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "<h1>Hello from AWS EC2 - Day 3!</h1>" > /var/www/html/index.html
   ```
7. Launch the instance and access the public IP to verify the web server is working.

#### Step 3: Clean Up After Practice

```bash
# Terminate EC2 instance
aws ec2 terminate-instances --instance-ids <instance-id>

# Detach and delete Internet Gateway
aws ec2 detach-internet-gateway --internet-gateway-id <igw-id> --vpc-id <vpc-id>
aws ec2 delete-internet-gateway --internet-gateway-id <igw-id>

# Delete Subnet, Route Table, then VPC (in that order)
aws ec2 delete-subnet --subnet-id <subnet-id>
aws ec2 delete-route-table --route-table-id <rt-id>
aws ec2 delete-vpc --vpc-id <vpc-id>
```

> ⚠️ Always clean up all resources after practice to avoid unexpected charges.

> **Lesson:** Understanding VPC networking - subnets, route tables, and Internet Gateways - is foundational to deploying any internet-accessible resource on AWS. Almost every production architecture starts with a well-designed VPC.

---

### Key Takeaways

- **AWS CLI** transforms manual Console operations into repeatable, scriptable commands - essential for automation.
- **DynamoDB vs. RDS** is not a matter of better/worse - it's about matching the database model to the access patterns.
- **CloudFront + Lambda@Edge** enables powerful edge computing: security, personalization, and performance optimization without any round-trips to the origin.
- Building a VPC from scratch (not using the default) reveals the true mechanics of AWS networking - subnets, routing tables, and internet gateway dependencies.
- The **User Data script** pattern is powerful - it enables fully automated server configuration at launch time without any manual SSH login.

---

*Source: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
