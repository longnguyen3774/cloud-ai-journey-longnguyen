---
title: "Day 4"
date: 2026-05-05
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

> **Day 4 - Tuesday, May 5, 2026:** Serverless compute with AWS Lambda, Infrastructure as Code with AWS CDK, developer tooling with AWS Toolkit for VS Code, and hands-on S3 file management with Lifecycle Policies.

---

### Objectives for the Day

- Understand **AWS Lambda's** event-driven execution model and when to use serverless architecture.
- Learn **AWS CDK** as a modern Infrastructure as Code tool using real programming languages.
- Explore **AWS Toolkit for VS Code** to develop and test Lambda functions without leaving the editor.
- Understand **EBS Data Lifecycle Manager** for automated snapshot management.
- Hands-on: Create S3 buckets, upload files via CLI, and configure **Lifecycle Policies** to automate cost-optimized storage tiering.

---

### Theory: AWS Lambda - Serverless Compute

**AWS Lambda** is an event-driven, serverless compute service - you write code, Lambda handles everything else: server provisioning, OS patching, scaling, and high availability.

**Execution model:**
- **Function → Handler → Runtime**: Your code runs inside a handler function in a supported runtime (Node.js, Python, Java, Go, Ruby, .NET).
- **Invocation types:** Synchronous (API Gateway, direct invoke) vs. Asynchronous (S3 events, SNS) vs. Event source mapping (SQS, DynamoDB Streams, Kinesis).

**Key features:**
- **Triggers:** API Gateway, S3 events, DynamoDB Streams, SQS, CloudWatch Events/EventBridge, SNS, Cognito, ALB.
- **Concurrency:** 
  - *Reserved concurrency*: Guarantees a maximum number of concurrent executions for a function.
  - *Provisioned concurrency*: Pre-warms Lambda instances to eliminate cold starts for latency-sensitive workloads.
- **Layers:** Package shared libraries and dependencies as Lambda Layers - reusable across multiple functions, keeping deployment packages small.
- **Environment Variables + Secrets Manager:** Store configuration safely - never hardcode credentials in Lambda code.
- **Lambda URLs:** Direct HTTPS endpoints for Lambda without needing API Gateway - simpler for single-function APIs.

**Pricing:**
- Charged per **number of requests** + **duration** (memory × execution time, billed in 1ms increments).
- Memory: 128 MB to 10 GB.
- Maximum timeout: **15 minutes**.
- **Free Tier:** 1 million requests and 400,000 GB-seconds per month - Lambda is extremely cost-effective for low-to-medium traffic workloads.

> **Lesson:** Lambda's event-driven model is the backbone of modern serverless architectures on AWS - it integrates natively with nearly every AWS service. The key mental shift is thinking in *events* rather than *always-on servers*.

---

### Theory: AWS CDK (Cloud Development Kit)

**AWS CDK** lets you define cloud infrastructure using familiar programming languages (TypeScript, Python, Java, C#, Go) - which then synthesizes down to CloudFormation templates under the hood.

**Core concepts:**
- **Constructs:** The basic building blocks of CDK applications.
  - *L1 Constructs*: Direct 1:1 wrappers around CloudFormation resources - maximum control, maximum verbosity.
  - *L2 Constructs*: Higher-level abstractions with sensible defaults - e.g., `aws_s3.Bucket` automatically handles encryption, versioning options.
  - *L3 Constructs (Patterns)*: Complete, opinionated solutions - e.g., an API Gateway backed by Lambda with DynamoDB, all in a few lines.
- **Stacks:** The unit of deployment, mapping 1:1 to a CloudFormation stack.
- **Apps:** The root CDK program containing one or more stacks.

**CDK workflow:**
```bash
# Initialize a new CDK project
cdk init app --language typescript

# Preview the CloudFormation template that CDK will generate
cdk synth

# Deploy to AWS
cdk deploy

# Tear down all resources
cdk destroy
```

**CDK advantages over raw CloudFormation:**
- **Type safety:** IDE catches configuration errors before deployment.
- **Autocomplete:** Your editor knows what properties are valid for each resource.
- **Logic:** Use real conditionals, loops, and functions - not CloudFormation's limited `Conditions` and `Mappings`.
- **Reusable constructs:** Share infrastructure patterns across teams as npm/PyPI packages.

---

### Theory: AWS Toolkit for VS Code

**AWS Toolkit for VS Code** is an IDE extension that enables direct interaction with AWS services from within VS Code - reducing context switching between editor and Console.

**Features explored:**
- Browse, invoke, and view logs of **Lambda functions** directly from the Explorer panel.
- Upload and download **S3 objects** without opening the AWS Console.
- Get syntax validation and schema support for **CloudFormation** and **CDK** templates.
- Debug Lambda functions **locally** using the integrated **AWS SAM CLI** - run and step through Lambda code on your laptop before deploying.
- Manage **AWS credentials and profiles** within the IDE.

---

### Theory: Amazon EBS Data Lifecycle Manager (DLM)

**Amazon EBS DLM** automates the creation, retention, and deletion of **EBS snapshots** - the backup mechanism for EC2 volume data.

**Key capabilities:**
- **Lifecycle policies:** Schedule snapshot creation (hourly, daily, weekly).
- **Retention rules:** Keep the N most recent snapshots OR keep snapshots for N days.
- **Cross-region copy:** Automatically copy snapshots to a disaster recovery region.
- **Fast Snapshot Restore (FSR):** Pre-warm snapshots so volumes restored from them immediately deliver full performance - critical for databases where cold-start I/O degradation is unacceptable.

> **Lesson:** DLM is the "set it and forget it" backup strategy for EBS - configure DLM policies for all production volumes from Day 1. A missing snapshot is only discovered when you actually need it.

---

### Hands-On: S3 File Management with Lifecycle Policies

#### 1. Create S3 Bucket via CLI

```bash
aws s3api create-bucket \
  --bucket my-day4-bucket-$(date +%s) \
  --region ap-southeast-1 \
  --create-bucket-configuration LocationConstraint=ap-southeast-1
```

#### 2. Upload Files from Local Machine

```bash
# Upload a single file
aws s3 cp ./local-file.txt s3://my-day4-bucket/

# Sync an entire directory (only uploads changed files)
aws s3 sync ./local-folder/ s3://my-day4-bucket/data/

# Verify after upload
aws s3 ls s3://my-day4-bucket/ --recursive
```

#### 3. Configure S3 Lifecycle Policies

| Rule | Action | When Applied |
|------|--------|-------------|
| Archive infrequently accessed data | Transition to S3 Standard-IA | After **30 days** |
| Archive old data | Transition to S3 Glacier Instant Retrieval | After **90 days** |
| Expire stale objects | Permanent deletion | After **365 days** |
| Clean up incomplete multipart uploads | Abort | After **7 days** |

**Apply lifecycle policy via AWS CLI:**
```bash
aws s3api put-bucket-lifecycle-configuration \
  --bucket my-day4-bucket \
  --lifecycle-configuration file://lifecycle-policy.json
```

#### 4. Clean Up After Practice

```bash
# Remove all objects first
aws s3 rm s3://my-day4-bucket/ --recursive

# Then delete the bucket
aws s3api delete-bucket --bucket my-day4-bucket
```

> ⚠️ An S3 bucket cannot be deleted while it still contains objects - always empty it first.

> **Lesson:** S3 Lifecycle policies are a zero-cost-overhead strategy for dramatically reducing storage costs - automatically moving aging data to progressively cheaper storage classes. This is the correct approach for any long-term data retention requirement.

---

### Key Takeaways

- **Lambda's event-driven model** changes how you think about compute: instead of managing servers, you think in terms of *what triggers my code*.
- **AWS CDK** is the modern IaC choice for teams comfortable with programming languages - it makes infrastructure as composable and testable as application code.
- The **AWS Toolkit for VS Code** significantly tightens the developer feedback loop - local Lambda debugging is a game-changer for development speed.
- **S3 Lifecycle policies** are non-negotiable for any data platform storing historical data - without them, costs compound silently over time as data accumulates.
- **EBS DLM** is the snapshot equivalent of S3 lifecycle policies - automate it on Day 1, never worry about missing backups again.

---

*Source: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
