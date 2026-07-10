---
title: "Day 2"
date: 2026-04-22
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

> **Day 2 - Wednesday, April 22, 2026:** Setting up a multi-layer cost monitoring system, exploring advanced analytics, and studying foundational AWS theory.

---

### Objectives for the Day

- Establish a comprehensive **Basic Monitoring** system across three alert levels.
- Research and configure **Advanced Analytics** using Custom CloudWatch Metrics and Resource Tagging.
- Build an **Emergency Cost Control Protocol** for rapid response to unexpected charges.
- Study foundational AWS theory: Cloud Computing, Global Infrastructure, IAM, Networking, Storage, Database, Compute.

---

### Basic Monitoring - Cost Control System

#### 1. AWS Budgets - Three-Tier Alert Thresholds

| Budget | Limit | Alert Trigger |
|--------|-------|---------------|
| Budget 1 | $50/month | Warning at 80% ($40) |
| Budget 2 | $25/month | Warning at 50% ($12.50) |
| Budget 3 | $10/day | Warning at 100% ($10) |

The three-tier approach ensures early warning well before the credit limit is reached - Budget 3 acts as a daily guardrail that catches runaway resources within 24 hours.

#### 2. CloudWatch Billing Alarms - Escalating Notifications

| Threshold | Notification Channel |
|-----------|---------------------|
| $25 | Email warning |
| $50 | Email + SMS |
| $75 | Email + SMS + Slack |

Configuring escalating channels ensures that if an email is missed, the SMS and Slack alerts provide a second and third layer of visibility.

#### 3. AWS Cost Explorer - Daily Reporting

- Activated **daily cost reports** to track spending day by day rather than waiting for monthly surprises.
- Configured **service-level breakdowns** to identify which AWS service is consuming the most credit.
- Set up monitoring for the **top 5 cost drivers** to quickly pinpoint the highest-cost resources.

---

### Advanced Analytics

#### Custom CloudWatch Metrics

Created custom metrics to monitor business-specific indicators that go beyond AWS's default metrics. This is especially useful for tracking application-level events (e.g., number of API calls, error rates, queue depth) alongside infrastructure metrics.

#### Resource Tagging Strategy

Applied a consistent tagging policy across all AWS resources:

| Tag Key | Example Value | Purpose |
|---------|--------------|---------|
| `Project` | `FCAJ-Internship` | Track per-project spending |
| `Environment` | `dev` / `prod` | Separate dev from production costs |
| `Owner` | `HuynhThaiLinh` | Individual accountability |

Consistent tagging enables cost allocation reports to show exactly where money is going - by team, project, and environment.

---

### Emergency Cost Control Protocol

#### Immediate Resource Audit

When abnormal costs are detected, the first step is a full audit of all running resources:
```bash
# List all running EC2 instances across all regions
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[].Instances[].{ID:InstanceId,Type:InstanceType,Region:Placement.AvailabilityZone}'

# Check all active RDS instances
aws rds describe-db-instances --query 'DBInstances[].{ID:DBInstanceIdentifier,Status:DBInstanceStatus}'
```

#### Emergency Shutdown Protocol

A documented runbook for shutting down non-essential resources to stop cost escalation - critical in a learning environment where an accidentally left-on GPU instance or NAT Gateway can drain credits in hours.

---

### Foundational Theory: Cloud Computing Overview

**Benefits of Cloud Computing:**
- **Elasticity:** Scale resources up or down on demand - no need to over-provision for peak traffic.
- **Pay-as-you-go:** Only pay for what you actually use, when you use it.
- **Global reach:** Deploy infrastructure across 30+ global regions in minutes.

**Service Models:**
| Model | AWS Example | What You Manage |
|-------|-------------|-----------------|
| **IaaS** | EC2, VPC | OS, runtime, application |
| **PaaS** | Elastic Beanstalk, RDS | Application logic only |
| **SaaS** | Amazon Chime, WorkMail | Nothing - just use it |

---

### Foundational Theory: AWS Global Infrastructure

- **Regions (~30+):** Geographically isolated areas. Data does not leave a region unless explicitly configured.
- **Availability Zones (AZs):** 2–6 physically separate data centers within a Region, connected by low-latency links. Running resources across multiple AZs ensures **High Availability**.
- **Edge Locations (~400+):** Points of presence for CloudFront CDN - serve cached content from the location closest to end users, reducing latency.

---

### Foundational Theory: AWS Security & IAM

- **Root Account:** The master account with unlimited privileges - use only for initial setup, then protect with MFA and lock away.
- **IAM Users:** Individual identities for people or applications - never share credentials.
- **IAM Groups:** Logical collections of users - attach policies to groups, not individual users.
- **IAM Policies:** JSON documents defining what actions are allowed or denied on which resources.
- **Principle of Least Privilege:** Grant only the minimum permissions necessary - nothing more.
- **MFA (Multi-Factor Authentication):** Mandatory for Root account; strongly recommended for all IAM users with elevated privileges.

---

### Foundational Theory: Core Services Overview

**Amazon S3 (Simple Storage Service):**
- Object storage with **99.999999999% (11 nines) durability**.
- Storage classes: S3 Standard → S3 Standard-IA → S3 Glacier → S3 Glacier Deep Archive (increasing cost efficiency, decreasing retrieval speed).

**Amazon EC2 (Elastic Compute Cloud):**
- Flexible compute with dozens of instance families optimized for different workloads (general purpose, compute optimized, memory optimized, GPU).
- Key decisions: Instance type, AMI, VPC/Subnet placement, Security Group, Key Pair.

**Amazon RDS:** Managed relational database - automated backups, patching, multi-AZ failover.

**AWS Lambda:** Serverless event-driven compute - no server management, scales automatically.

---

### Topics Studied Today

| Date | Topic |
|------|-------|
| 22/4 | Compute Essentials with Amazon EC2 |
| 22/4 | Instance Profiling with IAM Roles for EC2 |
| 22/4 | Cloud Development with AWS Cloud9 |
| 22/4 | Static Website Hosting with Amazon S3 |
| 22/4 | Database Essentials with Amazon RDS |

---

### Key Takeaways

- A **three-tier monitoring system** (Budgets → CloudWatch Alarms → Cost Explorer) provides defense-in-depth against unexpected costs.
- **Resource tagging** is the foundation of cost accountability - without tags, it's impossible to know which project or person caused a spike.
- Understanding the **Shared Responsibility Model** clarified what AWS manages vs. what I'm responsible for - security of the cloud (AWS) vs. security in the cloud (me).
- The **Emergency Protocol** mindset is as important as technical skills - knowing how to quickly stop a runaway cost situation is a real-world production skill.

---

*Source: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
