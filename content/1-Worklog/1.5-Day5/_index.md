---
title: "Day 5"
date: 2026-05-06
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

> **Day 5 - Wednesday, May 6, 2026:** Infrastructure as Code with AWS CloudFormation, data engineering pipeline with AWS Glue & Amazon Athena, and hands-on provisioning of a relational database with Amazon RDS.

---

### Objectives for the Day

- Master **AWS CloudFormation** as the native Infrastructure as Code service for repeatable, auditable deployments.
- Understand the **AWS Glue + Amazon Athena** stack for building a cost-effective data lake analytics pipeline.
- Hands-on: Provision a **PostgreSQL or MySQL relational database** using Amazon RDS inside a custom VPC with proper security group configuration.

---

### Theory: AWS CloudFormation - Infrastructure as Code

**AWS CloudFormation** is AWS's native **IaC (Infrastructure as Code)** service - define your entire AWS environment in a JSON or YAML template and deploy it consistently, repeatedly, and safely.

**Template structure:**

| Section | Purpose |
|---------|---------|
| `AWSTemplateFormatVersion` | Template format version declaration |
| `Parameters` | Input values (environment, instance type, etc.) - makes templates reusable |
| `Resources` | The actual AWS resources to create (the only required section) |
| `Outputs` | Values to export (ARNs, endpoints) for cross-stack references |
| `Mappings` | Key-value lookup tables (e.g., AMI IDs per region) |
| `Conditions` | Conditional resource creation (e.g., only create a NAT Gateway in prod) |

**Key concepts:**
- **Stacks:** A logical group of AWS resources from one template - created, updated, and deleted together as a unit.
- **Change Sets:** Preview infrastructure changes before applying - see exactly what will be added, modified, or removed. *Always use Change Sets in production.*
- **Stack Sets:** Deploy the same template across multiple AWS accounts and regions simultaneously - critical for multi-account organizations.
- **Drift Detection:** Detect when resources have been manually modified outside of CloudFormation - identifies configuration drift from the source of truth.
- **Nested Stacks:** Break large templates into smaller, reusable modules - reference child stacks from a parent stack template.

**Simple CloudFormation template example:**
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Day 5 Practice - S3 Bucket with Versioning'

Parameters:
  Environment:
    Type: String
    AllowedValues: [dev, staging, prod]
    Default: dev

Resources:
  DataBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub 'fcaj-data-${Environment}-${AWS::AccountId}'
      VersioningConfiguration:
        Status: Enabled
      Tags:
        - Key: Environment
          Value: !Ref Environment

Outputs:
  BucketName:
    Value: !Ref DataBucket
    Export:
      Name: !Sub '${AWS::StackName}-BucketName'
  BucketArn:
    Value: !GetAtt DataBucket.Arn
    Export:
      Name: !Sub '${AWS::StackName}-BucketArn'
```

> **Lesson:** CloudFormation creates **reproducible, version-controlled infrastructure** - the foundation of GitOps and CI/CD pipelines for cloud environments. Every manual Console action is a gap in your infrastructure audit trail.

---

### Theory: AWS Glue & Amazon Athena - Data Engineering

#### AWS Glue

**AWS Glue** is a fully managed **ETL (Extract, Transform, Load)** service for building data pipelines without managing infrastructure - the backbone of serverless data engineering on AWS.

**Key components:**
- **Data Catalog:** A centralized metadata repository - stores table schemas, partition information, and data locations. Acts as the Hive Metastore for the AWS ecosystem.
- **Glue Crawlers:** Automatically discover data in S3, RDS, DynamoDB, and other sources - infer schema, detect new partitions, and keep the Data Catalog up to date.
- **ETL Jobs:** PySpark, Spark Streaming, or Python Shell scripts to transform raw data into structured, analytics-ready formats.
- **Glue Studio:** Visual ETL job builder - drag-and-drop interface for non-programmers to design transformation pipelines.
- **Glue DataBrew:** Guided data profiling and cleaning - no-code tool for data analysts to explore, visualize, and clean datasets.

#### Amazon Athena

**Amazon Athena** is a **serverless, interactive SQL query engine** for analyzing data directly on **Amazon S3** - no data loading, no cluster management, no infrastructure to provision.

**Key capabilities:**
- Supports **ANSI SQL** standard - if you know SQL, you can query data lakes immediately.
- Query data in CSV, JSON, Parquet, ORC, Avro, and more - directly from S3.
- **Pay-per-query pricing:** Charged only for data scanned (typically $5/TB). Optimize costs by:
  - Using **columnar formats** (Parquet, ORC) - only the needed columns are read.
  - **Partitioning** data - queries only scan relevant partitions.
- **AWS Glue Data Catalog integration:** Athena uses Glue's Data Catalog as its metadata layer - no separate schema management needed.
- **Federated Queries:** Query data across RDS, DynamoDB, Redshift, and on-premises sources from a single Athena query - powered by Lambda-based connectors.
- Query results automatically saved to a designated **S3 output bucket**.

**Typical data lake analytics flow:**
```
Raw data (CSV/JSON) → S3
         ↓
Glue Crawler → discovers schema → updates Data Catalog
         ↓
Glue ETL Job → transforms to Parquet + partition by date → S3
         ↓
Athena → SQL queries on optimized Parquet data
         ↓
QuickSight / Notebooks → visualization & analysis
```

> **Lesson:** The Glue + Athena combination creates an efficient, cost-optimized data lake analytics stack - ingest raw data to S3, catalog with Glue, transform to columnar format, then query instantly with Athena. No servers, no clusters, no upfront infrastructure cost.

---

### Hands-On: Provisioning a Relational Database with Amazon RDS

**Goal:** Provision a managed PostgreSQL database inside a custom VPC with proper security configuration.

#### Step 1: Create DB Subnet Group

A DB Subnet Group tells RDS which subnets it can place database instances in. Requires at least 2 subnets in different Availability Zones (mandatory for Multi-AZ deployments).

1. Go to **RDS Console** → **Subnet Groups** → **Create DB Subnet Group**.
2. Select the custom VPC and at least 2 subnets in different AZs.

#### Step 2: Configure Security Group for RDS

Create a dedicated security group that only allows database traffic from application servers (not the public internet):

- Allow inbound `PostgreSQL (port 5432)` or `MySQL (port 3306)` **only from the application EC2's security group** - not from `0.0.0.0/0`.
- This implements **security group chaining** - a best practice for database access control.

#### Step 3: Launch RDS Instance

| Parameter | Value |
|-----------|-------|
| Engine | PostgreSQL 15 (or MySQL 8.0) |
| Template | Free Tier |
| Instance class | `db.t3.micro` |
| Storage | 20 GB gp2 (General Purpose SSD) |
| Multi-AZ | Disabled (Free Tier) |
| VPC | Custom VPC |
| Public Access | No |
| Backup Retention | 7 days |

#### Step 4: Connect to the Database

```bash
# Using psql (PostgreSQL)
psql -h <rds-endpoint> -U admin -d postgres

# Using mysql client
mysql -h <rds-endpoint> -u admin -p
```

#### Step 5: Clean Up

```bash
# Delete RDS instance (disable final snapshot to avoid charges)
aws rds delete-db-instance \
  --db-instance-identifier my-day5-db \
  --skip-final-snapshot

# Wait for deletion, then delete the DB Subnet Group
aws rds delete-db-subnet-group \
  --db-subnet-group-name my-day5-subnet-group
```

> **Lesson:** Amazon RDS eliminates all database administration overhead - patching, backups, failover, and replica management are all handled automatically. This lets developers focus entirely on data modeling and application logic instead of DBA tasks.

---

### Key Takeaways

- **CloudFormation** is the bedrock of infrastructure reproducibility - manual Console operations are fine for exploration but should never be done in production without a corresponding template change.
- **Change Sets** are a safety net before applying any CloudFormation update - they prevent the "I didn't know that would be deleted" surprises.
- The **Glue + Athena** stack demonstrates AWS's philosophy of serverless data engineering: pay only for what you use, no clusters sitting idle overnight.
- **Columnar format (Parquet)** is the single most impactful optimization for Athena queries - it can reduce data scanned (and cost) by 10x or more vs. raw CSV.
- **Security group chaining** for RDS (only allowing traffic from specific application security groups) is the correct database security model - never expose database ports to the internet.

---

*Source: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
