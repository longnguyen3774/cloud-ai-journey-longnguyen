---
title: "Event 2"
date: 2026-05-23
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Event Report: AWS FIRST CLOUD AI JOURNEY COMMUNITY DAY

### Event Information

| Field | Details |
|-------|---------|
| **Event Name** | AWS FIRST CLOUD AI JOURNEY COMMUNITY DAY |
| **Date & Time** | 09:00, May 23, 2026 |
| **Location** | 26th Floor, Bitexco Tower, 02 Hai Trieu Street, Saigon Ward, Ho Chi Minh City |
| **Role** | Attendee |

---

### Speakers

- **Tinh Truong**
- **Anh Pham**
- **Thinh Nguyen**
- **Mai Nguyen**, **Uyen Le**, **Thao Nguyen** *(team)*
- **Duc Dao**
- **Vy Lam**

---

### Event Schedule

| Time | Topic | Speaker |
|------|-------|---------|
| 09:00 – 09:30 | Context Is Everything: Making AI Actually Work for You | Tinh Truong |
| 09:30 – 09:45 | Friendly AI Assistant with Amazon QuickSight Q | Anh Pham |
| 09:45 – 10:25 | From Edge To Origin: CloudFront as Your Foundation | Thinh Nguyen |
| 10:25 – 10:55 | 36 hrs with LotusHacks – Building UTMorpho from Idea to Reality | Mai Nguyen, Uyen Le, Thao Nguyen |
| 10:55 – 11:00 | ☕ Break | - |
| 11:00 – 11:30 | Non-Determinism of "Deterministic" LLM Settings | Duc Dao |
| 11:30 – 12:00 | Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring | Vy Lam |

---

### Session Summaries

#### 1. Context Is Everything: Making AI Actually Work for You
*Speaker: Tinh Truong*

This session centered on a truth many AI users overlook: **the problem isn't the model - it's the quality of context you provide**.

**Why does AI often give poor results?**
- AI cannot infer your goal on its own - it works only with what you give it.
- Misaligned, verbose, or constraint-missing answers all stem from poor context, not a weak model.

**What makes good context?**
- **Goal**: What outcome do you want?
- **Situation**: Who are you, what stage is the project at?
- **Constraints**: Tech stack, style, budget, format.
- **Relevant evidence**: Code, documents, real examples.

**Three common context mistakes:**
- Pasting entire documents into one chat - dilutes the signal and wastes tokens.
- Providing obvious information the AI already knows - wastes useful context space.
- Vague prompts - produce vague answers.

**The evolution of AI usage:**
`Prompt → Context → Memory (Second AI Brain)` - a system that stores and learns from your notes and projects to answer with increasing precision over time.

**Framework before asking AI:** Goal + Relevant info + Constraints + Success criteria.

> *"The future isn't humans vs. AI - it's people who know how to work with AI vs. those who don't."*

---

#### 2. Friendly AI Assistant with Amazon QuickSight Q
*Speaker: Anh Pham*

**Amazon Quick Suite** - an agentic AI toolkit that helps businesses move from data to action quickly, without writing code.

**Key components:**
- **Quick Chat Agent**: AI assistant for exploring and analyzing data through conversation.
- **Quick Flows**: Create intelligent workflows using natural language - no coding required.
- **Quick Spaces**: Shared collaborative spaces that turn individual insights into team knowledge.
- **Quick Sight**: Build dashboards and reports from raw data using natural language.

**Technical infrastructure:**
- Connects to 40+ data sources (databases, data warehouses, file uploads...).
- Integrates Amazon Bedrock and web search.
- Executes thousands of actions on third-party apps via API.
- Enterprise-grade data governance and security built in.

**Typical use case - PM Assistant:**
Automatically generate meeting minutes → send notification emails → schedule follow-up meetings, all without manual effort.

---

#### 3. From Edge To Origin: CloudFront as Your Foundation
*Speaker: Thinh Nguyen*

**Amazon CloudFront** was presented not just as a CDN, but as a **comprehensive protection and optimization foundation** for any workload.

**Global infrastructure:**
700+ Points of Presence (PoPs) across 50+ countries, connected directly to major ISPs via AWS's private fiber backbone.

**Key update - Flat-rate Pricing** *(launched November 19, 2025)*:
Addresses the risk of bill spikes during DDoS attacks or viral content events. Three tiers: Free/Pro, Business, Premium - each bundling CloudFront, WAF, Anti-DDoS, Route 53, and S3.

**Multi-layer security:**
- Blocks DDoS attacks at the Edge - no 3–4 minute mitigation delay like traditional solutions.
- SYN Proxy against SYN Flood attacks.
- Origin Cloaking (VPC Origin / OAC) to completely hide infrastructure from the public internet.
- Geo restrictions, Signed URLs/Cookies, TLS 1.3, mTLS.

**Performance & cost optimization:**
- Free data transfer from AWS Origin to CloudFront.
- Multi-tier caching with Request Collapsing - millions of user requests collapsed into one origin request.
- HTTP/3 (QUIC) support, up to 82% compression reduction, Persistent Connections, and Edge Computing (CloudFront Functions / Lambda@Edge).

---

#### 4. 36 hrs with LotusHacks – Building UTMorpho from Idea to Reality
*Speakers: Mai Nguyen, Uyen Le, Thao Nguyen*

The story of a 36-hour sprint at LotusHacks 2026 to build **UTMorpho** - an AI-powered UI generator with direct WYSIWYG canvas editing.

**What problem does UTMorpho solve?**
- Instead of re-prompting AI to change a button color or size, users edit directly on the canvas.
- Changes only affect the selected element - preserving the rest of the design.
- Smart diffing means small edits consume minimal tokens, reducing cost.

**The 36-hour journey:**
1. Left the hackathon venue to find creative space - the idea came from real frustration with existing AI tools.
2. Rapid task division based on team chemistry.
3. Backend scaffold → first AI calls → UI generator → inline editor → state sync.
4. Hit token limits, cut features to focus on a solid core demo.
5. Delivered a 5-minute pitch to the judges.

**Key lessons:**
- The best ideas come from your own daily frustrations.
- Team chemistry replaces dozens of meetings and processes.
- Stepping away from pressure sometimes unlocks better ideas.
- Treat AI (Claude, Bedrock) as a teammate, not just a tool.

---

#### 5. Non-Determinism of "Deterministic" LLM Settings
*Speaker: Duc Dao*

A rarely discussed but critical topic for anyone building products with LLMs: **Temperature = 0 does not guarantee the same output every time**.

**How it works:**
LLMs generate text token by token - compute logits → softmax → sample the next token. At temp=0, theory says the model always picks the highest-probability token (argmax). Reality is more complicated.

**Empirical evidence** *(research on GPT-3.5 Turbo, GPT-4o, Llama-3, Mixtral)*:
- Accuracy can vary by up to **15%** between identical runs.
- Best-to-worst result gap can reach **70%**.
- Exact text match rate near 0% on hard tasks.

**Root causes:**
- **Technical**: Floating-point arithmetic on GPUs is not associative → tiny rounding errors in logits → enough to flip the argmax result.
- **Commercial**: API providers batch requests from multiple users to optimize throughput - your computation changes depending on what else is in the batch.

**Mitigation strategies:**
- Majority voting: run the same prompt multiple times, pick the most common answer.
- Use Structured Output (JSON mode / function calling) to narrow the response space.
- Self-hosted: full control over infrastructure and inference parameters.
- Design systems that tolerate variation from the start.

> 💡 *Practical tip: Use temp=0.1 instead of 0 to avoid repetitive loops while maintaining high stability.*

> ⚠️ *Never rely on temp=0 for critical applications (medical, legal, financial). LLMs are probabilistic by nature - design systems accordingly.*

---

#### 6. Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring
*Speaker: Vy Lam*

A deep dive into building a **Multi-Agent System** for startup credit scoring - a domain where traditional banking systems routinely fail.

**The core problem:**
Traditional credit systems require 3+ years of financials and physical collateral. Startups typically have 6–18 months of history, IP-based assets, and unstructured data - evaluation requires deep expertise across multiple domains simultaneously.

**Why Multi-Agent over Single Agent?**
- Single agents suffer from expertise dilution, context limits, and no built-in checks and balances.
- **Virtual Credit Committee** with specialized agents:
  - Manager: Overall coordination
  - Financial Analyst: Cash flow, burn rate, unit economics
  - Market Analyst: TAM/SAM/SOM, competitive landscape
  - Team Evaluator: Founder track record
  - Risk & Compliance Specialist: Policy adherence and risk mitigation

**6 enterprise-grade pillars:** Security, Data Governance, Networking (VPC), Operations, Human Factors, Compliance (SOC 2, GDPR, PCI DSS).

**Impressive ROI:**
| Metric | Before | After |
|--------|--------|-------|
| Processing time | 2–3 weeks | 2–4 hours *(95% reduction)* |
| Cost per decision | ~$4,000 USD | <$200 USD |
| Approval rate | 15–20% | 35–45% |

**AWS implementation:** Amazon Bedrock, AgentCore, Docker (ECR), API Gateway, VPC isolation, IAM least-privilege.

---

### Key Takeaways

#### On AI and effective usage
- **Context is king**: A clear, well-constrained prompt always outperforms any powerful model used carelessly.
- **Don't trust "determinism" blindly**: LLMs are probabilistic - system design must embrace that reality.
- **Agentic AI** doesn't just answer questions - it takes actions. This is a paradigm shift worth catching early.

#### On building products
- The best ideas come from real pain - UTMorpho is living proof.
- Going from POC to production requires investing in security, governance, and operations - not just business logic.
- AI is a teammate, not just a tool - how you collaborate with AI determines your actual output quality.

#### On the learning journey
- Community Day proves: a community learning together creates value that no textbook can replicate.
- Every speaker shared from lived experience - no dry theory, all lessons from actually doing.

---

### Personal Reflection

This Community Day was unlike any typical conference. From AI context strategies to a 36-hour hackathon story, from LLM non-determinism to enterprise multi-agent systems - every session was delivered by someone who had actually built it, shipped it, or learned it the hard way.

What I take away isn't just technical knowledge, but a **new lens**: AI isn't magic or scary - it's a powerful tool whose effectiveness depends almost entirely on the person wielding it.

---

### Event Photos

![AWS First Cloud AI Journey Community Day](/images/event2.jpg)
