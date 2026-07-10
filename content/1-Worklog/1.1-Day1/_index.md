---
title: "Day 1"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

> **Day 1 - Monday, April 20, 2026:** Account activation, AWS credits, and completing 5 hands-on tasks.

---

### Objectives for the Day

- Create and configure an AWS account, receive **$100 starter credit** from AWS.
- Complete **5 hands-on tasks** to accumulate an additional **$100 credit** (+$20 each).
- Get hands-on experience with 5 core AWS services: EC2, Bedrock, Budgets, Lambda, RDS.
- Understand the fundamentals of AWS security and cost management.

---

### Task 1: Launch EC2 Instance - +$20 Credit

**Goal:** Create and manage a Virtual Machine on AWS Cloud.

**Amazon EC2 (Elastic Compute Cloud)** is AWS's core compute service, allowing you to launch virtual servers with flexible hardware configurations and operating systems.

**Steps:**
1. Access **AWS Console** → "Explore AWS" widget → Select task **Launch an instance using EC2**.
2. Click **Start activity** to begin.
3. Set Instance name: `Test Instance` → Select an appropriate AMI.
4. Keep default hardware configuration.
5. Create a new Key Pair: Name `first-kp` | Type **RSA** | Format **.pem**.
6. Create a Security Group with default rules.
7. Review configuration → **Launch Instance**.
8. **Clean up:** Terminate the instance after completing to avoid unnecessary charges.

> **Lesson:** Always clean up resources immediately after practice sessions to keep costs under control. An EC2 instance left running overnight can burn through credits faster than expected.

---

### Task 2: Amazon Bedrock Playground - +$20 Credit

**Goal:** Experience AI/ML with Foundation Models on AWS.

**Amazon Bedrock** is a fully managed service providing access to leading foundation models (Claude, Llama, Titan, and more) through a single unified API - without having to manage any underlying infrastructure.

**Steps:**
1. Access **Bedrock Console** → Select task **Use a foundation model in Amazon Bedrock**.
2. Select model: **Claude 3 Haiku** (good balance between performance and cost).
3. Fill in use case details and submit - wait for approval. If you encounter an authorization error, create a **Support Case** with AWS (Category: Bedrock Allowlisting).
4. Once approved: Write a test prompt → **Run** → Review the result → **Finish**.

> **Lesson:** Some AWS AI services require use case approval before access is granted - this is AWS's mechanism for enforcing Responsible AI principles. Submitting a clear, legitimate use case description significantly speeds up the approval process.

---

### Task 3: Set Up AWS Budgets - +$20 Credit

**Goal:** Establish a cost monitoring and alerting system.

**AWS Budgets** allows you to set cost thresholds and automatically receive notifications when you're approaching or exceeding limits - preventing billing surprises at the end of the month.

**Steps:**
1. Access **Budgets Console** → Select task **Set up a cost budget using AWS Budgets**.
2. Click **Start activity** → Configure budget parameters.
3. Enter an email address to receive notifications → **Create budget**.
4. Budget is successfully created with automatic email alerts configured.

> **Lesson:** Setting up budgets on Day 1 is non-negotiable in an internship environment where AWS credits are finite. A single misconfigured resource running overnight can consume a week's worth of credit.

---

### Task 4: Create Lambda Web App - +$20 Credit

**Goal:** Build a serverless web application on AWS.

**AWS Lambda** is a serverless compute service - runs code without managing servers, and you only pay for actual execution time. No idle capacity, no over-provisioning.

**Steps:**
1. Access **Lambda Console** → Select task **Create a web app using AWS Lambda**.
2. Click **Start activity** → Select **Use a blueprint** → **Getting started with Lambda HTTP**.
3. Set function name: `http-function-url-tutorial`.
4. Tick **Acknowledgment** → **Create function** successfully.
5. **Clean up:** Delete the function after completing.

> **Lesson:** Serverless architecture removes the need for server provisioning entirely - ideal for workloads with irregular traffic patterns. A Lambda function that receives 0 requests costs $0.

---

### Task 5: Create RDS Database - +$20 Credit

**Goal:** Set up a managed relational database on AWS.

**Amazon RDS (Relational Database Service)** manages the entire database lifecycle: automated backups, OS patching, scaling, and failover - so developers can focus on schema design and queries.

**Steps:**
1. Access **RDS Console** → Select task **Create an Amazon RDS Database**.
2. Select **Easy create** → Engine: **Aurora (PostgreSQL Compatible)**.
3. **Create database** → Wait for status to change to **Available**.
4. **Clean up (in the correct order):** Delete instance `database-1-instance-1` first, then delete cluster `database-1`.

> **Important:** The instance must be deleted before the cluster - attempting to delete the cluster first will result in a dependency error that blocks deletion.

---

### Key Takeaways

- **$200 AWS Credit** successfully accumulated: $100 starter + $100 from 5 tasks.
- **5 core AWS services** experienced hands-on: EC2, Bedrock, Budgets, Lambda, RDS.
- Understood AWS's **Responsible AI** policy - model access isn't automatic; it requires approval.
- Established the habit of **"clean up after every session"** - the most important cost-saving discipline in a credit-limited environment.
- Recognized that the AWS Console's guided tasks are an excellent low-risk way to explore services for the first time.

---

*Source: [First Cloud Journey - AWS Study Group](https://cloudjourney.awsstudygroup.com/)*
