astructure-as-code--deployment)
11. [Exam Mindset & Tips](#11-exam-mindset--tips)

---

## 1️⃣ Identity & Security (VERY IMPORTANT)

### 🎯 IAM (Identity and Access Management) - The Foundation

**Think of IAM as:** The security guard system of your AWS account. It controls WHO can access WHAT.

#### IAM Users
- **What:** Individual people or applications that need AWS access
- **Example:** John (developer), Sarah (admin), or a monitoring application
- **Best Practice:** Each person gets their own user (never share credentials!)
- **Access:** Username + password (console) OR Access Key ID + Secret (programmatic)

#### IAM Groups
- **What:** Collections of users with similar job functions
- **Why:** Easier to manage permissions for multiple people at once
- **Example:** 
  - "Developers" group → all developers get the same permissions
  - "Admins" group → all admins get admin permissions
- **Rule:** Users can belong to multiple groups

#### IAM Roles
- **What:** Temporary permissions that AWS services or users can "assume"
- **Key Difference from Users:** No permanent credentials! Temporary security tokens.
- **Common Use Cases:**
  1. **EC2 accessing S3:** EC2 instance assumes a role to read S3 buckets
  2. **Cross-account access:** Account A's user assumes role in Account B
  3. **Lambda execution:** Lambda function assumes role to access DynamoDB
- **Why Roles > Access Keys:** More secure, automatically rotated, no hardcoded credentials

#### IAM Policies - The Permission Documents

**Two Types You MUST Know:**

| **Managed Policies** | **Inline Policies** |
|---------------------|---------------------|
| Standalone policy objects | Embedded directly in user/group/role |
| Can be attached to multiple entities | 1:1 relationship with entity |
| AWS-managed or Customer-managed | When deleted, entity loses it |
| Reusable and centrally managed | Use for exceptions only |
| **PREFERRED by AWS** | Harder to track and manage |

**Policy Structure (JSON):**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",           // Allow or Deny
      "Action": "s3:GetObject",    // What action
      "Resource": "arn:aws:s3:::my-bucket/*"  // On what resource
    }
  ]
}
```

**Policy Evaluation Logic:**
1. **Explicit DENY** → Always wins (highest priority)
2. **Explicit ALLOW** → Grants permission
3. **Default:** Everything is denied by default

#### Trust Policies & Role Assumption

**Trust Policy:** Defines WHO can assume a role (attached to roles only)

**Example Scenario:**
- EC2 instance needs to read from S3
- Create IAM role with:
  - **Trust Policy:** "EC2 service can assume this role"
  - **Permission Policy:** "This role can read S3"
- Attach role to EC2 → EC2 can now access S3!

**Role Assumption Process:**
1. Entity requests to assume role
2. AWS checks trust policy
3. If allowed, AWS issues temporary credentials (15 min - 12 hours)
4. Entity uses temporary credentials

#### MFA (Multi-Factor Authentication)

**What:** Second layer of security beyond password
- **Something you know:** Password
- **Something you have:** MFA device (phone app, hardware token)

**Types:**
- Virtual MFA (Google Authenticator, Authy) - Most common
- Hardware MFA (physical device)
- U2F Security Key (YubiKey)

**Best Practices:**
- ✅ Enable MFA for root user (MANDATORY!)
- ✅ Enable MFA for privileged IAM users
- ✅ Enforce MFA with IAM policies

#### Password Policies

Configure requirements for IAM user passwords:
- Minimum length (e.g., 12 characters)
- Require uppercase, lowercase, numbers, symbols
- Password expiration (e.g., 90 days)
- Prevent password reuse
- Allow users to change their own passwords

---

### 🏢 AWS Organizations

**What:** Manage multiple AWS accounts from a single organization

**Structure:**
```
Root (Management Account)
├── Organizational Unit: Production
│   ├── Account: Prod-Web
│   └── Account: Prod-Database
└── Organizational Unit: Development
    ├── Account: Dev-Team1
    └── Account: Dev-Team2
```

**Benefits:**
- Consolidated billing (one bill for all accounts)
- Volume discounts across accounts
- Centralized security policies (SCPs)
- Easy account creation

#### SCPs (Service Control Policies) 🔥

**What:** Maximum permissions for accounts in your organization
**Key Point:** SCPs DON'T grant permissions, they LIMIT them!

**Think of it as:** A fence around your accounts

**Example:**
- SCP says: "No one can use EC2 in eu-west-1 region"
- Even if IAM policy allows it → DENIED
- SCP + IAM policy = Actual permissions

**Common Use Cases:**
- Restrict regions (e.g., only use us-east-1 and us-west-2)
- Prevent account closure
- Enforce MFA
- Block specific services (e.g., no cryptocurrency mining)

**Exam Tip:** SCPs affect ALL users and roles in the account, EXCEPT the management account root user.

---

### 🔐 Encryption & Key Management

#### KMS (Key Management Service)

**What:** Managed service to create and control encryption keys

**Key Concepts:**
- **CMK (Customer Master Key):** The encryption key you manage
- **Data Key:** Key used to actually encrypt your data (generated by CMK)
- **Envelope Encryption:** Encrypt data with data key, encrypt data key with CMK

**Types of CMKs:**

| **AWS Managed CMK** | **Customer Managed CMK** |
|---------------------|--------------------------|
| Created by AWS services | You create and manage |
| Free | $1/month |
| Automatic rotation (3 years) | Optional rotation (1 year) |
| Limited control | Full control over policies |

**Common Use Cases:**
- Encrypt EBS volumes
- Encrypt S3 objects (SSE-KMS)
- Encrypt RDS databases
- Encrypt secrets in Secrets Manager

**Exam Scenario:**
> "Company needs to encrypt data at rest with full control over key rotation and access policies"
> **Answer:** Customer Managed CMK in KMS

---

### 🛡️ AWS Shield & WAF

#### AWS Shield

**What:** DDoS (Distributed Denial of Service) protection

**Two Tiers:**

| **Shield Standard** | **Shield Advanced** |
|---------------------|---------------------|
| FREE | $3,000/month |
| Automatic protection | 24/7 DDoS Response Team |
| Layer 3/4 protection | Layer 3/4/7 protection |
| All AWS customers | Cost protection (credits for scaling) |
| | Real-time attack notifications |

**When to use Advanced:**
- High-value applications
- Need cost protection during attacks
- Require DDoS experts on standby

#### AWS WAF (Web Application Firewall)

**What:** Protects web applications from common exploits (Layer 7)

**Protects Against:**
- SQL injection
- Cross-site scripting (XSS)
- IP address blocking
- Geographic restrictions
- Rate limiting (e.g., max 2000 requests/5 min)

**Works With:**
- CloudFront
- Application Load Balancer
- API Gateway
- AppSync

**Web ACL (Access Control List):** Rules that define what traffic to allow/block

---

### 🔑 Secrets Management

#### Secrets Manager vs Parameter Store

| Feature | **Secrets Manager** | **Parameter Store** |
|---------|---------------------|---------------------|
| **Purpose** | Store secrets (passwords, API keys) | Store configuration data |
| **Automatic Rotation** | ✅ Yes (Lambda-based) | ❌ No |
| **Cost** | $0.40/secret/month + API calls | Free (Standard), $0.05 (Advanced) |
| **Encryption** | Always encrypted (KMS) | Optional encryption |
| **Cross-region** | ✅ Replicate secrets | ❌ No |
| **Best For** | Database credentials, API keys | App configs, feature flags |

**Exam Tip:** If question mentions "automatic rotation" → Secrets Manager

**Secrets Manager Rotation:**
1. Secrets Manager calls Lambda function
2. Lambda creates new password
3. Lambda updates database
4. Lambda updates secret in Secrets Manager
5. Old password still works (grace period)
6. After verification, old password removed

---

### 📊 CloudTrail - The Audit Log

**What:** Records ALL API calls in your AWS account (who did what, when, where)

**Key Points:**
- **Enabled by default** (90 days retention)
- For longer retention → Create trail → Store in S3
- **Use Cases:**
  - Security analysis
  - Compliance auditing
  - Troubleshooting
  - Operational issue tracking

**What's Logged:**
- Who made the request (user, role)
- When (timestamp)
- What action (API call)
- Which resource
- Source IP address
- Response (success/failure)

**Exam Scenarios:**
> "How to identify who deleted an S3 bucket?"
> **Answer:** Check CloudTrail logs

> "Compliance requires 7 years of API activity logs"
> **Answer:** CloudTrail → S3 → Glacier for long-term storage

**CloudTrail vs CloudWatch:**
- **CloudTrail:** WHO did WHAT (audit trail)
- **CloudWatch:** HOW is it performing (metrics, logs)

---

### 🤝 Shared Responsibility Model 🔥

**Critical for Exam:** AWS is responsible for security OF the cloud, you're responsible for security IN the cloud.

| **AWS Responsibility** | **Customer Responsibility** |
|------------------------|----------------------------|
| Physical data centers | Data encryption |
| Hardware | IAM users, groups, roles |
| Network infrastructure | Security group rules |
| Managed service operations | Application code |
| Regions, AZs | Operating system patches (EC2) |
| | Firewall configuration |
| | Network traffic protection |

**Examples:**

**S3:**
- AWS: Physical security, hardware, network
- You: Bucket policies, encryption, versioning, access control

**EC2:**
- AWS: Physical host, network, hypervisor
- You: OS patches, security groups, IAM roles, data encryption

**RDS:**
- AWS: OS patches, database software patches, backups
- You: Database user permissions, encryption, security groups

**Lambda:**
- AWS: Everything infrastructure (highly managed)
- You: Code, IAM roles, environment variables

**Exam Tip:** More managed service = Less customer responsibility

---

## 🎓 Section 1 Summary - Key Takeaways

✅ **IAM:**
- Users (people), Groups (collections), Roles (temporary permissions)
- Managed policies > Inline policies
- Always enable MFA for root and privileged users

✅ **Organizations & SCPs:**
- SCPs limit maximum permissions (don't grant)
- SCPs don't affect management account root user

✅ **Encryption:**
- KMS for encryption key management
- Customer Managed CMK for full control

✅ **Protection:**
- Shield for DDoS (Standard free, Advanced paid)
- WAF for Layer 7 web attacks

✅ **Secrets:**
- Secrets Manager for automatic rotation
- Parameter Store for configuration

✅ **Auditing:**
- CloudTrail logs all API calls (who did what)

✅ **Shared Responsibility:**
- AWS = Security OF the cloud
- You = Security IN the cloud

---

*[Continue to Section 2: Compute Services →]*

---

## 2️⃣ Compute Services

### 🖥️ EC2 (Elastic Compute Cloud) - Virtual Servers in the Cloud

**What:** Rent virtual servers (instances) in AWS data centers

**Think of it as:** Renting a computer in AWS that you can configure and control

---

#### EC2 Instance Types & Families 🔥

AWS has 400+ instance types, but they follow a naming pattern:

**Format:** `[Family][Generation].[Size]`
**Example:** `m5.xlarge`
- `m` = Family (general purpose)
- `5` = Generation (5th generation)
- `xlarge` = Size (4 vCPUs, 16 GB RAM)

**Main Instance Families (Remember: FIGHT DR MC PX):**

| Family | Name | Use Case | Example |
|--------|------|----------|---------|
| **F** | FPGA | Hardware acceleration | Genomics, financial analytics |
| **I** | Storage optimized | High IOPS | NoSQL databases, data warehousing |
| **G** | Graphics | GPU workloads | Machine learning, video rendering |
| **H** | High disk throughput | MapReduce, HDFS | Big data processing |
| **T** | Burstable | Variable workloads | Web servers, dev environments |
| **D** | Dense storage | Massive data | Hadoop, distributed file systems |
| **R** | RAM optimized | Memory intensive | In-memory databases, caching |
| **M** | General purpose | Balanced | Application servers (most common) |
| **C** | Compute optimized | CPU intensive | Batch processing, gaming servers |
| **P** | GPU | ML training | Deep learning, HPC |
| **X** | Extreme memory | Largest RAM | SAP HANA, in-memory databases |

**Most Common for Exam:**
- **T3/T3a:** Burstable, good for variable workloads (websites, dev/test)
- **M5/M5a:** General purpose, balanced compute/memory/network
- **C5/C5n:** Compute optimized, high CPU performance
- **R5/R5a:** Memory optimized, large RAM for databases
- **I3/I3en:** Storage optimized, high IOPS for databases

**Burstable Instances (T2/T3):**
- Baseline CPU performance
- Can "burst" above baseline using CPU credits
- Credits accumulate when below baseline
- Good for: Websites with variable traffic, dev/test

---

#### EC2 Purchasing Options 🔥

| Option | Description | Commitment | Savings | Best For |
|--------|-------------|------------|---------|----------|
| **On-Demand** | Pay per second/hour | None | 0% | Short-term, unpredictable workloads |
| **Reserved (RI)** | 1 or 3 year commitment | 1-3 years | 40-60% | Steady-state applications (databases) |
| **Savings Plans** | Commit to $/hour usage | 1-3 years | 40-60% | Flexible instance types/regions |
| **Spot Instances** | Bid on spare capacity | None | 50-90% | Fault-tolerant, flexible workloads |
| **Dedicated Hosts** | Physical server for you | 1-3 years | Varies | Licensing requirements, compliance |
| **Dedicated Instances** | Instances on dedicated hardware | None | Varies | Isolation from other customers |

**Deep Dive:**

**1. On-Demand:**
- Most expensive
- No upfront payment
- No commitment
- **Use when:** Testing, short-term projects, unpredictable workloads

**2. Reserved Instances (RI):**
- **Standard RI:** Highest discount, can't change instance type
- **Convertible RI:** Lower discount, can change instance type
- **Payment options:**
  - All upfront (highest discount)
  - Partial upfront (medium discount)
  - No upfront (lowest discount)
- Can sell unused RIs on Reserved Instance Marketplace

**3. Savings Plans:**
- Commit to consistent usage (e.g., $10/hour for 1 year)
- **Compute Savings Plan:** Most flexible (any instance, any region, even Lambda/Fargate)
- **EC2 Instance Savings Plan:** Specific instance family in region
- **Exam Tip:** Savings Plans > Reserved Instances for flexibility

**4. Spot Instances:** 🔥
- AWS sells unused EC2 capacity at huge discounts
- **Catch:** AWS can terminate with 2-minute warning if capacity needed
- **Spot Request:** Bid price, instance type, availability zone
- **Spot Fleet:** Collection of Spot + On-Demand instances
- **Use cases:**
  - Batch processing
  - Data analysis
  - Image rendering
  - CI/CD testing
  - Workloads with flexible start/end times
- **DON'T use for:**
  - Databases
  - Critical applications
  - Anything that can't handle interruptions

**Spot Instance Strategies:**
- **Lowest price:** Launch instances in pool with lowest price
- **Diversified:** Distribute across all pools (most available capacity)
- **Capacity optimized:** Launch in pools with optimal capacity

**5. Dedicated Hosts vs Dedicated Instances:**

| Feature | Dedicated Host | Dedicated Instance |
|---------|----------------|-------------------|
| **Visibility** | Physical server visible to you | Not visible |
| **Control** | Control instance placement | No control |
| **Licensing** | Use existing licenses (BYOL) | Can't use BYOL |
| **Cost** | Most expensive | Less expensive |
| **Use case** | Compliance, licensing | Compliance only |

---

#### EC2 Placement Groups 🔥

**What:** Control how instances are placed on physical hardware

**Three Types:**

**1. Cluster Placement Group:**
- **Strategy:** Pack instances close together in same AZ
- **Pros:** 
  - Lowest latency (10 Gbps network)
  - Highest network throughput
- **Cons:** 
  - Single AZ (no high availability)
  - If rack fails, all instances fail
- **Use case:** HPC (High Performance Computing), big data jobs requiring low latency

**2. Spread Placement Group:**
- **Strategy:** Spread instances across different hardware
- **Limit:** Max 7 instances per AZ per group
- **Pros:**
  - Reduced risk of simultaneous failure
  - Can span multiple AZs
  - Each instance on different rack
- **Cons:** Limited to 7 instances per AZ
- **Use case:** Critical applications requiring high availability

**3. Partition Placement Group:**
- **Strategy:** Divide instances into partitions (each partition on different racks)
- **Limit:** Up to 7 partitions per AZ
- **Pros:**
  - Hundreds of instances per group
  - Partitions isolated from each other
  - Can span multiple AZs
- **Cons:** More complex
- **Use case:** Large distributed systems (Hadoop, Cassandra, Kafka)

**Exam Comparison:**

| Need | Solution |
|------|----------|
| Lowest latency between instances | Cluster |
| Maximum availability (small number of instances) | Spread |
| Large distributed system with partition awareness | Partition |

---

#### AMI (Amazon Machine Image)

**What:** Template to launch EC2 instances (like a snapshot of a configured server)

**Contains:**
- Operating system
- Application server
- Applications
- Configuration
- Permissions

**Types:**
- **AWS provided:** Amazon Linux, Ubuntu, Windows, etc.
- **AWS Marketplace:** Pre-configured by vendors (paid or free)
- **Custom AMI:** You create from your configured instance

**AMI Workflow:**
1. Launch EC2 instance
2. Configure it (install software, settings)
3. Create AMI from instance
4. Launch new instances from your AMI (pre-configured!)

**Benefits:**
- Faster deployment (pre-configured)
- Consistency across instances
- Disaster recovery (backup)
- Can share AMIs across accounts/regions

**Regional:** AMIs are region-specific, must copy to other regions

**Exam Tip:** 
> "Need to launch 100 identical web servers quickly"
> **Answer:** Create AMI from configured instance, launch from AMI

---

#### Auto Scaling Groups (ASG) 🔥

**What:** Automatically add/remove EC2 instances based on demand

**Key Components:**

**1. Launch Template/Configuration:**
- AMI to use
- Instance type
- Security groups
- IAM role
- User data script

**2. Scaling Policies:**

| Policy Type | Description | Example |
|-------------|-------------|---------|
| **Target Tracking** | Maintain metric at target | Keep CPU at 50% |
| **Step Scaling** | Scale based on CloudWatch alarm | CPU > 70% add 2, CPU > 90% add 4 |
| **Simple Scaling** | Single scaling action | CPU > 80% add 1 instance |
| **Scheduled** | Scale at specific times | Scale up at 9 AM, down at 6 PM |
| **Predictive** | ML-based forecasting | Predict traffic patterns |

**3. Scaling Limits:**
- **Minimum capacity:** Always run at least X instances
- **Desired capacity:** Target number of instances
- **Maximum capacity:** Never exceed X instances

**Health Checks:**
- **EC2 health check:** Is instance running?
- **ELB health check:** Is application responding?
- Unhealthy instances automatically terminated and replaced

**Cooldown Period:**
- Wait time after scaling activity (default 300 seconds)
- Prevents rapid scaling up/down
- Allows metrics to stabilize

**Exam Scenarios:**

> "Application needs to handle variable traffic, maintain 60% CPU utilization"
> **Answer:** ASG with target tracking policy (60% CPU)

> "Scale up during business hours (9 AM - 6 PM)"
> **Answer:** ASG with scheduled scaling

**ASG + Load Balancer:**
- ASG automatically registers new instances with ELB
- ELB distributes traffic across healthy instances
- Perfect combination for high availability

---

### ⚖️ Elastic Load Balancer (ELB)

**What:** Distributes incoming traffic across multiple targets (EC2, containers, IPs)

**Three Types You MUST Know:**

---

#### 1. Application Load Balancer (ALB) - Layer 7 🔥

**What:** HTTP/HTTPS traffic, application-aware routing

**Key Features:**
- **Path-based routing:** `/api/*` → API servers, `/images/*` → image servers
- **Host-based routing:** `api.example.com` → API servers, `www.example.com` → web servers
- **Query string routing:** `?platform=mobile` → mobile servers
- **HTTP header routing:** Route based on headers
- **WebSocket support**
- **HTTP/2 support**
- **Fixed hostname:** `xxx.region.elb.amazonaws.com`

**Target Types:**
- EC2 instances
- IP addresses
- Lambda functions
- Containers (ECS)

**Use Cases:**
- Microservices architecture
- Container-based applications
- Need advanced routing
- HTTP/HTTPS applications

**Exam Tip:** If question mentions "routing based on URL path" or "microservices" → ALB

---

#### 2. Network Load Balancer (NLB) - Layer 4

**What:** TCP/UDP traffic, ultra-high performance

**Key Features:**
- **Extreme performance:** Millions of requests/second
- **Low latency:** ~100 microseconds (vs ALB ~400ms)
- **Static IP:** One static IP per AZ (can assign Elastic IP)
- **Preserve source IP:** Clients see real client IP
- **TCP/UDP/TLS traffic**
- **No security groups** (traffic passes through)

**Use Cases:**
- Extreme performance requirements
- Static IP needed (whitelisting)
- TCP/UDP applications (not HTTP)
- Gaming servers
- IoT applications

**Exam Tip:** If question mentions "millions of requests," "static IP," or "TCP traffic" → NLB

---

#### 3. Classic Load Balancer (CLB) - Legacy

**What:** Old generation, supports Layer 4 & 7

**Status:** Being phased out, use ALB or NLB instead

**Exam Tip:** If CLB appears in answer choices, usually NOT the right answer

---

**ELB Comparison Table:**

| Feature | ALB | NLB | CLB |
|---------|-----|-----|-----|
| **Layer** | 7 (HTTP/HTTPS) | 4 (TCP/UDP) | 4 & 7 |
| **Performance** | Good | Extreme | Moderate |
| **Latency** | ~400ms | ~100µs | Higher |
| **Static IP** | ❌ | ✅ | ❌ |
| **Path routing** | ✅ | ❌ | ❌ |
| **WebSocket** | ✅ | ✅ | ❌ |
| **Target types** | EC2, IP, Lambda | EC2, IP | EC2 |
| **Use case** | Web apps, microservices | High performance, TCP | Legacy |

**Cross-Zone Load Balancing:**
- **Enabled:** Distributes traffic evenly across all instances in all AZs
- **Disabled:** Distributes traffic only within each AZ
- **ALB:** Enabled by default (no charge)
- **NLB:** Disabled by default (charges for cross-AZ traffic)

**Sticky Sessions (Session Affinity):**
- Same client always routed to same instance
- Uses cookies (application-based or load balancer-generated)
- **Use case:** User session data stored on specific instance
- **Downside:** Can cause uneven load distribution

**Connection Draining / Deregistration Delay:**
- Time to complete in-flight requests before terminating instance
- Default: 300 seconds (1-3600 seconds)
- Prevents abrupt connection termination

---

### ⚡ AWS Lambda - Serverless Compute

**What:** Run code without managing servers (truly serverless!)

**How it works:**
1. Upload your code (function)
2. Configure trigger (API call, S3 upload, schedule, etc.)
3. Lambda runs your code when triggered
4. You pay only for compute time used

**Key Characteristics:**
- **Languages:** Python, Node.js, Java, C#, Go, Ruby, Custom runtime
- **Execution time:** Max 15 minutes per invocation
- **Memory:** 128 MB - 10 GB (CPU scales with memory)
- **Pricing:** Pay per request + compute time (GB-seconds)
- **Free tier:** 1M requests + 400,000 GB-seconds per month

**Common Triggers:**
- API Gateway (REST API)
- S3 events (file upload)
- DynamoDB Streams (data changes)
- CloudWatch Events (scheduled)
- SNS/SQS messages
- ALB (Application Load Balancer)

**Use Cases:**
- **Data processing:** Process S3 uploads (resize images, extract metadata)
- **Real-time file processing:** Thumbnail generation, video transcoding
- **APIs:** Serverless REST APIs (Lambda + API Gateway)
- **Automation:** Scheduled tasks (backups, reports)
- **Stream processing:** Process Kinesis/DynamoDB streams
- **Chatbots:** Process messages

**Lambda Limits (Important for Exam):**

| Resource | Limit |
|----------|-------|
| Execution time | 15 minutes max |
| Memory | 128 MB - 10 GB |
| Deployment package | 50 MB (zipped), 250 MB (unzipped) |
| /tmp storage | 512 MB - 10 GB |
| Concurrent executions | 1000 (default, can increase) |
| Environment variables | 4 KB total |

**Lambda + ALB:**
- ALB can invoke Lambda functions
- Lambda function must return HTTP response
- **Use case:** Serverless web applications
- **Benefit:** No EC2 instances to manage

**Lambda + API Gateway:**
- Most common pattern for REST APIs
- API Gateway handles HTTP requests
- Lambda processes requests
- **Benefit:** Fully serverless API

**Exam Scenarios:**

> "Process images uploaded to S3 (resize, thumbnail)"
> **Answer:** S3 trigger → Lambda function

> "Run code for max 10 minutes to process data"
> **Answer:** Lambda (within 15-min limit)

> "Run code for 30 minutes to process large dataset"
> **Answer:** NOT Lambda (exceeds 15-min limit) → Use EC2, ECS, or Batch

> "Need serverless REST API"
> **Answer:** API Gateway + Lambda

---

### 🐳 Container Services (High Level)

#### ECS (Elastic Container Service)

**What:** AWS's container orchestration service (like Kubernetes, but AWS-native)

**Key Points:**
- Run Docker containers on AWS
- **Launch Types:**
  1. **EC2 Launch Type:** You manage EC2 instances (more control)
  2. **Fargate Launch Type:** AWS manages infrastructure (serverless)
- Integrates with ALB/NLB
- IAM roles for tasks

**When to use:**
- Containerized applications
- Microservices
- Want AWS-native solution (not Kubernetes)

---

#### EKS (Elastic Kubernetes Service)

**What:** Managed Kubernetes service

**Key Points:**
- Run Kubernetes on AWS
- AWS manages control plane
- You manage worker nodes (or use Fargate)
- More complex than ECS
- Industry-standard (portable across clouds)

**When to use:**
- Already using Kubernetes
- Need Kubernetes-specific features
- Want multi-cloud portability

**ECS vs EKS:**
- **ECS:** Simpler, AWS-native, easier to learn
- **EKS:** More powerful, industry-standard, complex

**Exam Tip:** If question doesn't specify Kubernetes → ECS. If mentions Kubernetes → EKS.

---

### 📦 AWS Batch (Basics)

**What:** Run batch computing jobs (hundreds/thousands of jobs)

**Key Points:**
- Automatically provisions optimal compute resources
- Schedules jobs
- Manages job queues
- Uses EC2 or Spot Instances
- **No time limit** (unlike Lambda's 15 minutes)

**Use Cases:**
- Large-scale data processing
- Video rendering
- Scientific simulations
- Financial risk modeling

**Batch vs Lambda:**
- **Lambda:** Short tasks (<15 min), event-driven, serverless
- **Batch:** Long-running tasks (hours/days), batch processing, managed EC2

**Exam Scenario:**
> "Process millions of images, each takes 30 minutes"
> **Answer:** AWS Batch (Lambda limited to 15 min)

---

## 🎓 Section 2 Summary - Key Takeaways

✅ **EC2 Instance Types:**
- Remember: FIGHT DR MC PX
- T = Burstable, M = General, C = Compute, R = Memory, I = Storage

✅ **Purchasing Options:**
- On-Demand: No commitment, most expensive
- Reserved: 1-3 year commitment, 40-60% savings
- Spot: 50-90% savings, can be interrupted
- Savings Plans: Flexible, commit to $/hour

✅ **Placement Groups:**
- Cluster: Low latency (same AZ)
- Spread: High availability (max 7/AZ)
- Partition: Large distributed systems

✅ **Auto Scaling:**
- Target tracking for maintaining metrics
- Scheduled for predictable patterns
- Integrates with ELB for high availability

✅ **Load Balancers:**
- ALB: Layer 7, HTTP/HTTPS, path routing, microservices
- NLB: Layer 4, TCP/UDP, extreme performance, static IP
- CLB: Legacy, avoid in exam

✅ **Lambda:**
- Serverless, 15-min max execution
- Pay per request + compute time
- Common triggers: API Gateway, S3, CloudWatch Events

✅ **Containers:**
- ECS: AWS-native, simpler
- EKS: Kubernetes, more powerful

✅ **Batch:**
- Long-running batch jobs
- No time limit (vs Lambda 15 min)

---

*[Continue to Section 3: Storage Services →]*

---

## 3️⃣ Storage Services

### 📦 Amazon S3 (Simple Storage Service) - Object Storage 🔥

**What:** Store and retrieve any amount of data (files/objects) from anywhere

**Think of it as:** Unlimited cloud storage for files (not a file system!)

---

#### S3 Basics

**Key Concepts:**
- **Bucket:** Container for objects (like a folder, but top-level)
  - Globally unique name (across all AWS accounts!)
  - Defined at region level
  - Naming: lowercase, no underscores, 3-63 characters
- **Object:** File stored in bucket
  - Key = full path (e.g., `my-folder/my-file.txt`)
  - Value = content (max 5 TB per object)
  - Metadata = key-value pairs
  - Version ID (if versioning enabled)

**S3 is NOT a file system:**
- No directories (just key prefixes that look like folders)
- Can't mount like EFS
- Access via API, CLI, or Console

---

#### S3 Storage Classes 🔥

**Critical for Exam:** Choose the right storage class based on access patterns and cost

| Storage Class | Description | Availability | Use Case | Retrieval Time |
|---------------|-------------|--------------|----------|----------------|
| **S3 Standard** | General purpose | 99.99% | Frequently accessed | Instant |
| **S3 Intelligent-Tiering** | Auto-moves between tiers | 99.9% | Unknown/changing access | Instant |
| **S3 Standard-IA** | Infrequent access | 99.9% | Backups, disaster recovery | Instant |
| **S3 One Zone-IA** | Infrequent, single AZ | 99.5% | Secondary backups | Instant |
| **S3 Glacier Instant** | Archive, instant retrieval | 99.9% | Medical images, news | Instant |
| **S3 Glacier Flexible** | Archive | 99.99% | Long-term backups | Minutes to hours |
| **S3 Glacier Deep Archive** | Lowest cost archive | 99.99% | Compliance, 7-10 year retention | 12-48 hours |

**Detailed Breakdown:**

**1. S3 Standard:**
- **Cost:** Highest storage cost
- **Durability:** 99.999999999% (11 nines) - virtually never lose data
- **Availability:** 99.99% - highly available
- **Use case:** Frequently accessed data, content distribution, big data analytics

**2. S3 Intelligent-Tiering:**
- **Automatic:** Moves objects between access tiers based on usage
- **No retrieval fees**
- **Tiers:**
  - Frequent Access (automatic)
  - Infrequent Access (30 days no access)
  - Archive Instant Access (90 days)
  - Archive Access (90-730 days, optional)
  - Deep Archive (180-730 days, optional)
- **Use case:** Unpredictable access patterns, set-it-and-forget-it

**3. S3 Standard-IA (Infrequent Access):**
- **Lower storage cost** than Standard
- **Retrieval fee** per GB
- **Minimum:** 30 days storage, 128 KB object size
- **Use case:** Disaster recovery, backups accessed monthly

**4. S3 One Zone-IA:**
- **Cheapest IA option**
- **Single AZ** (vs 3+ AZs for others)
- **Risk:** Data lost if AZ destroyed
- **Use case:** Secondary backups, recreatable data

**5. S3 Glacier Instant Retrieval:**
- **Archive storage** with instant access
- **Minimum:** 90 days storage
- **Use case:** Medical images, news media archives (rarely accessed but need instant retrieval)

**6. S3 Glacier Flexible Retrieval (formerly Glacier):**
- **Retrieval options:**
  - Expedited: 1-5 minutes (most expensive)
  - Standard: 3-5 hours
  - Bulk: 5-12 hours (cheapest)
- **Minimum:** 90 days storage
- **Use case:** Long-term backups, compliance archives

**7. S3 Glacier Deep Archive:**
- **Lowest cost** storage in AWS
- **Retrieval time:** 12-48 hours
- **Minimum:** 180 days storage
- **Use case:** Compliance data kept for 7-10 years, rarely/never accessed

**Exam Decision Tree:**
```
Need instant access?
├─ Yes → Frequently accessed?
│  ├─ Yes → S3 Standard
│  ├─ No → Infrequent access?
│  │  ├─ Yes, critical → S3 Standard-IA
│  │  ├─ Yes, not critical → S3 One Zone-IA
│  │  └─ Archive → S3 Glacier Instant
│  └─ Unknown pattern → S3 Intelligent-Tiering
└─ No → How long can you wait?
   ├─ Minutes-hours → S3 Glacier Flexible
   └─ 12-48 hours → S3 Glacier Deep Archive
```

---

#### S3 Lifecycle Rules 🔥

**What:** Automatically transition or delete objects based on rules

**Actions:**
1. **Transition actions:** Move objects to different storage class
2. **Expiration actions:** Delete objects after specified time

**Example Rules:**

```
Rule 1: Transition to IA
- After 30 days → Move to Standard-IA
- After 90 days → Move to Glacier Flexible
- After 365 days → Move to Glacier Deep Archive

Rule 2: Delete old versions
- After 90 days → Delete non-current versions

Rule 3: Clean up incomplete uploads
- After 7 days → Delete incomplete multipart uploads
```

**Use Cases:**
- **Log files:** Standard → IA (30 days) → Glacier (90 days) → Delete (365 days)
- **Backups:** Standard → IA (30 days) → Glacier Deep Archive (90 days)
- **Thumbnails:** Standard → Delete (60 days) if not accessed

**Exam Tip:**
> "Automatically move infrequently accessed data to cheaper storage"
> **Answer:** S3 Lifecycle policies

---

#### S3 Versioning

**What:** Keep multiple versions of an object in the same bucket

**How it works:**
- Enabled at bucket level
- Each modification creates new version
- Version ID assigned to each version
- Protects against accidental deletes (delete marker added, not actually deleted)

**Benefits:**
- **Protect against unintended deletes**
- **Rollback to previous versions**
- **Compliance requirements**

**Considerations:**
- Increases storage costs (storing multiple versions)
- Deleting a versioned object adds delete marker (not permanent)
- To permanently delete, specify version ID

**Exam Scenario:**
> "Protect S3 objects from accidental deletion"
> **Answer:** Enable versioning

---

#### S3 Replication 🔥

**Two Types:**

**1. Cross-Region Replication (CRR):**
- Replicate objects to bucket in different region
- **Use cases:**
  - Compliance (data must be in specific regions)
  - Lower latency access for global users
  - Disaster recovery

**2. Same-Region Replication (SRR):**
- Replicate objects to bucket in same region
- **Use cases:**
  - Log aggregation (multiple buckets → one)
  - Live replication between prod and test
  - Compliance (separate accounts)

**Requirements:**
- Versioning must be enabled (source and destination)
- Buckets can be in different accounts
- IAM permissions for S3 to replicate
- Only new objects replicated (not existing, unless using Batch Replication)

**What's Replicated:**
- New objects (after replication enabled)
- Metadata
- Object ACLs
- Object tags

**What's NOT Replicated:**
- Existing objects (before replication enabled)
- Objects encrypted with SSE-C
- Delete markers (optional)
- Deletions with version ID

**Exam Tip:**
> "Replicate S3 data to another region for disaster recovery"
> **Answer:** Cross-Region Replication (CRR)

---

#### S3 Encryption 🔥

**Four Methods:**

**1. SSE-S3 (Server-Side Encryption with S3-Managed Keys):**
- AWS manages keys
- AES-256 encryption
- **Header:** `x-amz-server-side-encryption: AES256`
- **Default** for new buckets
- **Use case:** Simple encryption, no key management needed

**2. SSE-KMS (Server-Side Encryption with KMS):**
- AWS KMS manages keys
- You control key policies
- Audit key usage in CloudTrail
- **Header:** `x-amz-server-side-encryption: aws:kms`
- **Use case:** Need audit trail, control over keys

**3. SSE-C (Server-Side Encryption with Customer-Provided Keys):**
- You manage keys outside AWS
- AWS encrypts/decrypts using your key
- **HTTPS required**
- Key must be provided with every request
- **Use case:** Full control over keys, keys stored outside AWS

**4. Client-Side Encryption:**
- You encrypt before uploading
- You decrypt after downloading
- AWS never sees unencrypted data
- **Use case:** Maximum security, don't trust AWS with unencrypted data

**Encryption in Transit:**
- HTTPS (SSL/TLS)
- Recommended for all S3 operations
- Required for SSE-C

**Exam Comparison:**

| Method | Key Management | Audit Trail | Complexity | Use Case |
|--------|----------------|-------------|------------|----------|
| SSE-S3 | AWS | ❌ | Easiest | Default choice |
| SSE-KMS | AWS KMS | ✅ | Medium | Need audit, control |
| SSE-C | You (outside AWS) | ❌ | Hard | Full key control |
| Client-Side | You | ❌ | Hardest | Maximum security |

**Exam Tip:**
> "Need to audit who accessed encryption keys"
> **Answer:** SSE-KMS (CloudTrail logs KMS key usage)

---

#### S3 Security & Access Control

**Bucket Policies:**
- JSON-based policies attached to buckets
- Grant cross-account access
- Make bucket public

**Example - Public Read Access:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

**IAM Policies:**
- Attached to users, groups, roles
- Control what IAM entities can do with S3

**Access Control Lists (ACLs):**
- Legacy (use bucket policies instead)
- Object-level or bucket-level
- Less flexible than policies

**Block Public Access:**
- Safety feature to prevent accidental public access
- Four settings (block all public access recommended)
- Overrides bucket policies and ACLs

**S3 Access Points:**
- Simplify managing access to shared datasets
- Each access point has its own policy
- **Use case:** Different teams need different access to same bucket

**Exam Tip:**
> "Grant cross-account access to S3 bucket"
> **Answer:** Bucket policy with principal from other account

---

#### S3 Performance Optimization

**Multipart Upload:**
- Split large files into parts
- Upload parts in parallel
- **Recommended:** Files > 100 MB
- **Required:** Files > 5 GB
- Faster uploads, can resume failed uploads

**Transfer Acceleration:**
- Use CloudFront edge locations for faster uploads
- Data routed through AWS backbone network
- **Use case:** Upload from far away regions, need faster uploads

**S3 Select:**
- Retrieve subset of data using SQL
- Filter data server-side (don't download entire object)
- **Use case:** Query CSV/JSON files, save bandwidth

**Byte-Range Fetches:**
- Request specific byte range of object
- Parallelize downloads
- Recover from partial failures

---

### 💾 EBS (Elastic Block Store) - Block Storage for EC2 🔥

**What:** Virtual hard drives for EC2 instances

**Think of it as:** A USB drive you attach to your EC2 instance

---

#### EBS Volume Types 🔥

**Two Categories:**
1. **SSD-backed:** For transactional workloads (IOPS-intensive)
2. **HDD-backed:** For throughput workloads (MB/s-intensive)

| Type | Name | IOPS | Throughput | Size | Use Case |
|------|------|------|------------|------|----------|
| **gp3** | General Purpose SSD | 3,000-16,000 | 125-1,000 MB/s | 1 GB - 16 TB | Most workloads |
| **gp2** | General Purpose SSD | 3-16,000 (bursts) | 128-250 MB/s | 1 GB - 16 TB | Legacy |
| **io2 Block Express** | Provisioned IOPS SSD | 256,000 | 4,000 MB/s | 4 GB - 64 TB | Highest performance |
| **io2** | Provisioned IOPS SSD | 64,000 | 1,000 MB/s | 4 GB - 16 TB | Mission-critical |
| **io1** | Provisioned IOPS SSD | 64,000 | 1,000 MB/s | 4 GB - 16 TB | Legacy |
| **st1** | Throughput Optimized HDD | 500 | 500 MB/s | 125 GB - 16 TB | Big data, logs |
| **sc1** | Cold HDD | 250 | 250 MB/s | 125 GB - 16 TB | Infrequent access |

**Detailed Breakdown:**

**1. gp3 (General Purpose SSD) - Most Common:**
- **Default choice** for most workloads
- **Baseline:** 3,000 IOPS, 125 MB/s (free)
- **Scalable:** Up to 16,000 IOPS, 1,000 MB/s (pay extra)
- **Cost:** Lower than gp2
- **Use case:** Virtual desktops, dev/test, low-latency apps

**2. gp2 (General Purpose SSD) - Legacy:**
- IOPS scales with size (3 IOPS per GB)
- Burst up to 3,000 IOPS (for volumes < 1 TB)
- **Being replaced by gp3**

**3. io2 Block Express (Provisioned IOPS SSD):**
- **Highest performance** EBS volume
- Sub-millisecond latency
- **Use case:** Largest databases (SAP HANA, Oracle)

**4. io2/io1 (Provisioned IOPS SSD):**
- **Guaranteed IOPS** (you provision specific IOPS)
- **Use case:** 
  - Databases (need consistent IOPS)
  - Mission-critical applications
  - Applications requiring > 16,000 IOPS
- **Multi-Attach:** Can attach to multiple EC2 instances (io2 only)

**5. st1 (Throughput Optimized HDD):**
- **Cannot be boot volume**
- Optimized for sequential reads/writes
- **Use case:** Big data, data warehouses, log processing

**6. sc1 (Cold HDD):**
- **Lowest cost** EBS volume
- **Cannot be boot volume**
- **Use case:** Infrequently accessed data, lowest storage cost

**Exam Decision Tree:**
```
Need boot volume?
├─ Yes → gp3 or io2 (only SSD can be boot)
└─ No → What's your workload?
   ├─ Random I/O (IOPS) → SSD
   │  ├─ General use → gp3
   │  └─ High performance DB → io2
   └─ Sequential I/O (throughput) → HDD
      ├─ Frequently accessed → st1
      └─ Infrequently accessed → sc1
```

**Exam Tips:**
> "Need cost-effective storage for frequently accessed data"
> **Answer:** gp3

> "Database requires 50,000 IOPS"
> **Answer:** io2 (gp3 max is 16,000)

> "Big data workload, sequential reads, throughput-intensive"
> **Answer:** st1

---

#### EBS Snapshots 🔥

**What:** Backup of EBS volume at a point in time

**Key Points:**
- Stored in S3 (managed by AWS, you don't see the bucket)
- Incremental (only changed blocks backed up)
- First snapshot = full copy, subsequent = only changes
- Can copy snapshots across regions
- Can create volumes from snapshots (even in different AZ)

**EBS Snapshot Archive:**
- Move snapshots to archive tier (75% cheaper)
- Takes 24-72 hours to restore
- **Use case:** Long-term backups, rarely needed

**Recycle Bin:**
- Protect against accidental deletion
- Retain deleted snapshots for 1 day to 1 year
- Recover deleted snapshots during retention period

**Fast Snapshot Restore (FSR):**
- Force full initialization of snapshot
- No latency on first use
- Expensive
- **Use case:** Need instant performance from snapshot

**Exam Scenarios:**
> "Backup EBS volume for disaster recovery"
> **Answer:** Create EBS snapshot

> "Move EBS volume to another AZ"
> **Answer:** Create snapshot → Create volume from snapshot in new AZ

> "Reduce snapshot storage costs for old backups"
> **Answer:** EBS Snapshot Archive

---

#### EBS Characteristics

**Availability:**
- Locked to single AZ
- To move: Snapshot → Create volume in new AZ

**Attachment:**
- One volume → One instance (except io2 Multi-Attach)
- One instance → Multiple volumes (✅)

**Persistence:**
- **Root volume:** Deleted by default when instance terminates (can change)
- **Additional volumes:** Not deleted by default

**Encryption:**
- Encrypted volumes → Encrypted snapshots → Encrypted volumes
- Encrypt unencrypted volume: Snapshot → Copy snapshot (encrypt) → Create volume

---

### 📁 EFS (Elastic File System) - Network File System

**What:** Managed NFS (Network File System) that can be mounted on multiple EC2 instances

**Think of it as:** Shared drive accessible by multiple computers

**Key Differences from EBS:**

| Feature | EBS | EFS |
|---------|-----|-----|
| **Attachment** | One instance (except io2 Multi-Attach) | Multiple instances |
| **Scope** | Single AZ | Multi-AZ (regional) |
| **Type** | Block storage | File storage |
| **Protocol** | Block device | NFS |
| **Use case** | Instance storage | Shared file storage |

**EFS Characteristics:**
- **Multi-AZ:** Data replicated across multiple AZs
- **Highly available and scalable**
- **Pay per use:** No capacity planning (grows/shrinks automatically)
- **Expensive:** 3x cost of gp2 EBS
- **POSIX-compliant:** Works with Linux (not Windows)

**Performance Modes:**

**1. General Purpose (default):**
- Latency-sensitive workloads
- Web serving, content management

**2. Max I/O:**
- Higher latency, higher throughput
- Big data, media processing

**Throughput Modes:**

**1. Bursting:**
- Throughput scales with file system size
- 50 MB/s per TB

**2. Provisioned:**
- Set throughput regardless of size
- **Use case:** Small file system, high throughput needs

**3. Elastic:**
- Automatically scales throughput
- Pay for what you use

**Storage Classes:**
- **Standard:** Frequently accessed
- **Infrequent Access (IA):** Lower cost, retrieval fee
- **Lifecycle policy:** Auto-move files to IA after X days

**Exam Scenarios:**
> "Multiple EC2 instances need to share files"
> **Answer:** EFS

> "Need shared storage across multiple AZs"
> **Answer:** EFS (EBS is single AZ)

> "Linux instances need shared file system"
> **Answer:** EFS

---

### 🗄️ FSx - Managed File Systems

**What:** Fully managed third-party file systems

**Two Main Types:**

---

#### FSx for Windows File Server

**What:** Managed Windows file system

**Features:**
- **SMB protocol** (Windows native)
- **Active Directory integration**
- **Windows-native features:** DFS, ACLs, user quotas
- **Multi-AZ** for high availability

**Use Cases:**
- Windows applications
- Need Active Directory integration
- Lift-and-shift Windows workloads

**Exam Tip:**
> "Windows instances need shared file storage"
> **Answer:** FSx for Windows

---

#### FSx for Lustre

**What:** High-performance file system for compute-intensive workloads

**Features:**
- **Extreme performance:** 100s GB/s, millions of IOPS
- **Sub-millisecond latencies**
- **S3 integration:** Can read/write directly to S3
- **Scratch or Persistent** storage

**Use Cases:**
- Machine learning
- High Performance Computing (HPC)
- Video processing
- Financial modeling

**Scratch vs Persistent:**
- **Scratch:** Temporary, no replication, highest performance, lowest cost
- **Persistent:** Long-term, replicated, high availability

**Exam Tip:**
> "HPC workload needs high-performance file system"
> **Answer:** FSx for Lustre

---

### 💾 AWS Backup

**What:** Centralized backup service across AWS services

**Supported Services:**
- EC2, EBS
- S3
- RDS, Aurora, DynamoDB
- EFS, FSx
- Storage Gateway

**Features:**
- **Backup Plans:** Define schedule, retention, lifecycle
- **Backup Vault:** Store and organize backups
- **Cross-region backup**
- **Cross-account backup**
- **Point-in-time recovery**

**Use Cases:**
- Centralized backup management
- Compliance requirements
- Disaster recovery

**Exam Tip:**
> "Centrally manage backups across multiple AWS services"
> **Answer:** AWS Backup

---

## 🎓 Section 3 Summary - Key Takeaways

✅ **S3:**
- Object storage, not file system
- Storage classes: Standard, IA, Glacier (choose based on access frequency)
- Lifecycle rules for automatic transitions
- Versioning protects against deletes
- Replication: CRR (cross-region), SRR (same-region)
- Encryption: SSE-S3 (default), SSE-KMS (audit), SSE-C (your keys)

✅ **EBS:**
- Block storage for EC2 (virtual hard drives)
- gp3: General purpose (most common)
- io2: High performance databases
- st1: Throughput-optimized (big data)
- sc1: Cold storage (lowest cost)
- Snapshots for backups (incremental, stored in S3)

✅ **EFS:**
- Network file system (NFS)
- Multiple EC2 instances can mount
- Multi-AZ, highly available
- Linux only (POSIX-compliant)

✅ **FSx:**
- FSx for Windows: Windows file server, Active Directory
- FSx for Lustre: HPC, extreme performance

✅ **AWS Backup:**
- Centralized backup across AWS services
- Backup plans, vaults, cross-region/account

---

*[Continue to Section 4: Databases →]*

---

## 4️⃣ Databases (HIGH WEIGHT) 🔥🔥🔥

This section is CRITICAL for the exam. Databases appear in many questions!

---

### 🗄️ RDS (Relational Database Service) 🔥

**What:** Managed relational database service (AWS manages the database for you)

**Think of it as:** Rent a database without managing servers, backups, patches

---

#### Supported Database Engines

1. **MySQL**
2. **PostgreSQL**
3. **MariaDB**
4. **Oracle**
5. **Microsoft SQL Server**
6. **Amazon Aurora** (AWS's own, covered separately)

**What AWS Manages:**
- OS patching
- Database software patching
- Automated backups
- Monitoring
- Hardware provisioning
- Setup and configuration

**What You Manage:**
- Database schema
- Query optimization
- User management
- Application optimization

---

#### RDS Multi-AZ vs Read Replicas 🔥🔥🔥

**CRITICAL FOR EXAM - MUST UNDERSTAND THE DIFFERENCE!**

---

**Multi-AZ (High Availability):**

**Purpose:** Disaster recovery, high availability

**How it works:**
- **Synchronous replication** to standby instance in different AZ
- Standby instance is NOT accessible (can't read from it)
- Automatic failover if primary fails
- Same endpoint (DNS name doesn't change)
- Failover time: ~60-120 seconds

**Use Cases:**
- Production databases
- Need high availability
- Minimize downtime during maintenance

**Key Points:**
- **NOT for scaling reads** (standby not accessible)
- **For disaster recovery only**
- Automatic backups from standby (no performance impact on primary)
- Zero data loss (synchronous replication)

---

**Read Replicas (Scalability):**

**Purpose:** Scale read performance, offload read traffic

**How it works:**
- **Asynchronous replication** from primary
- Up to 15 read replicas (Aurora), 5 (other engines)
- Each replica has its own endpoint
- Can be in same AZ, cross-AZ, or cross-region
- Can be promoted to standalone database

**Use Cases:**
- Read-heavy workloads (reporting, analytics)
- Offload read traffic from primary
- Disaster recovery (promote replica to primary)

**Key Points:**
- **For scaling reads** (not high availability)
- Application must update connection string to use replicas
- **Eventual consistency** (slight replication lag)
- Can have read replicas of read replicas (but more lag)

---

**Comparison Table:**

| Feature | Multi-AZ | Read Replicas |
|---------|----------|---------------|
| **Purpose** | High availability, disaster recovery | Read scalability |
| **Replication** | Synchronous | Asynchronous |
| **Standby accessible?** | ❌ No | ✅ Yes |
| **Failover** | Automatic | Manual (promote) |
| **Endpoint** | Same endpoint | Different endpoints |
| **Data loss** | Zero | Possible (replication lag) |
| **Number** | 1 standby | Up to 15 (Aurora) |
| **Use case** | Production HA | Read-heavy workloads |
| **Cost** | 2x (primary + standby) | Pay per replica |

---

**Exam Scenarios:**

> "Application needs high availability for database"
> **Answer:** RDS Multi-AZ

> "Read-heavy application, primary database overloaded"
> **Answer:** RDS Read Replicas

> "Need to run analytics queries without impacting production database"
> **Answer:** RDS Read Replica (run queries on replica)

> "Database must have zero downtime during maintenance"
> **Answer:** RDS Multi-AZ (failover to standby during maintenance)

> "Need to scale read capacity for global users"
> **Answer:** RDS Read Replicas in multiple regions

---

#### RDS Backups

**Automated Backups:**
- Daily full backup (during backup window)
- Transaction logs backed up every 5 minutes
- Retention: 1-35 days (default 7 days)
- Point-in-time recovery (restore to any second within retention)
- Stored in S3
- **Free storage** equal to database size

**Manual Snapshots:**
- User-initiated backups
- Retention: Forever (until you delete)
- Good for: Before major changes, long-term archival

**Restoring:**
- Creates NEW database instance
- Cannot restore over existing database
- Can restore to specific point in time

---

#### RDS Storage Auto Scaling

**What:** Automatically increase storage when running out of space

**How it works:**
- Set maximum storage threshold
- RDS automatically scales when:
  - Free space < 10% of allocated storage
  - Low storage lasts > 5 minutes
  - 6 hours passed since last scaling

**Use case:** Unpredictable workloads, avoid manual scaling

---

#### RDS Security

**Encryption at Rest:**
- KMS encryption
- Must be enabled at creation time
- If primary encrypted → replicas encrypted
- To encrypt unencrypted DB: Snapshot → Copy snapshot (encrypt) → Restore

**Encryption in Transit:**
- SSL/TLS connections
- Download SSL certificate from AWS
- Enforce SSL in database settings

**Network Security:**
- Deploy in private subnets
- Security groups control access
- Never publicly accessible (best practice)

**IAM Authentication:**
- Use IAM roles instead of passwords
- Auth token valid for 15 minutes
- Benefits: Centralized access management, no password management

---

### 🚀 Amazon Aurora 🔥🔥

**What:** AWS's proprietary database engine (MySQL and PostgreSQL compatible)

**Why Aurora:** Better performance, availability, and scalability than standard RDS

---

#### Aurora Key Features

**Performance:**
- **5x faster than MySQL** on RDS
- **3x faster than PostgreSQL** on RDS
- Achieved through optimized storage and caching

**Storage:**
- Automatic storage scaling (10 GB → 128 TB)
- 6 copies of data across 3 AZs
- Self-healing (corrupt data automatically repaired)
- Only pay for used storage

**High Availability:**
- **15 read replicas** (vs 5 for RDS)
- Replication < 10ms lag
- Automatic failover < 30 seconds
- Master + up to 15 read replicas

**Endpoints:**
- **Writer Endpoint:** Points to master (read/write)
- **Reader Endpoint:** Load balances across read replicas
- **Custom Endpoints:** Define subset of instances

---

#### Aurora Serverless

**What:** On-demand, auto-scaling Aurora

**How it works:**
- No need to choose instance size
- Automatically starts, stops, scales based on usage
- Pay per second for resources used

**Use Cases:**
- Infrequent, intermittent, or unpredictable workloads
- Development/test databases
- New applications with unknown demand

**Exam Tip:**
> "Database with unpredictable traffic, want to minimize costs"
> **Answer:** Aurora Serverless

---

#### Aurora Global Database

**What:** Single Aurora database spanning multiple regions

**Features:**
- 1 primary region (read/write)
- Up to 5 secondary regions (read-only)
- < 1 second replication lag
- Disaster recovery: Promote secondary to primary < 1 minute

**Use Cases:**
- Global applications
- Disaster recovery across regions
- Low-latency global reads

---

#### Aurora Multi-Master

**What:** Multiple instances can perform writes

**Features:**
- Every instance can read and write
- Immediate failover (no downtime)
- Higher write availability

**Use case:** Applications requiring continuous write availability

---

#### RDS vs Aurora

| Feature | RDS | Aurora |
|---------|-----|--------|
| **Performance** | Standard | 5x MySQL, 3x PostgreSQL |
| **Storage** | Manual scaling | Auto-scaling (10GB-128TB) |
| **Replicas** | 5 | 15 |
| **Failover** | 60-120 seconds | < 30 seconds |
| **Cost** | Lower | 20% more expensive |
| **Availability** | 99.95% (Multi-AZ) | 99.99% |
| **Use case** | Standard workloads | High-performance, critical apps |

**Exam Tip:**
> "Need highest performance relational database"
> **Answer:** Aurora

> "MySQL database, need cost-effective solution"
> **Answer:** RDS MySQL

---

### 📊 DynamoDB - NoSQL Database 🔥🔥

**What:** Fully managed NoSQL database (key-value and document)

**Think of it as:** Super-fast, scalable database for simple data structures

---

#### DynamoDB Basics

**Key Concepts:**
- **Table:** Collection of items
- **Item:** Row (like a row in SQL)
- **Attribute:** Column (like a column in SQL)
- **Primary Key:** Uniquely identifies each item

**Primary Key Types:**

**1. Partition Key (Simple Primary Key):**
- Single attribute
- Must be unique
- DynamoDB uses hash function to determine partition

**Example:** User table with `user_id` as partition key

**2. Partition Key + Sort Key (Composite Primary Key):**
- Two attributes
- Partition key groups items
- Sort key orders items within partition
- Combination must be unique

**Example:** Orders table
- Partition key: `customer_id`
- Sort key: `order_date`
- Same customer can have multiple orders (different dates)

---

#### Partition Key vs Sort Key 🔥

**CRITICAL FOR EXAM!**

**Partition Key:**
- **Purpose:** Determine which partition stores the item
- **Uniqueness:** Must be unique (if no sort key)
- **Query:** Can only query by exact match
- **Example:** `user_id`, `product_id`, `customer_id`

**Sort Key:**
- **Purpose:** Order items within a partition
- **Uniqueness:** Unique within partition
- **Query:** Can use operators (>, <, between, begins_with)
- **Example:** `timestamp`, `order_date`, `version_number`

**Why Use Sort Key:**
- Store multiple items with same partition key
- Enable range queries
- Maintain chronological order

**Example Scenario:**

**Without Sort Key:**
```
Table: Users
Partition Key: user_id
- user_id: 123 → User data
- user_id: 456 → User data
```

**With Sort Key:**
```
Table: UserOrders
Partition Key: user_id
Sort Key: order_date
- user_id: 123, order_date: 2024-01-01 → Order 1
- user_id: 123, order_date: 2024-01-15 → Order 2
- user_id: 123, order_date: 2024-02-01 → Order 3
```

**Query Examples:**
- Get all orders for user 123: Query by partition key
- Get orders for user 123 in January: Query by partition key + sort key range

---

#### DynamoDB Capacity Modes

**1. Provisioned Capacity:**
- Specify read/write capacity units (RCU/WCU)
- **RCU:** 1 strongly consistent read/sec for item up to 4 KB
- **WCU:** 1 write/sec for item up to 1 KB
- Auto-scaling available
- Cheaper if predictable traffic

**2. On-Demand:**
- Pay per request
- No capacity planning
- Automatically scales
- More expensive per request
- Good for unpredictable traffic

**Exam Tip:**
> "Unpredictable traffic patterns"
> **Answer:** DynamoDB On-Demand

> "Steady, predictable traffic"
> **Answer:** DynamoDB Provisioned with Auto Scaling

---

#### DynamoDB Global Tables 🔥

**What:** Multi-region, multi-active database

**Features:**
- **Multi-region replication**
- **Multi-active:** Read/write in any region
- **Low latency:** Local reads/writes
- **Disaster recovery:** Automatic failover
- **Replication lag:** Typically < 1 second

**Requirements:**
- DynamoDB Streams must be enabled
- Tables must have same name in all regions

**Use Cases:**
- Global applications
- Low-latency access worldwide
- Disaster recovery

**Exam Scenario:**
> "Application with users worldwide, need low-latency database access"
> **Answer:** DynamoDB Global Tables

---

#### DynamoDB Accelerator (DAX) 🔥

**What:** In-memory cache for DynamoDB

**Features:**
- **Microsecond latency** (vs milliseconds for DynamoDB)
- Fully managed
- Compatible with DynamoDB API (no code changes)
- 10x performance improvement

**How it works:**
- Cache sits in front of DynamoDB
- Read requests → Check DAX → If miss, query DynamoDB
- Write-through cache (writes go to DynamoDB and DAX)

**Use Cases:**
- Read-heavy workloads
- Need microsecond response times
- Reduce DynamoDB costs (fewer reads)

**DAX vs ElastiCache:**
- **DAX:** DynamoDB-specific, no code changes, object cache
- **ElastiCache:** General-purpose, requires code changes, can cache query results

**Exam Tip:**
> "Improve DynamoDB read performance with minimal code changes"
> **Answer:** DAX

---

#### DynamoDB Streams

**What:** Capture changes to DynamoDB table (inserts, updates, deletes)

**Features:**
- Ordered stream of changes
- Retention: 24 hours
- Can trigger Lambda functions
- Enable cross-region replication

**Use Cases:**
- Real-time analytics
- Trigger workflows (Lambda)
- Replicate data to other services
- Audit trail

---

#### DynamoDB TTL (Time To Live)

**What:** Automatically delete items after expiration time

**Features:**
- No extra cost
- Specify TTL attribute (Unix timestamp)
- Items deleted within 48 hours of expiration
- Doesn't consume WCUs

**Use case:** Session data, temporary data, logs

---

### 🗄️ ElastiCache - In-Memory Cache 🔥

**What:** Managed in-memory cache (Redis or Memcached)

**Purpose:** Reduce database load, improve application performance

---

#### Redis vs Memcached 🔥

**CRITICAL FOR EXAM!**

| Feature | Redis | Memcached |
|---------|-------|-----------|
| **Data Types** | Strings, lists, sets, sorted sets, hashes | St
