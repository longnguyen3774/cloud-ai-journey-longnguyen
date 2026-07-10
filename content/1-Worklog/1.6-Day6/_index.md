---
title: "Day 6"
date: 2026-06-04
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

> **Day 6 - Friday, June 4, 2026:** Running a simple data pipeline architecture experiment using IaC code, local testing with Floci and LocalStack (ministack), and deep-diving into streaming and analytics services - EMR, Kinesis, Firehose, Kinesis Data Streams, Lake Formation, Redshift, and QuickSight.

---

### Objectives for the Day

- Build and test a **simple end-to-end data pipeline** using Infrastructure as Code (AWS CDK).
- Use **Floci** and **LocalStack (ministack)** as local AWS emulators to validate architecture without incurring real AWS costs.
- Understand the role and use cases of **Amazon EMR**, **Amazon Kinesis**, **Kinesis Data Streams**, **Amazon Data Firehose**, **AWS Lake Formation**, **Amazon Redshift**, and **Amazon QuickSight** in a modern data platform.

---

### Hands-On: Simple Data Pipeline Experiment with IaC

#### Architecture Overview

The goal was to design and locally test a lightweight data pipeline that covers the full journey of data - from ingestion to storage to query:

```
[Data Source / Event Generator]
         ↓
[Kinesis Data Streams]  ← real-time ingestion
         ↓
[Amazon Data Firehose]  ← buffering + delivery
         ↓
[Amazon S3]             ← raw data lake storage
         ↓
[AWS Glue Crawler]      ← schema discovery
         ↓
[Amazon Athena]         ← SQL analytics on raw data
         ↓
[Amazon Redshift]       ← structured data warehouse
         ↓
[Amazon QuickSight]     ← dashboards & visualization
```

The entire architecture was defined in **AWS CDK (TypeScript)** - meaning the whole infrastructure can be deployed, torn down, and re-deployed with a single command.

#### Testing Locally with Floci and LocalStack

Rather than deploying to real AWS immediately, the architecture was validated first using two open-source local emulators:

**Floci:**
- A lightweight AWS local emulator built for speed and low resource usage.
- **138x faster startup** and **11x lower memory usage** than LocalStack Community.
- Used to emulate S3, Lambda, and DynamoDB locally for rapid iteration - write code, test instantly, no billing risk.
- Ideal for early-stage architecture validation before committing to real infrastructure.

**LocalStack (ministack):**
- A more comprehensive AWS emulator that supports a wider range of services.
- Used here to emulate Kinesis Data Streams and Firehose locally.
- Runs as a Docker container: `docker run -p 4566:4566 localstack/localstack`.
- Allows testing the full pipeline flow - produce events → Kinesis → Firehose → S3 → Athena query - entirely on a local machine.

**Why test locally first?**
- Zero cost during iteration - no risk of accidentally leaving a Kinesis shard or Redshift cluster running.
- Faster feedback loops - deploy and test in seconds, not minutes.
- Validate IaC logic before touching production or even dev AWS accounts.

**CDK stack structure tested:**
```typescript
// Simplified CDK snippet - data pipeline stack
const stream = new kinesis.Stream(this, 'DataStream', {
  streamName: 'fcaj-data-stream',
  shardCount: 1,
});

const bucket = new s3.Bucket(this, 'DataLakeBucket', {
  bucketName: 'fcaj-data-lake',
  removalPolicy: cdk.RemovalPolicy.DESTROY,
});

const firehose = new kinesisFirehose.DeliveryStream(this, 'FirehoseStream', {
  sourceStream: stream,
  destinations: [new destinations.S3Bucket(bucket)],
});
```

After validating locally with Floci/LocalStack, the same CDK code deploys identically to real AWS - no rewrites needed.

---

### Theory: Amazon EMR (Elastic MapReduce)

**Amazon EMR** is a managed big data platform that runs open-source frameworks like **Apache Spark**, **Apache Hive**, **Hadoop**, **Presto**, and **Flink** on scalable EC2 clusters - or serverlessly via **EMR Serverless**.

**When to use EMR:**
- Processing **terabytes to petabytes** of data - batch ETL at massive scale.
- Running complex **Spark ML pipelines** across distributed clusters.
- Cost-effective big data processing using **Spot Instances** (up to 90% cheaper than On-Demand).
- Teams already familiar with Spark/Hadoop ecosystems who need cloud-managed infrastructure.

**EMR deployment modes:**
| Mode | Best For |
|------|----------|
| **EMR on EC2** | Full control over cluster config, long-running jobs |
| **EMR Serverless** | Serverless Spark/Hive - no cluster management, auto-scales to zero |
| **EMR on EKS** | Run Spark on Kubernetes clusters |

**Key EMR concepts:**
- **Primary node:** Coordinates the cluster - manages job scheduling and resource allocation.
- **Core nodes:** Run tasks AND store data in HDFS - must always be present.
- **Task nodes:** Run tasks only - no storage, can be Spot Instances safely (no data loss if terminated).
- **Bootstrap actions:** Custom scripts run on every node when the cluster starts - install dependencies, configure settings.
- **EMR Steps:** Units of work submitted to the cluster (e.g., "run this Spark job").

> **Lesson:** EMR Serverless is the right starting point for most workloads - it removes cluster management entirely and scales to zero when idle, making it far more cost-efficient than keeping a cluster running 24/7.

---

### Theory: Amazon Kinesis - Real-Time Streaming Data

**Amazon Kinesis** is a family of services for collecting, processing, and analyzing real-time streaming data at scale.

#### Kinesis Data Streams

**Kinesis Data Streams** is a massively scalable, durable real-time data streaming service - think of it as a high-throughput, low-latency message bus for data.

**Core concepts:**
- **Shards:** The unit of capacity. Each shard handles **1 MB/s write** and **2 MB/s read**.
- **Partition Key:** Determines which shard a record goes to - choose carefully to avoid "hot shards" where one shard receives disproportionate traffic.
- **Sequence Number:** Unique identifier for each record within a shard - guarantees ordering within a shard.
- **Retention Period:** Data stored for **24 hours by default**, extendable up to **365 days**.
- **Consumers:** Applications reading from the stream - Lambda, Kinesis Data Analytics, Firehose, custom EC2/ECS apps.

**Use cases:** Real-time analytics, log and event data collection, clickstream analysis, IoT telemetry ingestion.

#### Amazon Data Firehose

**Amazon Data Firehose** (formerly Kinesis Data Firehose) is a fully managed service that reliably **loads streaming data** into data stores - S3, Redshift, OpenSearch, Splunk - with built-in buffering, compression, and optional transformation.

**Key features:**
- **Sources:** Kinesis Data Streams, Amazon MSK, Direct PUT (HTTP endpoint), CloudWatch Logs.
- **Destinations:** Amazon S3, Amazon Redshift, Amazon OpenSearch, HTTP endpoints, Splunk.
- **Buffering:** Accumulates data before delivery - configurable by **size** (1–128 MB) or **time** (60–900 seconds).
- **Lambda transformation:** Optionally invoke a Lambda function to transform records inline before delivery - format conversion, filtering, enrichment.
- **Format conversion:** Automatically convert JSON to Parquet or ORC for cost-efficient S3 storage (no manual ETL job needed).
- **Fully managed:** No shards, no capacity planning - just configure source → destination and it scales automatically.

> **Key distinction:** Kinesis Data Streams = low-latency, custom consumer logic, full control. Firehose = zero-administration delivery pipeline into data stores, with built-in buffering.

---

### Theory: AWS Lake Formation

**AWS Lake Formation** is a managed service that makes it **easy to set up, secure, and manage a data lake** - it provides a single place to define and enforce fine-grained access controls across all data lake resources.

**Core capabilities:**
- **Centralized permissions:** Define who can access which **databases, tables, columns, and rows** across S3, Glue, Athena, and Redshift Spectrum from one place.
- **Column-level security:** Restrict access to specific columns - e.g., analysts can see sales data but not the PII columns.
- **Row-level filtering:** Restrict rows based on the requester's identity or attributes.
- **Data Catalog integration:** Works directly with the AWS Glue Data Catalog - the same metadata catalog used by Athena and Glue.
- **Cross-account data sharing:** Securely share governed data assets across AWS accounts without copying data.
- **Governed Tables:** Native Lake Formation table format with automatic compaction, ACID transactions, and row-level security.

**The Lake Formation permission model:**
Rather than managing dozens of S3 bucket policies and IAM policies independently, Lake Formation provides a unified **grant/revoke** model:
```
GRANT SELECT ON DATABASE analytics_db TO IAM ROLE data-analyst-role
GRANT SELECT ON TABLE sales.orders (order_id, amount, date) TO IAM ROLE reporting-role
  -- note: customer_name column is NOT granted → automatic column masking
```

> **Lesson:** Lake Formation is the correct governance layer for any multi-team data platform - it prevents the "permission sprawl" that happens when each team manages their own S3 bucket policies.

---

### Theory: Amazon Redshift

**Amazon Redshift** is AWS's fully managed **cloud data warehouse** - designed for complex analytical queries (OLAP) on structured data at petabyte scale, using columnar storage and massively parallel processing (MPP).

**Core concepts:**
- **Columnar storage:** Data stored by column rather than row - dramatically speeds up aggregation queries that touch only a few columns out of many.
- **MPP (Massively Parallel Processing):** Queries distributed across multiple compute nodes and processed in parallel.
- **Leader node:** Coordinates query planning and distributes work to compute nodes.
- **Compute nodes:** Execute query fragments and return results to the leader node.
- **Redshift Spectrum:** Query data directly in S3 (in Parquet, ORC, CSV) without loading into Redshift - pay only for data scanned.
- **RA3 nodes:** Separate storage (S3-backed) from compute - scale each independently.

**When to choose Redshift over Athena:**
| | Athena | Redshift |
|-|--------|----------|
| **Data location** | Always in S3 | Loaded into Redshift (or Spectrum for S3) |
| **Query complexity** | Simple to moderate SQL | Complex analytical queries, JOINs at scale |
| **Cost model** | Per-query (pay per TB scanned) | Per-hour cluster (or Serverless per RPU) |
| **Best for** | Ad-hoc exploration, infrequent queries | High-concurrency, repeated analytical workloads |
| **Performance** | Seconds to minutes | Sub-second for cached/optimized queries |

**Redshift Serverless:** The managed option - no cluster provisioning, automatically scales capacity (RPUs) based on workload, billed per second of compute used.

> **Lesson:** Redshift is the right choice when you have a well-defined set of recurring analytical queries with many concurrent users - it handles the optimization (distribution keys, sort keys, materialized views) that makes complex queries fast at scale.

---

### Theory: Amazon QuickSight

**Amazon QuickSight** is AWS's cloud-native **Business Intelligence (BI) and data visualization service** - connect to data sources, build interactive dashboards, and share insights across the organization.

**Key features:**
- **SPICE (Super-fast, Parallel, In-memory Calculation Engine):** In-memory data engine that pre-aggregates data for sub-second dashboard interactivity - no need to re-query the source on every interaction.
- **Data sources:** Amazon S3 (via Athena), Redshift, RDS, Aurora, DynamoDB, Salesforce, and 50+ connectors.
- **ML Insights (built-in):** Anomaly detection, forecasting, and natural language narratives (auto-generated written summaries of chart trends) - no data science expertise required.
- **Amazon Q in QuickSight:** Ask questions in plain language ("Show me sales by region last quarter") and QuickSight generates the chart automatically.
- **Embedded analytics:** Embed QuickSight dashboards directly into your own web applications using the Embedding SDK.
- **Pricing model:** Per-session pricing for readers (pay per 30-minute session, capped at $5/user/month) vs. per-author pricing for dashboard creators.

**QuickSight in the data pipeline:**
QuickSight sits at the end of the data pipeline - it reads from Athena (for ad-hoc exploration of raw S3 data) or directly from Redshift (for high-performance analytical queries on curated data).

---

### Full Pipeline: How All Services Connect

| Layer | Service | Role |
|-------|---------|------|
| **Ingestion** | Kinesis Data Streams | Real-time event capture |
| **Delivery** | Amazon Data Firehose | Buffer + format convert + deliver to S3 |
| **Storage** | Amazon S3 | Raw and processed data lake |
| **Catalog** | AWS Glue Data Catalog | Schema discovery and metadata |
| **Governance** | AWS Lake Formation | Centralized access control |
| **Processing** | Amazon EMR (Spark) | Large-scale batch ETL transformation |
| **Warehouse** | Amazon Redshift | Curated, high-performance analytical store |
| **Ad-hoc Query** | Amazon Athena | SQL on raw S3 data without loading |
| **Visualization** | Amazon QuickSight | Dashboards, reports, and ML insights |

---

### Key Takeaways

- **Local testing first** (Floci + LocalStack) is a discipline that saves both time and money - validate the IaC architecture locally before touching real AWS.
- **Kinesis Data Streams vs. Firehose** is a common point of confusion: Streams = low latency + custom consumers; Firehose = zero-management delivery into data stores.
- **Lake Formation** is the answer to "how do we govern access to a data lake across multiple teams" - it replaces the chaos of individual S3 bucket policies.
- **Redshift vs. Athena** is about workload pattern: ad-hoc on S3 = Athena, high-concurrency repeated queries on structured data = Redshift.
- **QuickSight's SPICE engine** is what makes dashboards feel instant - pre-aggregation is the key to interactive BI at scale.
- Seeing all these services connected in a single pipeline diagram made the AWS data platform ecosystem much more intuitive than studying each service in isolation.

---

*Source: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
