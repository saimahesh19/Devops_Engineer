# AWS Solutions Architect Associate (SAA) - Complete Study Notes

---

## Table of Contents

1. [Identity & Security](#1-identity--security)
2. [Compute Services](#2-compute-services)
3. [Storage Services](#3-storage-services)
4. [Databases](#4-databases)
5. [Networking](#5-networking)
6. [Application Integration](#6-application-integration)
7. [High Availability & Disaster Recovery](#7-high-availability--disaster-recovery)

---

## 1️⃣ Identity & Security (VERY IMPORTANT)

### Topics Covered
- IAM users, groups, roles
- IAM policies (inline vs managed)
- Trust policies & role assumption
- MFA, password policies
- AWS Organizations
- SCPs (Service Control Policies)
- KMS (encryption at rest)
- AWS Shield, WAF
- Secrets Manager vs Parameter Store
- CloudTrail (who did what)
- Shared Responsibility Model

---

### IAM: Identity and Access Management

**What is IAM?**
- Tells who can access what
- Defines who you are (user/group)
- What you can do (role)
- On which resource

#### IAM User
**What:** Individual people or applications that need AWS access

**Example:** John (developer), Sarah (admin), or a monitoring application

**Best Practice:** Each person gets their own user (never share credentials!)

**Access:** 
- Username + password (console) 
- Access Key ID + Secret (programmatic)

#### IAM Group
**What:** Group of people with similar job functions

**Purpose:** Easier to manage permissions for multiple people at once

**Example:**
- "Developers" group → all developers get the same permissions
- "Admins" group → all admins get admin permissions

**Rule:** Users can belong to multiple groups

#### IAM Roles
**What:** Temporary user and permission that AWS user can assume

**Characteristics:** No permanent credentials, more secure

**Why roles even though we have access keys?**

1. Access keys provide permanent credentials that can be used to authenticate API requests to AWS services, but they pose a higher security risk if exposed or compromised because they do not expire automatically.

2. In contrast, IAM roles are designed to eliminate the need for hardcoding long-term credentials by providing temporary security credentials that are automatically managed and rotated.

3. These temporary credentials are valid for a configurable period, typically between 15 minutes and 36 hours, and are automatically refreshed before expiration, reducing the risk of credential misuse.

**How roles work:**
- AWS service assumes role
- AWS provides temporary credentials which are more secure
- Example: EC2 accessing S3 using IAM role

---

### IAM Policy

**What:** A permission document that defines what actions are allowed/denied on which resource

**Format:** Written in JSON

**Example:**
```json
{
  "Effect": "Allow",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```
This tells AWS to allow upload object into my bucket.

#### Core Terms in Policy

| Element   | Meaning                        |
|-----------|--------------------------------|
| Effect    | Allow or Deny                  |
| Action    | What you can do                |
| Resource  | On which AWS resource          |
| Condition | Optional rules (IP, MFA, time) |

#### Flow of Usage of Policies

1. **Create EC2**
   - Which has CPU, RAM, disk
   - No permission to access any service

2. **Create IAM role** (ex: ec2-s3-role)
   - At this moment role exists, can do nothing, just an empty permission identity

3. **Attach policy to that role**
   - Allow read and write S3
   - Now role has permission but not used by anyone

4. **Attach that role to EC2**
   - Now EC2 is allowed to use permission in that role

**Simple Flow:**
```
EC2 → uses → ROLE → has → POLICY → allows → S3
```

**WHY DID AWS DESIGN ROLE LIKE THIS?**
- EC2 can be deleted
- EC2 can be replaced
- Role stays
- Policy stays
- Security stays clean

**Simply:** Service → assumes Role → Role has Policy → Policy allows access

**When AWS says "assume a role", it simply means:**
- EC2 is allowed to use that role's permissions
- AWS gives EC2 "temporary credentials"
- No passwords or access keys are stored

---

### 2 Types of IAM Policies

#### Managed Policies
**Use case:** I want the SAME permission in MULTIPLE places

**Example:**
- 5 developers need S3 read access
- EC2 and Lambda both need S3 access

**Benefit:** One policy → many attachments (reusable permission)

#### Inline Policies
**Use case:** The permission only here and nowhere else (embedded inside the user or role)

**Drawback:** Can't be reused, hard to manage

**Should I attach policy to USER or GROUP?**

❌ **Bad:**
```
User1 → policy
User2 → policy
User3 → policy
```

✅ **Good:**
```
Policy → Group → Users
```

---

### Trust Policy

**What:** Who gave permission to access this role

**Why needed?**
If roles gave permissions directly without control, anyone could misuse them — dangerous ❌

**Concept:**
- **Trust Policy** → "Who can enter?"
- **Permission Policy** → "What can they do inside?"

**Flow:**
1. EC2 instance starts
2. EC2 asks: "Can I use this role?"
3. Trust policy checks: Is EC2 allowed? ✅
4. AWS gives temporary credentials
5. EC2 uses permissions attached to role

**Main difference between permission and trust policy:**
- **Trust Policy** → EC2 can use role
- **Permission Policy** → Role can access S3

**Formula:**
```
ROLE = TRUST POLICY + PERMISSION POLICY
```

---

### MFA (Multi-Factor Authentication)

**Formula:** MFA = Something you KNOW + Something you HAVE

---

### IAM Password Policy

**What:** How strong password must be

**Requirements:**
- ✔ Minimum length
- ✔ Uppercase / lowercase
- ✔ Numbers
- ✔ Special characters
- ✔ Password expiry
- ✔ Prevent password reuse

**Password policy applies for:**
- ONLY for IAM USERS with console access
- ❌ Not for roles

**Why roles don't have MFA and password policy?**

The working of role is like a service is connected to role via trust policy and in role there are permissions by permission policy. MFA is required if machines are interacted by humans, but here there is communication between services so no MFA required.

---

### AWS Organizations

**What:** Manage multiple AWS accounts centrally

**Structure:**
```
Management Account (Master)
    |
    +--- Dev Account
    +--- Test Account
    +--- Prod Account
```

#### 1️⃣ Management Account
- Root of the organization
- Controls all other accounts
- Pays the bill
- Creates rules (SCPs)

#### 2️⃣ Member Accounts
- Used for workload
- Follow organization rules

**Why AWS prefers multiple accounts over one single big account:**
- Security isolation
- Separate billing
- Blast radius reduction
- Easier compliance

**Important:** AWS Organization doesn't replace IAM, doesn't give any permissions but controls what is allowed at account level.

---

### SCP (Service Control Policies)

**What:** Maximum allowed permissions for an account

**Key Points:**
- SCP doesn't give permissions, it only limits access
- SCP applies to only member account, not for IAM user, role, or management account
- Even if IAM allows → SCP can block
- SCP can be attached to root, group of accounts, individual account
- SCP does not attach to users or roles — it attaches to ACCOUNTS

**Flow:**
```
REQUEST
  ↓
SCP (Account-level)
  ↓
IAM Policy (User/Role-level)
  ↓
RESOURCE
```

- If SCP blocks → STOP ❌
- If SCP allows → IAM decides

**Summary:**
- **IAM** controls "WHO can do WHAT"
- **SCP** controls "WHAT this ACCOUNT is allowed to do"

---

### KMS (Key Management Service)

**Purpose:** Data should NOT be readable

**What:** Service that creates and manages encryption keys

**How it works:**
- You do not encrypt data yourself
- AWS services do it automatically using KMS keys
- KMS stores: Encryption keys, Key policies, Controls who can use keys
- AWS services use those keys to encrypt data

**Example:** 
We create S3 bucket, we enable encryption, AWS asks KMS for key, data is encrypted before writing into disk.

#### 2 Types of KMS Keys

**1. AWS Managed:**
- Created by AWS automatically when we enable encryption
- Gives limited control

**2. Customer Managed Keys:**
- Created by you
- Full control
- Can rotate
- Can audit

**Important:** Even if you have access to S3, you still need access to KMS key

**So TWO checks:**
1. S3 permission
2. KMS key permission

#### Flow of IAM, KMS, Policies

```
1️⃣ SCP (Account-level)
   ↓
2️⃣ IAM Policy (S3 permission)
   ↓
3️⃣ KMS Key Policy + IAM
   ↓
4️⃣ Object Access
```

#### Key Rotation
- **AWS managed keys** → auto-rotated
- **Customer managed keys** → optional auto-rotation (yearly)

**Important:** KMS keys are region-specific

---

### AWS Shield & WAF

**Scenario:** We host web app on CloudFront, API Gateway. Attackers send millions of requests, fake traffic, malicious payloads which creates steal of data and brings our application down.

#### 2 Different Types of Attacks

**1. Network Level Attack (Volume based)**
- Huge traffic flood
- SYN flood
- UDP flood

**2. Application Level Attack (Request content based)**
- SQL injection
- XSS
- Bad bots
- Fake login attempts

| Attack Type         | AWS Service |
|---------------------|-------------|
| Network / DDoS      | AWS Shield  |
| Application attacks | AWS WAF     |

---

### AWS Shield

**What:** Automatic DDoS protection for AWS resources

#### Types of Shields

**Shield Standard (Free and Default)**
- Enabled automatically, no setup required
- Protects ALB, NLB, Route 53, CloudFront

**Shield Advanced (Paid)**
- Advanced DDoS protection
- 24/7 AWS DDoS response team
- Cost protection during attack
- Advanced monitoring
- Used by banks, e-commerce, critical apps

**Important:**
- It does NOT inspect request content
- It does NOT block SQL injection

---

### AWS WAF

**What:** Filters HTTP/HTTPS requests based on rules

**Protects against:**
- SQL Injection
- XSS
- Bad IPs
- Bots
- Country-based blocks
- Rate limiting

**Can attach WAF to:**
- CloudFront
- ALB
- API Gateway
- ❌ Not to EC2 directly

**Flow:** Request → WAF rules → Allowed / Blocked → Resource

**Example Rules:**
- Block IP 1.2.3.4
- Block requests from China
- Allow only GET requests
- Block >1000 requests/min

---

### Secrets Manager vs Parameter Store

**Problem:** Where do I store passwords safely?

We have Database password, API key, Token. We can't:
- Hardcode
- Store in GitHub
- Put in plain text

So AWS gives secure storage services.

#### Secrets Manager

**What:** A service made specifically for passwords and credentials

**Best for:**
- Database password
- API key
- Token
- Credentials that need to rotate

**Cost:** Paid service

**Features:**
- Can rotate DB passwords automatically
- Update RDS credentials
- No app downtime

#### AWS Parameter Store

**What:** Secure storage for application configuration values

**Best for:**
- DB endpoint
- Feature flags
- App config values
- Non-rotating secrets

**Cost:** Free service

#### Comparison Table

| Feature               | Secrets Manager | Parameter Store |
|-----------------------|-----------------|-----------------|
| Best for              | Passwords       | Config values   |
| Automatic rotation    | ✅ YES          | ❌ NO           |
| Encryption            | KMS             | KMS             |
| Cost                  | 💰 Paid         | 🆓 Free         |
| Native DB integration | ✅ Yes          | ❌ No           |
| Exam preference       | ⭐ For secrets  | ⭐ For configs  |

---

### AWS CloudTrail

**What:** Whenever something happens in AWS, CloudTrail answers:
- ❓ Who did this?
- ❓ When did it happen?
- ❓ From where?
- ❓ What action was taken?

**Key Points:**
- CloudTrail records ALL AWS API calls
- API calls like EC2 creation, delete S3 bucket, modify security group

#### What CloudTrail Logs Contain

Each event records:
- ✔ IAM user / role
- ✔ Action performed
- ✔ Timestamp
- ✔ Source IP
- ✔ Region
- ✔ Success or failure

**This is audit logging.**

**Storage:**
- CloudTrail is ON by default
- Records management events
- Logs are stored at S3 / CloudWatch Logs

**Use cases:**
- Security audit
- Forensics
- Detect unauthorized actions

**What CloudTrail won't do:**
- Performance monitoring
- Resource metrics
- Application logs

**Summary:** CloudTrail = WHO did WHAT, WHEN, and FROM WHERE

---

### Shared Responsibility Model

**Concept:**
- AWS secures the cloud
- Customer secures what's IN the cloud

#### AWS Handles
- ✔ Physical data centers
- ✔ Hardware
- ✔ Network infrastructure
- ✔ Host OS (for managed services)

#### You Handle
- ✔ IAM users & roles
- ✔ Security groups
- ✔ Data encryption
- ✔ OS patching (for EC2)
- ✔ Application security

---

## 2️⃣ Compute Services

### Topics Covered
- EC2 instance types & families
- On-Demand vs Reserved vs Spot
- EC2 placement groups
- AMI creation & usage
- Auto Scaling Groups
- Elastic Load Balancer
- ALB vs NLB vs CLB
- Lambda (Use cases, Limits, Lambda + ALB/API Gateway)
- ECS vs EKS (high level)
- Batch (basics)

---

### 4 Main Compute Services

| Service       | Simple Meaning               |
|---------------|------------------------------|
| EC2           | Your own virtual server      |
| Auto Scaling  | Automatically add/remove EC2 |
| ELB           | Distribute traffic           |
| Lambda        | Run code without servers     |

---

### EC2 (Elastic Compute Cloud)

**What:** Virtual computer in AWS

**Control:**
- AWS controls data center and physical server
- User controls OS, patching, installation, security groups

#### When You Launch EC2, AWS Asks For

1. **AMI (Amazon Machine Image)** – Operating system
2. **Instance Type** – CPU + RAM
3. **Key Pair** – Login (SSH)
4. **Security Group** – Firewall
5. **Storage** – Disk (EBS)

#### AMI (Amazon Machine Image)

**What:** Template of OS + preinstalled software

**Examples:** Amazon Linux, Ubuntu, Windows Server

**Relationship:** One AMI → Many EC2 instances

#### Instance Type

**What:** Decides RAM, memory, performance

**Example:** t3.micro, m5.large

**In t3.micro:**

| Part  | Meaning    |
|-------|------------|
| t     | Family     |
| 3     | Generation |
| micro | Size       |

#### Instance Families

- **t, m** - General purpose for small apps, small DBs, dev/test
- **c** - CPU heavy like batch processing, high CPU apps
- **r** - RAM heavy like caching, in-memory DB
- **i, d** - Disk heavy and storage optimized, used for large data, analytics

**Tip:** Spiky traffic → t instance (because they are burstable, use CPU credits)

---

### EC2 Pricing Models

Different workloads → different cost strategies

Some apps need to run all the time, some need guaranteed capacity, some can tolerate interruption, so pricing varies.

#### 1. On-Demand

**Characteristics:**
- Pay only when the instance is running
- Short term and unpredictable workloads
- Most expensive / hour
- Best for testing env

#### 2. Reserved Instances

**Characteristics:**
- You commit to use EC2 for 1 or 3 years
- Big discount

**2 Types:**
- **Standard RI:** Max discount, can't change much
- **Convertible RI:** Less discount, can change instance family

**Used for:** 24/7 running servers, production, predictable workload

#### 3. Spot Instances

**Characteristics:**
- Use unused AWS capacity at huge discount
- AWS can terminate anytime (2-minute notice)
- Cheapest but interruptible

**Used for:**
- Batch jobs
- Data processing
- Fault-tolerant workloads

**NOT for:**
- Database
- Critical applications

**When to use:** Cost is highest priority

---

### Placement Groups

**Problem:** AWS places EC2 anywhere in a Region. We don't care where exactly it is, but sometimes we need very low latency, very high bandwidth, high availability.

**Solution:** Placement Group = tells AWS HOW to place EC2 instances physically

**Important:** Placement Group works ONLY within ONE AZ, not across AZs

#### 3 Types of Placement Groups

**1. Cluster Placement Group**

**Concept:** "Put all EC2s very close together"

**Characteristics:**
- Places EC2 on same rack / nearby racks
- Gives low latency + high bandwidth
- If rack fails, all EC2 down

**Use case:** Low latency, high throughput, HPC workload

**2. Spread Placement Group**

**Concept:** "Keep EC2s far apart"

**Characteristics:**
- Each EC2 on different hardware
- Even hardware failure won't affect others

**Use case:**
- Small apps
- Master nodes
- Minimize co-related failures
- High availability

**3. Partition Placement Group**

**Concept:** "Divides EC2s into partitions"

**Characteristics:**
- Each partition on separate hardware
- Failure affects only one partition

**Use case:**
- Large distributed systems
- Kafka
- Hadoop
- Fault isolation

---

### AMI (Amazon Machine Image)

**What:** Snapshot of an EC2 configuration

**Includes:**
- Operating System
- Installed software
- Configuration
- Attached root volume

**Important:** AMI is Region-specific

You cannot directly use AMI in another region; You must copy AMI.

#### How AMI is Created

1. Launch EC2 (normal)
2. Install everything like apps and config
3. Create AMI from EC2

**Note:** We can create AMI from running and stopped instance

#### Where AMI is Used

1. **Launch EC2** → Choose AMI and get identical server
2. **Auto Scaling Groups:** Launch Template → AMI (all EC2 are identical, so easy scaling)
3. **Disaster Recovery:** Restore EC2 from AMI; faster recovery

#### Types of AMI

- **Public:** Provided by AWS like Linux, macOS
- **Private:** Created by you and account specific
- **Marketplace AMI:** Paid / licensed software

---

### ASG (Auto Scaling Groups)

**What:** Group of EC2 instances that AWS manages automatically

**Purpose:** Ensure fault-tolerant, high availability, right number of EC2

**Important:** Auto Scaling Group is Region-wide but spans multiple AZs (This is key difference vs placement group)

#### ASG Needs 3 Inputs

**1. Launch Template**

Contains:
- EC2's AMI
- Instance type
- Security group
- Key pair

**2. Desired / Min / Max**

| Term        | Meaning            |
|-------------|--------------------|
| **Min**     | Minimum EC2        |
| **Desired** | Normal running EC2 |
| **Max**     | Maximum EC2        |

ASG will always try to keep Desired count.

**3. Scaling Policy**

Tells ASG when to add or remove EC2

#### Types of Scaling

**Target Tracking:**
- Keep CPU at 60%
- AWS automatically adds EC2 if CPU is down and vice versa

**Step Scaling:**
- Example: CPU > 70% → add 2 EC2
- CPU > 90% → add 5 EC2

**Schedule Scaling:**
- Scales at 9AM
- Reduces at 9PM

#### Health Checks

- ASG checks EC2 health, ELB health
- If EC2 fails, ASG terminates it and launches it automatically

**Key Points:**
- ASG replaces unhealthy EC2 automatically
- ASG works best with load balancer
- ASG ensures high availability across AZs

---

### Elastic Load Balancer (ELB)

**What:** Traffic Distributor

**Purpose:** Receives traffic from users and sends to healthy EC2 instances

**Flow:**
```
Users
  ↓
Load Balancer
  ↓
EC2 (Auto Scaling Group)
```

#### Types of Load Balancers

**1. ALB (Application Load Balancer)**

- **Layer:** 7 (HTTP/HTTPS)
- **Use case:** REST API
- **Features:**
  - Supports path, host-based routing
  - Microservices and containers

**2. NLB (Network Load Balancer)**

- **Layer:** 4 (TCP/UDP)
- **Features:**
  - Ultra low latency
  - Handles millions of requests
  - Supports static IP
- **Use case:** Gaming, VoIP, Financial apps

**3. CLB (Classic Load Balancer)**

- **Layer:** 4, 7
- **Use case:** Legacy applications

---

### Lambda

**What:** Run code without managing servers

**How it works:**
- Upload code and define trigger
- AWS manages servers, scales automatically, handles availability
- We won't manage servers in Lambda
- When event occurs, Lambda function runs and returns result

**Events can be:**
- API calls
- File upload to S3
- Schedule (cron)

#### When to Use Lambda

- Short-running tasks
- Event-driven
- Unpredictable traffic
- Low cost

#### Can't Be Used When

- Long-running jobs
- Heavy OS customization

| Use case           | Why Lambda  |
|--------------------|-------------|
| REST API backend   | Auto scale  |
| Image processing   | Event-based |
| Cron jobs          | No server   |
| S3 file processing | Triggered   |

#### Lambda Limits

- Execution time: Max 15 min
- CPU memory: 128MB - 10GB
- Storage: 512MB

#### Lambda + API

Can be exposed in 2 ways:

**1. API Gateway + Lambda**
- Used for REST API
- Flow: User → API Gateway → Lambda

**2. ALB + Lambda**
- For HTTP routing

#### Lambda Security

- Uses IAM Role
- No password
- Temporary credentials

#### Lambda Cost

- Pay for number of executions
- Execution time
- Not for idle time

---

### ECS vs EKS

#### ECS (Elastic Container Service)

**What:** AWS's own container manager

**Characteristics:**
- AWS controls control plane, scheduling, integration
- We just need to run container

**ECS runs on 2 modes:**

1. **ECS on EC2:** You manage EC2, ECS manages containers
2. **ECS on Fargate:** No EC2, AWS manages everything

**When to use:**
- Simple containers
- Don't want K8s integration
- AWS native

#### EKS (Elastic Kubernetes Service)

**What:** Managed Kubernetes on AWS

**Characteristics:**
- AWS manages control plane
- We manage worker nodes
- Open source orchestration
- EKS works across clouds like GCP, AWS, Azure (multi-cloud strategy)

#### Comparison Table

| Feature        | ECS     | EKS     |
|----------------|---------|---------|
| Complexity     | Simple  | Complex |
| Kubernetes     | ❌ No   | ✅ Yes  |
| AWS-native     | ✅ Yes  | ❌ No   |
| Multi-cloud    | ❌ No   | ✅ Yes  |
| Learning curve | Easy    | Steep   |

**Tip:** No server management → ECS/EKS Fargate

#### What is Fargate?

**What:** AWS runs the servers for containers (Serverless compute for containers)

**Important:** Fargate is NOT a container service; Fargate is a compute engine for containers

---

### AWS Batch

**Scenario:** "I have 10,000 jobs. Each job runs minutes or hours. They don't need to run immediately. Cost matters."

**Why not:**
- ❌ Lambda → time limit
- ❌ Manual EC2 → operational headache

**Solution:** AWS Batch exists for THIS exact case

**What:** Run large number of batch jobs without managing servers

**Batch job means:** No user interaction, runs in background like data processing

**Flow:**
```
Job (your task)
   ↓
Batch decides where to run it
   ↓
AWS runs it on compute
```

**Key Points:**
- AWS Batch manages compute for you
- Used when: Queue-based jobs, Large number of compute jobs, Cost optimized processing, Batch processing
- Can't be used when: Real-time API, Event-driven short task

#### Comparison Table

| Feature     | Lambda      | Batch           |
|-------------|-------------|-----------------|
| Time limit  | 15 min      | Hours           |
| Use case    | Event-based | Background jobs |
| Server mgmt | None        | AWS-managed     |
| Scale       | Auto        | Auto            |

---

## 3️⃣ Storage Services

### Topics Covered
- S3 (Buckets, objects, Storage classes, Lifecycle rules, Versioning, Replication, Encryption)
- EBS (Volume types, Snapshots)
- EFS
- FSx (Windows / Lustre – basics)
- AWS Backup

---

### AWS Storage Services

- **Object Storage** → S3
- **Block Storage** → EBS
- **File Storage** → EFS / FSx
- **Backup Service** → AWS Backup

---

### S3 (Amazon Simple Storage Service)

**What:** Unlimited storage bucket on internet

**Used for:**
- Storing images, videos, logs, backups
- Static websites

**Type:** Object storage

**Important:** S3 is not something that is attached to EC2, not a disk, not a filesystem

#### S3 Terms

**Bucket:**
- Top-level container that holds our files
- We can't store files directly in S3, must store inside bucket
- Bucket name is globally unique
- Created in one region
- If Bucket creation fails → usually name conflict
- S3 bucket exists in a region, but accessed globally
- One bucket can store unlimited objects, no size limit, can scale automatically

**Object:**
- Actual file
- If bucket is the container, object is the content
- Object contains: data (file), metadata, key (full path of the object)
- No folders in S3, they are just part of object key
- Max object size: 5 TB
- Upload > 5 GB → multipart upload

**Important:** S3 is flat storage, not hierarchical

#### Simple Scenario

You upload a file called report.pdf into S3:
- A bucket (already exists)
- An object called report.pdf

---

### S3 Storage Classes

**Why storage classes exist?**
- Old data → accessed rarely
- New data → accessed frequently
- Some data → archive only

AWS gives cheaper options for different access patterns.

| Requirement      | Class                | Features                                                  |
|------------------|----------------------|-----------------------------------------------------------|
| Frequent access  | Standard             | Low latency (default)                                     |
| Unknown access   | Intelligent          | Access pattern unknown & Moves objects between tiers      |
| Rare access      | Standard-IA          | Retrieval cost; used for backup and disaster recovery     |
| Archive          | Glacier              | Very cheap + Long-term archival                           |
| One AZ           | One Zone IA          | Cost optimization + Re-creatable data                     |
| 7–10 year backups| Glacier Deep Archive | Lowest cost, very rarely accessed                         |

---

### S3 Lifecycle Rules

**What:** Lifecycle rules automatically MOVE or DELETE S3 objects based on time

#### 2 Actions

**1. Transition:** Move object between storage classes
- Example: Standard → IA → Glacier

**2. Expiration:** Delete object permanently

#### Example: Application Logs

- First 30 days → accessed daily → S3 Standard
- Next 60 days → rarely accessed → Standard-IA
- After 90 days → archive → Glacier
- After 365 days → delete → expired

**Lifecycle rules are based on:**
- Time (days)
- Object version (current / noncurrent)
- Prefix or tag

**Benefit:** Reduce storage cost automatically over time

#### Versioning + Lifecycle

When versioning is enabled:
- Old versions = noncurrent versions
- Delete noncurrent versions after X days
- Move them to Glacier

**Lifecycle rules work at:**
- Bucket level
- Prefix level
- Tag level

---

### Versioning

**Scenario:** Imagine you upload a file "report.pdf". Later you overwrite it or someone deletes it. You need the old file back.

**Solution:** S3 versioning solves this problem

**What:** Versioning keeps multiple versions of the same object in S3

**Characteristics:**
- Old version still exists and can restore anytime
- Enabled at bucket level
- When you DELETE an object, S3 adds a delete marker
- Object looks deleted but old versions still stored

**In lifecycle POV:**
- Old versions move to Glacier
- Delete after some days

**Tip:** Unexpected S3 cost increase after enabling versioning → add lifecycle rule

---

### S3 Replication

**What:** Replication automatically copies objects from one S3 bucket to another

**Important:** Versioning MUST be enabled on BOTH buckets

**Tip:** If replication is not working, check versioning

#### Types of Replicas

**1. SRR (Same Region Replica)**
- Source bucket and destination bucket are in same AWS region
- Used for: Data isolation, log aggregation

**2. CRR (Cross Region Replica)**
- Source bucket in one region
- Destination bucket in another region
- Used for: Disaster recovery, global access
- Protects against regional failure

#### What Gets Replicated

✔️ New objects (after rule creation)
✔️ Object versions
✔️ Delete markers (optional)

#### Prefix & Tag with Replication

- Replicate only logs/
- Replicate only objects with tag Env=Prod

**Replication uses:**
- IAM Role
- Source bucket grants permission
- Destination bucket allows write

---

### S3 Encryption

**What:** Storing data in unreadable form

#### S3 Has 2 Types of Encryptions

**1. Encryption at Rest**

**SSE-S3 (Server Side Encryption):**
- AWS creates & manages keys
- Fully automatic and no extra setup
- Flow: You upload object → AWS encrypts → stores
- Default one with minimal effort
- For simple use cases

**SSE-KMS:**
- KMS managed keys
- You control permissions
- CloudTrail does audit
- Flow: S3 + KMS key → encrypted object
- You control who can use the key
- You can disable the key
- Get audit logs

**When accessing an S3 object encrypted with SSE-KMS:**
- Check 1: S3 permission
- Check 2: KMS permission
- If SCP denies kms:Decrypt or s3:GetObject → Access fails (Even if IAM allows it)

**Use case:** Compliance + audit + control → SSE-KMS

**2. Encryption at Transit**

**HTTPS:**
- If Data moving from your system → AWS S3, Use HTTPS (TLS)

**How to prevent users from uploading objects without encryption in transit?**

Answer: Set S3 Bucket Policy to Deny requests that are not HTTPS

| Encryption Type | Meaning                |
|-----------------|------------------------|
| At rest         | Data stored in S3      |
| In transit      | Data moving to/from S3 |

---

### EBS (Elastic Block Storage)

**What:** Hard disk for EC2 (EC2 is computer and EBS is disk attached to it)

**Scope:** Lives in ONE AZ

**Use case:** Needs low-latency disk for EC2 → EBS

**Flow:**
```
Create EBS volume
    ↓
Attach to EC2
    ↓
Format & mount
    ↓
Use like disk
```

#### Comparison Table

| Feature     | EBS           | S3             |
|-------------|---------------|----------------|
| Type        | Block storage | Object storage |
| Attached to | EC2           | Independent    |
| AZ scope    | Single AZ     | Regional       |
| Use case    | OS, DB, apps  | Files, backups |
| Access      | Low latency   | Over network   |

#### What Happens if EC2 Stops or Terminates?

**If EC2 is STOPPED:**
- EBS remains and data is safe

**If EC2 is TERMINATED:**
- Root EBS is DELETED when EC2 is terminated (root volume has OS, boot files)
- Extra EBS = additional disks you attached manually; They are NOT deleted

#### AZ Lock-In

An EBS volume can attach only to EC2 in same AZ (for low latency)

**If you want data in another AZ:** Create snapshot

---

### EBS Volume Types

Different workloads need different things based on speed, cost, consistency.

#### SSD Volumes (OS, DB, Applications)

**GP3 (General Purpose SSD):**
- Balanced cost and performance
- Used for boot volumes and general purpose workloads

**io1 / io2 (Provisioned IOPS SSD):**
- Input, Process, Output, and Storage → IOPS
- Used when high IOPS
- Mission-critical DB (Oracle, SAP)

#### HDD Volumes (Throughput Based)

Used for: Large files, sequential access, logs, big data

**st1 (Throughput Optimized HDD):**
- Big data and streaming
- Log processing
- High throughput, low cost

**sc1 (Cold HDD):**
- Cheapest
- For rarely accessed data

#### Legacy

Rarely used

**Summary:** SSD = IOPS | HDD = throughput

---

### EBS Snapshots

**Scenario:** Imagine EC2 disk crashes or AZ fails or we want backup

**What:** Snapshots are point-in-time backup of an EBS volume

**Storage:** These snapshots are stored at S3

| Item       | Scope    |
|------------|----------|
| EBS volume | Single AZ|
| Snapshot   | Regional |

**Regional means:** Like Asia Pacific, us-east

**Why snapshot is regional?**
- Can restore in any AZ

**Use case:** Move EBS data to another AZ/Region → create snapshot

**Characteristics:**
- First snapshot is full backup
- Next snapshots are incremental
- EC2 can run during snapshots

**AMI uses EBS snapshots:**
- To create identical EC2 instance we can use AMI via snapshots

---

### EFS (Elastic File System)

**Problem:** Imagine multiple EC2 instances, all need to access same files at same time. EBS and S3 can't support it.

**Solution:** EFS solves this issue

**What:** EFS is shared file system for EC2

**Characteristics:**
- File storage
- Shared by many EC2
- Multi-AZ
- High available

#### Comparison Table

| Feature      | EBS       | EFS          |
|--------------|-----------|--------------|
| Storage type | Block     | File         |
| Attach to    | One EC2   | Many EC2     |
| AZ           | Single AZ | Multi-AZ     |
| Use case     | OS, DB    | Shared files |

**Use cases:**
- Web servers sharing content
- CMS Systems
- Home directories

**Important:**
- Supports only Linux EC2
- For Windows, use FSx
- Scales automatically
- Pay for what you use

#### Security

- Uses NFS
- Controlled by security groups, IAM
- Supports encryption at rest and transit

---

### FSx

**Problem:** Some workloads need enterprise file systems, Windows native support, high performance computing

**Solution:** FSx provides all of them → managed file systems for special workloads

#### 2 Types

**1. FSx for Windows File Server**

**Characteristics:**
- Uses SMB protocol (Server Message Block)
- SMB enables secure, high-performance file sharing across Windows, Linux, and macOS instances
- Managed by Windows file system

**Used for:**
- Windows EC2
- Active Directory

**2. FSx for Lustre**

**What:** High performance file system

**Used for:**
- Big data
- ML
- HPC workloads
- Can integrate with S3
- High-throughput, low-latency file system for ML

---

### AWS Backup

**Problem:** Many services, each service has its own backup method, which is hard to manage

**Solution:** AWS Backup is centralized backup service (one place to manage backups for AWS services)

**Can do backup for:**
✅ EBS, EFS, RDS, DynamoDB, FSx
❌ But not for S3

#### Key Components

**Backup Plan:**
- Schedule and retention period

**Backup Vault:**
- Where backups are stored

**Resource Assignment:**
- Which resource to backup

#### Automation Policies

- Tag-based backups
- Schedule backups automatically
- Delete old backups automatically

#### Cross Region and Cross Account

- Copy backup to another region
- Copy to another account
- Disaster recovery backups

---

## 4️⃣ Databases (HIGH WEIGHT)

### Topics Covered
- RDS (MySQL, PostgreSQL, Oracle, SQL Server)
- Multi-AZ vs Read Replicas
- Aurora
- DynamoDB (Partition key vs sort key, Global tables, DAX)
- ElastiCache (Redis vs Memcached)
- Redshift (basics)
- Database migration (DMS, SCT)

---

### RDS (Relational Database Service)

**Problem:** Imagine you install MySQL on EC2 yourself. You need to manage:
- OS patching
- DB installation
- Backups
- Monitoring
- Failures

This is very difficult.

**Solution:** RDS manages all of them

**What:** Managed relational database service

**Concept:** You use the DB service, AWS manages infrastructure

#### You Manage
- Database schema
- Tables
- Queries
- Indexes
- Data

#### AWS Manages
- Server provisioning
- OS patching
- Database software patching
- Backups
- Monitoring
- Failover (if enabled)

**Supported database engines:**
- MySQL
- PostgreSQL
- Oracle
- SQL Server
- MariaDB

❌ **Not supported:** MongoDB, DynamoDB

**Important:**
- RDS is managed
- Runs inside VPC
- AZ based
- Not serverless (except Aurora)

**RDS can be accessed using:**
- Endpoints (DNS name)
- Port (3306, 5432)
- ❌ Not via SSH

**Storage:**
- RDS: EBS-Backed
- Backup: Automatic or manual snapshot

---

### RDS: Multi-AZ vs Read Replicas (VERY VERY IMPORTANT)

#### Multi-AZ (High Availability)

**Problem:** If DB instance fails / hardware fails / AZ outage, we need Multi-AZ

**Architecture:**
```
Primary DB (AZ-1)
    |
(Sync Replication)
    |
Standby DB (AZ-2) => Standby is not for reads => passive
```

**Key Characteristics:**
- Synchronous replication
- Automatic failover
- Same endpoints (DNS)
- Used for availability, not performance

#### Read Replicas

**Problem:** Many users, heavy read traffic, primary DB overloaded

**Solution:** Read replicas

**Architecture:**
```
Primary DB
   |
(Async Replication)
   |
Read Replica(s) => Replicas handle READS only
```

**Key Characteristics:**
- Asynchronous replication
- Separate endpoint
- Can have multiple replicas
- Used for scalability

#### Comparison Table

| Feature      | Multi-AZ      | Read Replica      |
|--------------|---------------|-------------------|
| Purpose      | Availability  | Scaling           |
| Replication  | Synchronous   | Asynchronous      |
| Read traffic | ❌ No         | ✅ Yes            |
| Failover     | Automatic     | ❌ Manual         |
| Endpoint     | Same          | Different         |
| AZ           | Different AZs | Same or different |

---

### Aurora

**Problem:** RDS has limited performance, storage tied to instance, slower failover

**Traditional RDS:**
```
DB Instance <- Attached Storage
```

**Aurora:**
```
Shared Distributed Storage <- DB Instances (stateless)
            ↑
    (6 copies across 3 AZs)
```

**What:** AWS-built, cloud-native relational database

#### Aurora Storage

- 6 copies of data
- Spread across 3 AZs
- Self-healing
- Auto-scaling

**Benefits:**
- No single AZ failure kills data
- No standby storage to attach
- Failover is FAST (seconds)

**Compatibility:** Aurora is very compatible

#### Aurora Serverless

**Problem in RDS:**
- Workload is unpredictable, intermittent
- Managing DB instances = waste

**Solution:** Aurora Serverless
- Auto-scales
- Pay only when you use
- No instance management
- Can handle infrequent or unpredictable workload

---

### DynamoDB

**Scenario:** Imagine an application with:
- Millions of users
- Spiky traffic
- Need minimal latency
- Must scale instantly

**Problem:** Aurora and RDS have limitations

**Solution:** If we remove joins, vertical scaling, fixed capacity, schemas → it's DynamoDB

**What:** Infinitely scalable key-value database

**Characteristics:**
- No servers, disks, capacity planning
- We don't run DynamoDB, we use it
- Built on partitions
- Data is split, distributed, stored across many partitions
- Simple access patterns

**Benefits:**
- Single digit latency
- Automatic scaling
- Multi-AZ
- Fully managed and serverless

---

### DynamoDB Partition Key vs Sort Key

**Purpose:** Keys help DynamoDB:
- Decide where to store data
- Retrieve data very fast

**Concept:** DynamoDB physically splits data into chunks called partitions

**Partition:**
- Stores subset of data
- Has limits
- Managed automatically by AWS

#### Partition Key

**What:** Decides where data is stored (WHICH server your data goes to)

**How it works:**
- DynamoDB applies a hash function on the partition key
- Hash result → chooses a physical partition
- Same partition key value goes to same partition

**Warning:** Same value repeated → ❌ hot partition (BAD) → poor partition key design

**Rule:** One item per partition key, no duplication

#### Sort Key

**What:** Decides ORDER of items within the same Partition Key

**Rule:** Partition Key + Sort Key together must be unique

**Benefit:** Allows multiple items per partition in ordered queues

**Example:** Query multiple items for the same user ordered by time

---

### DynamoDB Global Tables

**Scenario:** Users in India, Users in US, Users in Europe

**Problem:** If DynamoDB is in one region, users far away get higher latency

**Solution:** Global Tables allow DynamoDB to replicate data automatically across multiple AWS regions

**How it works:**
1. You create a DynamoDB table in Region A
2. You enable Global Tables
3. AWS creates replica tables in other regions
4. Data is automatically synced

**Characteristics:**
- Replication is fully automatic
- Managed
- No manual sync

**Benefit:** High Availability - If one region goes down → others still work

**What Global Tables Are NOT:**
- ❌ Not backup
- ❌ Not read-only replicas
- ❌ Not cross-account

---

### DAX (DynamoDB Accelerator)

**Problem:** DynamoDB is already fast, but some applications read same data again and again (like user info, product details)

**Issue:** Repeated reads → more cost, unnecessary latency

**Solution:** DAX is an in-memory cache for DynamoDB (cache in front of DynamoDB)

**Workflow:**
```
Application → DAX → DynamoDB
```

**Benefits:**
- Ultra-Fast Reads
- Microsecond latency
- Faster than DynamoDB alone
- Read-only optimization

---

### ElastiCache

**Problem:** Databases (RDS, DynamoDB, Aurora) are reliable, durable but slower than memory. Read the same data repeatedly but don't want to hit database every time.

**Solution:** ElastiCache is a fully managed in-memory data store used as a cache

**Characteristics:**
- Data stored in RAM
- Extremely fast
- Data can be temporary

**Use cases:**
- Cache frequently accessed data
- Improve performance
- Reduce database load

**Two Types:** Redis and Memcached

#### Comparison Table

| Feature    | DAX            | ElastiCache     |
|------------|----------------|-----------------|
| Works with | DynamoDB only  | Any database    |
| Use case   | DynamoDB reads | General caching |
| Type       | Specialized    | General-purpose |

---

### ElastiCache — Redis vs Memcached

**Both are:**
- In-memory
- Extremely fast
- Used for caching
- Managed by AWS via ElastiCache

#### Memcached

**What:** Simple in-memory key-value cache

**Characteristics:**
- No replication
- No persistence
- No backup
- No high availability
- Very simple
- Very fast

**Use cases:**
- Simple cache
- No need for HA
- Stateless data
- Scale out easily

#### Redis

**What:** Advanced in-memory data store

**Characteristics:**
- Replication
- High availability
- Persistence
- Backup & restore
- Supports data structures

**Use cases:**
- Session store
- Leaderboards / counters

---

### Amazon Redshift

**What:** A data warehouse used for analytics and reporting

**Use cases:**
- Business reports
- Analytics
- Aggregations
- Historical data analysis
- BI Tools

❌ **Not for:**
- Real-time transactions
- User login data
- Small frequent reads/writes

**Characteristics:**
- Column-Based Storage
- Works with TBs of data
- Heavy SELECT queries and aggregations

---

### Database Migration

**Problem:** Companies often want to:
- Move databases to AWS
- Change database engines
- Migrate with minimum downtime

**Solution:** AWS provides two tools, each with a different job

#### DMS (Database Migration Service)

**What:** Migrates data from one database to another

**Migrate data:**
- On-prem → AWS
- AWS → AWS
- One DB → another DB

**Supports:**
- Same engine (MySQL → MySQL)
- Different engine (Oracle → Aurora)

**Benefit:** Minimal downtime

**How it works:**
1. Source DB stays running
2. Target DB is created
3. DMS copies data
4. Keeps source & target in sync during migration

#### SCT (Schema Conversion Tool)

**What:** Converts database schema from one engine to another

**Converts:**
- Tables
- Indexes
- Procedures

**Used when migrating:**
- Oracle → Aurora
- SQL Server → PostgreSQL

**Important:**
- SCT doesn't move data
- SCT doesn't replicate changes

**Summary:**
- 🚚 **DMS** = Data Move
- 🧱 **SCT** = Structure Change

---

## 5️⃣ Networking (CRITICAL FOR EXAM)

### Topics Covered
- VPC (CIDR, Public vs private subnets, Route Tables)
- Internet Gateway
- NAT Gateway vs NAT Instance
- Security Groups vs NACLs
- VPC Peering
- Transit Gateway
- VPC Endpoints
- VPN vs Direct Connect
- Route 53 (Routing policies)
- CloudFront

---

### VPC (Virtual Private Cloud)

**Problem:** AWS services lived on shared infrastructure. Customers needed:
- Isolation
- Security
- Control

**Solution:** VPC

**What:** Your own private network inside AWS

**Important:** VPC itself does nothing until you build inside it

**By itself it has:**
- No internet
- No servers
- No traffic

**You will build:**
- Subnets
- Routing
- Gateway
- Security rules

**Scope:** One region only

#### 2 Types of VPC

**1. Default VPC:**
- AWS creates it
- Comes with subnet, internet access

**2. Custom VPC:**
- We build everything
- More control
- Used in production

**Almost all AWS Services connected to / run inside VPC:**
- EC2 → inside VPC
- RDS → inside VPC
- Lambda → can attach to VPC
- Load Balancer → inside VPC

---

### CIDR (Classless Inter-Domain Routing)

**Problem:** AWS must answer:
- Which IP addresses belong to your VPC?
- Which IPs belong to which subnet?

**Solution:** CIDR controls how many IP addresses AWS can allocate inside a VPC or subnet

#### CIDR Has Two Parts

```
10.0.0.0 / 16
│        │
│        └── how big the network is
└── starting IP
```

**An IPv4 address is:**
```
NETWORK PART | HOST PART => total 32 bits
```

**CIDR /X means:**
- X network bits
- So 32-X is number of host bits which can be used for VPC

#### CIDR is Used in Exactly 3 Places

1. VPC CIDR
2. Subnet CIDR
3. Networking connections (peering, VPN)

#### CIDR Rules AWS ENFORCES

1. Subnet must be inside VPC range
2. No Overlapping CIDRs: Subnets in same VPC → no overlap
3. You Cannot Change VPC CIDR Easily

**Example:**
```
VPC: 10.0.0.0/16
  ├─ Subnet A: 10.0.1.0/24
  └─ Subnet B: 10.0.2.0/24
```

**Key Concepts:**
- VPC is designed for scalability
- Subnets are designed for control and isolation

---

### Subnets

**Problem:** A VPC (10.0.0.0/16) → big IP space. Should everything live together in one block?

**Problems if everything is together:**
- No isolation
- No AZ separation
- Hard to control traffic

**Solution:** Subnet → A subnet is a smaller IP range carved out of a VPC

#### Subnet Characteristics

- Subnet belongs to One VPC
- One Availability Zone
- A subnet CANNOT span multiple AZs

**Why AWS Forces Subnet → AZ Binding:**
- AZ = isolated data center
- Subnet = network boundary
- Isolation must be preserved

#### Example

**VPC CIDR:** 10.0.0.0/16 → This is your entire network

**Region:** us-east-1 has 3 AZs: us-east-1a, us-east-1b, us-east-1c

**You can create:**
- Subnet 1: 10.0.1.0/24 in us-east-1a
- Subnet 2: 10.0.2.0/24 in us-east-1b
- Subnet 3: 10.0.3.0/24 in us-east-1c

✅ The VPC spans all 3 AZs
✅ Each subnet is in only one AZ

**Benefit:** High availability - if one AZ fails, resources in other AZs keep running

**Important:**
- You can have multiple subnets in the same AZ for different purposes
- Subnets do NOT decide internet access (done by routing tables)

---

### Public vs Private Subnets

**Important:** Subnets are NOT public or private by default. It becomes public or private only because of routing.

**Route table says:** If traffic wants to go somewhere, where should it go?

#### Public Subnet

**Definition:** A subnet is PUBLIC if its route table has a route to an Internet Gateway

**Flow:**
```
Subnet → Route Table → Internet Gateway → Internet
```

#### Private Subnet

**Definition:** A subnet is PRIVATE if its route table does NOT have a route to an Internet Gateway

🚫 **WRONG:** Subnet with public IPs is public → false (Routing decides, not IP assignment)

**Important:** Private subnet can access internet via NAT

**Private Subnet — "Hidden rooms":**
- Their route table does NOT point to IGW
- No inbound internet
- No direct outbound internet

**NAT Gateway:**
- Placed in public subnet
- Has a public IP
- Used by private subnet
- Route: 0.0.0.0/0 → NAT Gateway

**Result:**
```
Private EC2 → Internet ✅
Internet → Private EC2 ❌
```

---

### Route Tables

**Concept:** Every AWS creates a Route table by default when we create VPC. Every subnet uses it unless we explicitly associate with another one.

**Every route is like this:**
```
IF destination is X
THEN send traffic to Y
```

**Example:**
```
IF destination is 10.0.0.0/16
THEN send traffic locally
```

#### Two Most Important Routes

**Route 1:**
```
Destination: 10.0.0.0/16
Target: local
```
Traffic inside VPC, this route is automatic.

**Route 2:**
```
Destination: 0.0.0.0/0
Target: ???
```
0.0.0.0/0 means ANY IP ADDRESS (Google, YouTube, AWS console, updates, APIs, everything)

**Target = Internet Gateway (IGW):**
```
0.0.0.0/0 → IGW
```
Traffic goes directly to the internet {public}

**Target = NAT Gateway:**
```
0.0.0.0/0 → NAT Gateway
```
Go OUT to internet, but internet cannot start traffic back {private}

---

### ENI (Elastic Network Interface)

**What:** An ENI is a virtual network card inside a VPC

**Analogy:** Laptop has a Wi-Fi card, EC2 has ENI

**An ENI has:**
- Private IP address
- (Optional) Public IP / Elastic IP
- MAC address
- Security Groups

**Important:**
- EC2 always has at least one ENI
- That ENI is what connects EC2 to the subnet
- AWS creates an ENI inside your subnet
- That ENI has a private IP
- Your EC2 talks to that ENI
- Traffic never goes to internet

**Flow:**
```
EC2 (private subnet)
   | private IP
   v
ENI (Interface Endpoint)
   |
AWS Service (S3 / DynamoDB / etc)
```

**Question:** If an EC2 is in a public subnet, but has NO public IP, can it receive traffic from the internet?

**Answer:** ❌ No

**Reason:** A subnet is public only because its route table has a route to an Internet Gateway (IGW)

---

### Internet Gateway (IGW)

**What:** An Internet Gateway enables communication between a VPC and the public internet

**IGW does:**
- Connection between your VPC to the internet
- Allows two-way traffic

**Characteristics:**
- Attached to a VPC
- Horizontally scalable
- Highly available
- One IGW per VPC

#### What an IGW Actually Does

If a subnet's route table says:
```
0.0.0.0/0 → Internet Gateway
```
That subnet becomes a PUBLIC SUBNET

**Functions:**
1. **Route target:** Allows internet traffic when route table has 0.0.0.0/0 → IGW
2. **Public IP NAT:** Private IP ↔ Public IP

**Formula:** Public subnet + Public IP + IGW = internet access

#### What IGW Does NOT Do

- No firewall
- No security rules
- No traffic filtering

**Use IGW when:** Resource must be reachable from internet

---

### NAT Gateway vs NAT Instance

**Problem:** Allow private subnet resources to access the internet without being directly reachable from the internet

**Solution:** NAT

#### NAT Gateway

**What:** A managed AWS service that enables outbound-only internet access for private subnets

**Characteristics:**
- Must be deployed in a public subnet
- Uses an Elastic IP
- Highly available (within an AZ)
- Auto-scales
- No Security Groups
- No inbound connections from internet
- Can't exist in private IP

#### NAT Instance (EC2-based NAT)

**What:** An EC2 instance configured to perform NAT

**You manage:**
- Scaling
- Patching
- High availability

**Characteristics:**
- Must disable source/destination check
- Can attach Security Groups
- Lower cost
- Lower reliability

---

### Security Groups vs NACLs

| Feature    | Security Group | NACL         |
|------------|----------------|--------------|
| Applied to | EC2 / ENI      | Subnet       |
| Level      | Instance-level | Subnet-level |

**Analogy:**
- Security Group = bodyguard of EC2
- NACL = gate at subnet border

#### Detailed Comparison

| Feature            | Security Group    | NACL             |
|--------------------|-------------------|------------------|
| Stateful           | ✅ Yes            | ❌ No            |
| Stateless          | ❌ No             | ✅ Yes           |
| Allow rules        | ✅ Only allow     | ✅ Allow + Deny  |
| Default behavior   | Deny all inbound  | Allow all inbound|
| Rule order matters | ❌ No             | ✅ Yes           |

**Question:** You allowed inbound HTTP (80) in NACL, but website still not loading. Why?

**Answer:** Outbound ephemeral ports not allowed ❌

---

### What is a Security Group?

**Characteristics:**
- Instance-level firewall
- Attached to EC2 / ENI
- Controls who is allowed to communicate with the instance
- Deny by default
- Uses allow-only rules
- Security Groups focus on permissions, not restrictions

#### Why Security Groups do NOT have DENY rules?

**Reason:** AWS chose a fail-safe (fail-closed) security model

**Logic:**
- If something is not explicitly allowed → it is blocked
- This prevents accidental exposure

#### Why ALLOW-only is safer than ALLOW + DENY?

**Problems with ALLOW + DENY:**
- Rule conflicts
- Rule order dependency
- Ambiguity about precedence
- Higher risk of outages due to wrong deny rules
- Harder debugging in production

**Benefits of ALLOW-only:**
- No conflicts
- No precedence issues
- Predictable behavior
- Easier to reason about security
- Safer for large teams

**Important:** An EC2 instance CAN have multiple Security Groups. AWS combines them using OR logic.

---

### Network ACLs (NACL)

**Characteristics:**
- Subnet-level firewall
- Attached to subnets
- Stateless
- Supports ALLOW and DENY
- Rule order matters

**Relationship:**
```
One subnet → One NACL
One NACL → Many subnets
```

---

### Summary Flow

```
VPC gives space → CIDR sizes it → Subnets divide it → Route tables guide traffic → 
IGW gives public access → NAT gives private outbound → Security Groups protect instances 
→ NACLs protect subnets
```

---

### VPC Endpoints

**Problem:** How does a private EC2 access S3 by default?

**Default flow:**
```
Private EC2 → NAT Gateway → Internet → S3
```

**Problems with this approach:**
- Traffic goes over the internet
- NAT Gateway cost 💸
- Security risk (even if encrypted)
- Some companies do not allow internet access at all

#### What is a VPC Endpoint?

**What:** A VPC Endpoint allows private resources in a VPC to access AWS services privately, without using the internet, NAT, or IGW

**Benefits:**
- No public IP
- No IG
- Traffic inside AWS network

#### 2 Types of VPC Endpoints

**1. Gateway Endpoint**

**What services use Gateway Endpoints?**
- S3
- DynamoDB

**How:**
```
Private EC2 → VPC Endpoint → S3
```

**Where Gateway Endpoint is configured?**

Route table:
```
Destination: S3 Prefix List
Target: VPC Endpoint
```

**Gateway Endpoint does NOT need:**
- ❌ Internet Gateway
- ❌ NAT Gateway
- ❌ Security Groups
- ❌ Public IP

**Benefits:** Cheap, simple, secure

**Summary:** Gateway Endpoints provide private connectivity to S3 and DynamoDB by adding routes to the VPC route table

---

**2. Interface VPC Endpoints**

**What:** An Interface VPC Endpoint allows private connectivity to AWS services using a private IP inside your VPC

**Uses:**
- PrivateLink
- Creates an ENI (Elastic Network Interface) in your subnet
- Traffic stays inside AWS network

**Most AWS services use this.** If it's NOT S3 or DynamoDB → it uses Interface Endpoint

**How it looks:**
```
Private EC2 → ENI (Private IP) → AWS Service
```

**Characteristics:**
- Interface Endpoint is created inside a SUBNET
- One ENI per AZ
- Has private IP
- Uses security group

| Feature              | Interface Endpoint |
|----------------------|--------------------|
| Uses Security Groups | ✅ YES             |
| Uses Route Table     | ❌ NO              |
| Has Private IP       | ✅ YES             |

**Important:** You must allow inbound traffic in SG attached to the endpoint ENI

**By default:** AWS enables Private DNS so Public AWS service DNS resolves to private IP

**Cost:**
- Interface Endpoints COST MONEY
- Charged per hour + per GB
- Gateway Endpoints are FREE

**Use when:**
- Private subnet needs AWS service access
- No internet allowed
- Compliance / security requirement
- Access to services other than S3/DynamoDB
- No usage of NAT or IG

---

### Summary Flow

```
VPC gives IP space → Subnets divide it → Route tables decide direction → IGW gives public access 
→ NAT gives private outbound → SG protects instances → NACL protects subnets
```

---

### VPC Peering

**Scenario:** You have two VPCs. They need private communication. No internet, simple setup.

**Solution:** VPC Peering

**What:** VPC Peering is a private, one-to-one network connection between two VPCs using AWS's internal network

**Benefits:**
- Traffic stays on AWS network
- No internet
- Low latency
- Simple

**Characteristics:**
- One-to-One only: One VPC ↔ One VPC
- No transitive routing: ❌ VPC-A ↔ VPC-B ↔ VPC-C
- CIDR must NOT overlap
- Routing is manual: You must add routes in both VPCs' route tables

---

### Transit Gateway

**Scenario:** You have:
- VPC-A (Prod)
- VPC-B (Dev)
- VPC-C (Shared Services)

They all need to talk to each other.

**Solution with VPC Peering:**
Connect them like: A ↔ B, A ↔ C, B ↔ C

**Problem:** Now imagine 10 VPCs
- How many connections? 45 peering connections
- Routing tables become a nightmare
- No transitive routing
- Not scalable

**Solution:** Think of Transit Gateway as a hub. All VPCs connect to the hub. Traffic flows through the hub.

**Architecture:**
```
VPC-A \
       → Transit Gateway → Other VPCs / VPN / DX
VPC-B /
```

#### What Can Connect to a Transit Gateway?

- VPCs
- VPN connections
- Direct Connect
- Other Transit Gateways (inter-region)

This makes it extremely powerful.

#### Key Properties

**Hub-and-Spoke model:**
- TGW is the hub
- VPCs are spokes

**Transitive routing is ALLOWED:**

Example:
```
VPC-A → TGW
TGW → VPC-B
VPC-B → TGW
TGW → VPN (on-prem)
```
Traffic can flow end-to-end. VPC Peering does NOT allow this.

**Centralized routing control:**
- Instead of managing 10 route tables in 10 VPCs
- Routes in one Transit Gateway

---

### Important Distinction

- **VPC Endpoints** are for private access to AWS services (like S3, DynamoDB) without going over the internet
- **VPC Peering and Transit Gateway** are for private connectivity between VPCs (and on-prem networks in the case of Transit Gateway)

#### Comparison Table

| Feature                  | VPC Peering                  | Transit Gateway                        | VPC Endpoints                                   |
|--------------------------|------------------------------|----------------------------------------|-------------------------------------------------|
| **Purpose**              | Connect two VPCs directly    | Connect many VPCs and on-prem networks | Private access to AWS services                  |
| **Connectivity Model**   | One-to-one                   | One-to-many (hub-and-spoke)            | VPC → AWS service                               |
| **Transitive Routing**   | ❌ Not supported             | ✅ Supported                           | N/A                                             |
| **Scalability**          | ❌ Limited (mesh complexity) | ✅ High (centralized routing)          | ✅ High                                         |
| **Routing Management**   | Manual per VPC               | Centralized at TGW                     | Managed by AWS                                  |
| **Cross-Region Support** | ✅ Inter-region peering      | ✅ Inter-region TGW peering            | ✅                                              |
| **Internet Required**    | ❌ No                        | ❌ No                                  | ❌ No                                           |
| **Cost Model**           | Low (data transfer only)     | Higher (attachments + data processing) | Low (endpoint + data)                           |
| **Typical Use Case**     | Simple 2-VPC communication   | Enterprise multi-VPC architectures     | Secure private access to S3, DynamoDB, AWS APIs |

---

### VPN vs Direct Connect

**Scenario:** You have:
- An on-premises data center (office network)
- AWS VPC in the cloud

**You want:**
- Private communication
- Secure connectivity
- Your servers to talk to AWS resources

**AWS gives two ways to connect:** VPN and Direct Connect (DX)

---

#### Site-to-Site VPN — "Secure tunnel over the internet"

**What:** A VPN creates an encrypted tunnel between your on-prem network and AWS over the public internet

**Characteristics:**
- Encrypted
- Sent through the internet
- Secure but internet-dependent
- Uses Internet
- Encrypted (IPSec)
- Quick to set up
- Lower cost
- Variable latency & bandwidth

**When to use VPN:**
- Small or medium workloads
- Temporary connectivity
- Backup connection
- Cost-sensitive setups

---

#### Direct Connect — "Private highway to AWS"

**What:** A dedicated physical network connection from your data center directly into AWS

**Characteristics:**
- Doesn't use internet
- Private
- High bandwidth
- Consistent performance
- Low latency
- Enterprise setup

---

### Route 53

**What:** Amazon Route 53 is AWS's managed DNS service that translates domain names into IP addresses and routes users to AWS resources

**Why "53"?** DNS uses port 53, TCP/UDP port 53

**Route 53 can point users to:**
- EC2 (public IP / Elastic IP)
- Application Load Balancer
- Network Load Balancer
- CloudFront
- S3 static website
- Even external servers

---

#### Hosted Zones

**What:** A Hosted Zone is a container for DNS records

**2 Types:**

**1. Public Hosted Zone:**
- For internet-facing domains
- Example: example.com

**2. Private Hosted Zone:**
- Only accessible inside a VPC
- Used for internal services
- Example: db.internal.company
- Also known as "Internal DNS"

---

#### DNS Records

**A Record:** Name → IPv4 address

**AAAA Record:** Name → IPv6 address

**CNAME:** Name → another name

**Alias:**
- Alias records point a domain directly to AWS resources without extra cost
- "Root domain + AWS resource" → Alias
- Alias avoids extra DNS lookup
- Alias works at root domain
- Used for: ALB, CloudFront, S3 website, Route 53 native

**Important:** Route 53 does not charge for Alias queries. CNAME queries are charged like normal DNS queries.

---

#### How Route 53 Actually Works

1. User types domain
2. DNS query goes to Route 53
3. Route 53 checks records
4. Returns IP / AWS resource
5. User connects

**Route 53 is not just DNS. It also does:**
- Health checks
- Intelligent traffic routing
- Failover
- Load distribution

---

### Route 53 Routing Policies

**Important:** Routing policies decide HOW Route 53 responds to DNS queries, not how packets flow on the network

---

#### 1. Simple Routing Policy — "Just give me one answer"

**Scenario:** You have:
- One EC2
- One ALB
- One CloudFront distribution

**Behavior:**
- No failover
- No load balancing logic
- Just return the value

**Characteristics:**
- ❌ No health checks (important)
- Default routing policy
- Single resource
- Can return multiple IPs (random)

---

#### 2. Weighted Routing Policy — "Split traffic by percentage"

**Scenario:** You want:
- 90% traffic → Old version
- 10% traffic → New version (testing)

**How it works:**
- You assign weights
- App-A → weight 90
- App-B → weight 10
- Route 53 distributes traffic proportionally

**Use cases:**
- Blue/Green deployments
- Canary testing
- Gradual rollouts

---

#### 3. Failover Routing Policy — "Primary / Backup"

**Scenario:** You have:
- Primary site (Region A)
- Backup site (Region B)

**Behavior:** If primary fails, automatically route to backup

**Key requirements:**
- Health checks REQUIRED

**Use cases:**
- "Disaster recovery"
- "Active-passive"

---

#### 4. Latency-Based Routing — "Closest region wins"

**Scenario:** Users are global:
- India
- US
- Europe

You deploy apps in:
- Mumbai
- Virginia
- Frankfurt

**Behavior:** Route 53 sends users to the lowest-latency region

**Based on:**
- AWS latency measurements
- Not geography, but network latency

---

#### 5. Geolocation Routing — "User country decides"

**Scenario:** You want:
- India users → India site
- US users → US site
- France users → France site

**Behavior:** Route based on user location

---

#### 6. Geoproximity Routing — "Distance + bias" (ADVANCED)

**Scenario:** You want:
- Route users to nearest resource
- BUT you want to shift traffic manually

**Example:** Move more traffic to India by adding bias

**Important:** Can shift traffic using bias values

---

#### 7. Multi-Value Answer Routing — "DNS-level load balancing"

**Scenario:** You have:
- Multiple EC2 instances
- All healthy

**Behavior:**
- Route 53 returns multiple IPs
- Removes unhealthy ones

**Key difference vs Simple:**
- Uses health checks
- Improves availability
- NOT a load balancer

**Use case:** Multiple healthy IPs

---

#### Routing Policies Summary Table

| Policy       | Main Idea       | Health Check |
|--------------|-----------------|--------------|
| Simple       | One resource    | ❌           |
| Weighted     | % traffic       | Optional     |
| Failover     | Primary/Backup  | ✅ Required  |
| Latency      | Closest region  | ❌           |
| Geolocation  | Country-based   | ❌           |
| Geoproximity | Distance + bias | ❌           |
| Multi-Value  | Multiple IPs    | ✅           |

---

### CloudFront

**Problem:** Without CloudFront:
- Long distance → higher latency
- Slower page loads
- Poor user experience

**Solution:** Amazon CloudFront is a Content Delivery Network (CDN) that delivers content to users from locations closer to them. It caches content at Edge Locations around the world.

---

#### Key Concepts

**Edge Locations:**
- Global data centers
- Cache content close to users
- Not the same as AWS Regions

**Origin:**
- Where CloudFront gets content
- Can be: S3 bucket, ALB, EC2, Any HTTP server

---

#### How CloudFront Works

1. User requests content
2. Request goes to nearest Edge Location
3. If cached → served immediately
4. If not cached → fetched from Origin, cached, then served

---

#### CloudFront Provides

- Faster content delivery
- Reduced load on backend
- Built-in DDoS protection (with AWS Shield)
- HTTPS support
- Global scalability

---

### CloudFront + Route 53

**Typical setup:**
```
Route 53 → Domain name
Alias record → CloudFront distribution
CloudFront → Origin (S3 / ALB)
```

**Architecture:**
```
Route 53
   ↓
CloudFront (GLOBAL, cache)
   ↓
ALB (REGIONAL, balance)
   ↓
EC2 (servers)
```

**Note:** ALB distributes incoming traffic to multiple servers (EC2 / containers) inside AWS

---

## 6️⃣ Application Integration

### Topics Covered
- SQS (Standard vs FIFO)
- SNS
- EventBridge
- Step Functions
- API Gateway
- Decoupled architecture patterns

---

### AWS Simple Queue Service (SQS)

#### Why SQS Exists

**Scenario (real life):**
- User uploads a file
- Your app must:
  - Resize image
  - Send email
  - Update database

**Problem:** If your app does everything synchronously:
- User waits ❌
- If one step fails → whole request fails ❌
- Traffic spike → app crashes ❌

**Issue:** Tight coupling = fragile system

---

**Solution:** Amazon SQS is a fully managed message queue service

**Concept:**
- One service puts messages (producer)
- Another service reads messages (consumer)
- They won't talk directly → communicate through queue

**Benefits:**
- SQS decouples components of an application
- SQS is reliable (stores msgs in multiple AZ)
- Scalable (can handle millions of msgs)
- Failure isolation (producer doesn't care if consumer is down)

---

#### How SQS Works

1. **Your application (producer) writes a message**
   - It drops the message into SQS
   - Producer's job ends here

2. **Producer doesn't wait for processing**
   - Also doesn't know who will read it

3. **SQS stores msgs safely**
   - Replicated across multiple AZs

4. **Even if server fails → msg survives** (durable)

5. **Consumer polls SQS**
   - "Do you have any messages for me?"

6. **SQS hands over msgs**
   - Now msgs are locked and hidden from others
   - This hiding period is called visibility timeout

7. **Consumer does the actual work**
   - Re-size image, send email, update DB
   - No other consumer can see this message
   - Message is locked temporarily

8. **After successful processing**
   - Consumer says: "I'm done, delete this message"
   - Now message is permanently removed → job done

9. **If Consumer CRASHES before deleting**
   - Visibility timeout expires
   - Message becomes visible again
   - Another consumer picks the msg/task (reliability)

---

**Flow:** Receive → Process → Delete

**Important:** If delete does not happen → message reappears

**Key Point:** SQS assumes failure WILL happen. SQS ensures At-least-once delivery.

---

### Standard vs FIFO SQS

**Concept:** SQS is for decoupling systems

**Instead of:**
```
Producer → Consumer (tight coupling)
```

**We follow:**
```
Producer → SQS → Consumer(s)
```

Now failures, scaling, spikes = handled safely

---

**AWS observed two different needs:**

| Need                         | Example                |
|------------------------------|------------------------|
| Maximum throughput & scale   | Logs, metrics, events  |
| Strict order + no duplicates | Payments, transactions |

**Important:** One queue cannot optimize for both

---

#### Standard SQS

**Optimizes for:** Scale & performance

**Guarantees:**
- At-least-once delivery
- Msg can arrive in any order
- Because AWS stores messages across multiple distributed servers for scale

**Example:**
Let's consider msgs m1, m2 are in queue. While processing m1, its consumer failed and another consumer took that msg. Meanwhile m2 was already processed and deleted from SQS, so now m2 delivered before m1.

**Suitable for:**
- Log processing
- Event ingestion
- Metrics pipeline
- Email notification

**Key:** Order doesn't matter and duplication allowed

---

#### FIFO SQS

**Optimizes for:** Correctness over speed

**Guarantees:**
- Exactly-once processing (with deduplication)
- Strict ordering

**FIFO uses:**
- Message Group ID → ordering within a group
- Deduplication ID → removes duplicates
- Limited throughput (by design)

**Important:** Ordering is guaranteed within the same Message Group ID, not across different groups

**Used for:**
- Payment processing
- Bank transactions
- Inventory updates
- Order management

---

### SNS (Simple Notification Service)

**What:** Publish-subscribe service

**Concept:** SNS pushes messages to multiple subscribers at the same time

---

#### Basic Components

**Topic:** Central place where messages are published

**Publisher:** Sends message to the topic

**Subscriber:** Receives the message

---

#### What Can Subscribe to SNS?

SNS can send messages to:
- SQS queues
- Lambda functions
- HTTP / HTTPS endpoints
- Email
- SMS
- Mobile push notifications

**Key:** One message → many receivers

**Important:** SNS does not store messages long-term

---

**Architecture:**
```
Producer
    |
    v
SNS Topic
 /  |  \
SQS Lambda Email
```

---

**SNS behavior:**
- SNS does NOT wait for consumers
- SNS does NOT retry forever
- SNS does NOT guarantee processing

---

#### When to Use SNS?

- Send notification
- Alerts and alarms
- Fan-out events to multiple systems

**Fanout:** A messaging pattern where a single message published to an SNS topic is replicated and delivered to multiple subscribers simultaneously. This enables parallel, asynchronous processing of the same message by different consumers.

---

#### Comparison Table

| Service | Core Purpose                         |
|---------|--------------------------------------|
| SNS     | Notify many consumers                |
| SQS     | Decouple & reliably process messages |

---

**SNS → Push:**
- Message is sent immediately
- Subscribers don't ask for it

**SQS → Pull:**
- Consumers poll the queue
- Messages wait safely

---

### EventBridge

**What:** Event-driven routing service

**Concept:** It routes events from sources to targets based on rules

**Formula:** If THIS happens → do THAT automatically

---

**Scenario:** Imagine a company automation system:
- If payment succeeds → send email
- If EC2 stops → trigger Lambda
- If order placed → update database

**This decision-making router is EventBridge.**

---

#### Comparison Table

| SNS                | EventBridge        |
|--------------------|--------------------|
| Pushes messages    | Routes events      |
| Simple pub-sub     | Rule-based         |
| No filtering logic | Advanced filtering |
| Manual subscribers | Automatic targets  |

**Key:** EventBridge decides who should get the event

---

#### Core Components

**1. Event Source:**
Where the event comes from:
- AWS services (EC2, S3, RDS, etc.)
- Custom applications
- SaaS (like Zendesk, GitHub)

**2. Event:**
A JSON object describing what happened

**Example:**
```json
{
  "source": "aws.ec2",
  "detail-type": "EC2 Instance State Change",
  "detail": {
    "state": "stopped"
  }
}
```

**3. Rule:**
Rules say:
- What event to match
- What target to trigger

**Example:** If EC2 state = stopped → trigger Lambda

**4. Target:**
What action to take:
- Lambda
- SQS
- SNS
- Step Functions
- API Gateway

---

**Architecture:**
```
Event Source
     |
     v
EventBridge
     |
   Rules
     |
  Targets
```

---

#### Why AWS Created EventBridge?

**SNS was not enough because:**
- No event filtering
- No conditional routing
- No service-to-service automation

**EventBridge solves:**
- Loose coupling
- Event-driven architectures
- Microservices communication

---

#### When Should You Use EventBridge?

- React to AWS service events
- Automate workflows
- Event-driven workflows
- SaaS integrations

---

#### Summary Table

| Service     | Main Job      |
|-------------|---------------|
| SNS         | Notify        |
| SQS         | Queue & retry |
| EventBridge | Route events  |

---

### AWS Step Functions

**What:** Workflow orchestration service

**Example:** "Do step A → then B → if failed retry → else go to C"

**Concept:** Coordinates multiple AWS services in a defined sequence

---

**Without Step Functions:**
- Lambda calls Lambda manually
- Error handling written in code
- Retries & timeouts are complex
- Hard to visualize flow

---

#### Core Components

**State Machine:**
- The workflow definition (blueprint)
- Written in Amazon States Language (JSON)

**States:**
Each step in the workflow:
- Task (do work)
- Choice (if/else)
- Wait
- Retry
- Fail / Succeed

**Tasks:**
Tasks call:
- Lambda
- ECS
- Batch
- SNS
- SQS
- API Gateway

---

**Example Flow:**
```
Start
  |
Lambda A
  |
(success?)
  |     \
Yes      No
 |        |
Lambda B  Retry
  |
End
```

All managed by AWS.

---

#### Advantages

- Built-in retries
- Built-in error handling
- State tracking
- No custom orchestration
- Visual workflows

---

#### Types of Step Functions

**Standard:**
- Long running
- Exactly-once execution
- Used for business workflow

**Express:**
- Short-lived
- High volume
- Event processing

---

#### Comparison Table

| Lambda chaining | Step Functions  |
|-----------------|-----------------|
| Complex code    | Visual workflow |
| Manual retries  | Built-in        |
| Hard to debug   | Easy            |
| Tight coupling  | Loose coupling  |

---

#### When to Use Step Functions

- Multi-step business processing
- Order processing
- Payment workflow
- Orchestration of microservices

---

### API Gateway

**What:** Managed front door for backend

**Concept:** It receives requests from clients and forwards them to backend services

**Important:** Users don't talk directly to Lambda / EC2 — they talk to API Gateway

---

**Without API Gateway:**
- Clients hit Lambda or EC2 directly
- No security
- No throttling
- No monitoring
- No versioning

**AWS created API Gateway to:**
- Secure APIs
- Control traffic
- Manage versions
- Monitor usage

---

**Architecture:**
```
Client (Browser / App)
        |
        v
   API Gateway
        |
        v
Backend (Lambda / EC2 / ALB)
```

---

#### What Can API Gateway Connect To?

- Lambda (most common)
- EC2
- Application Load Balancer
- HTTP endpoints
- AWS services

---

#### Key Features

**Serverless:** No servers to manage

**Native Integration:** Built-in capability to directly connect and interact with various AWS services

**Cost Efficient:** Pay per request

**Authentication & Authorization:**
- IAM
- Cognito
- Custom authorizers

**Throttling & Rate Limiting:**
- Prevents abuse
- Protects backend

**Caching:**
- Reduces backend calls
- Improves performance
- Reduces cost ⭐

**Monitoring:**
- CloudWatch logs & metrics

**Summary:** "API features" → API Gateway

---

### Decoupled Architecture Patterns

**What:** Decoupled architecture = components do NOT depend on each other directly

**Meaning:**
- One service can fail
- Other services keep working
- No tight dependencies

---

**Tight Couple:**
```
Frontend → Backend → Database
```

**Problems:**
- Backend down → Frontend down
- Hard to scale
- Hard to change

---

**Decoupled:**
```
Frontend → API Gateway → SQS → Backend
```

**Benefits:**
- Backend can fail
- Messages are not lost
- Easy scaling
- Independent deployments

---

#### Service Roles in Decoupling

| Service         | Role in Decoupling  |
|-----------------|---------------------|
| SQS             | Buffer & retry      |
| SNS             | Fan-out             |
| EventBridge     | Event routing       |
| Step Functions  | Workflow decoupling |
| API Gateway     | Client isolation    |

---

#### Common Decoupled Patterns

**1. Queue-based decoupling:**
```
Producer → SQS → Consumer
```

**Benefits:**
- Absorbs traffic spikes
- Consumer can fail safely
- Retry handled automatically

**Use case:** "Handle spikes"

---

**2. Fan-out pattern:**
```
Producer → SNS → Multiple consumers
```

**Concept:** One event → many systems

**Use case:** Notification, parallel processing

---

**3. Reliable fan-out:**
```
Producer → SNS → SQS → Consumers
```

**Benefit:** Fanout + durability

---

**4. Event-driven architecture:**
```
AWS Service → EventBridge → Targets
```

**Use case:**
- Automation
- SaaS integration
- Microservices

---

**5. Workflow decoupling:**
```
Event → Step Functions → Services
```

**Use case:**
- Multi-step business logic
- Long-running workflows

---

#### Summary Table

| Requirement           | Use            |
|-----------------------|----------------|
| Decouple services     | SQS            |
| Fan-out               | SNS            |
| Fan-out + reliability | SNS + SQS      |
| React to events       | EventBridge    |
| Orchestration         | Step Functions |

---

## 7️⃣ High Availability & Disaster Recovery

### Topics Covered
- Multi-AZ vs Multi-Region
- Backup & restore
- Pilot light
- Warm standby
- Active-active
- Fault tolerance strategies

---

### One Absolutely Critical Clarification

**High Availability ≠ Disaster Recovery**

---

### High Availability (HA)

**Goal:** Keep the application running during normal failures

**Examples:**
- EC2 instance crash
- AZ goes down
- Hardware failure

**Focus:**
- Minutes or seconds
- Automatic recovery
- No human intervention

---

### Disaster Recovery (DR)

**Goal:** Recover application from major disasters

**Examples:**
- Entire region down
- Data corruption
- Accidental deletion
- Cyber attack

**Focus:**
- Data recovery
- Region-level failures
- Planned recovery strategies

---

#### Comparison Table

| Concept      | Protects Against     |
|--------------|----------------------|
| Multi-AZ     | AZ failures (HA)     |
| Multi-Region | Region failures (DR) |

---

### Multi-AZ

**What:** Resources are deployed in multiple Availability Zones within the same region

**Protects from:**
- EC2 failure
- AZ outage
- Hardware issues

**Does NOT protect from:**
- Region outage
- Large-scale disasters

**AWS services that are Multi-AZ by default:**
- RDS (Multi-AZ)
- ALB
- DynamoDB
- SQS
- ELB

**Summary:** Multi-AZ deployments provide high availability by running resources across multiple Availability Zones within a region

---

### Multi-Region (Disaster Recovery)

**What:** Resources are deployed in more than one AWS region

**Architecture:**
```
Region A (Primary)
Region B (Secondary)
```

**Protects from:**
- Region-wide outage
- Natural disasters
- Large-scale failures

**Trade-offs:**
- Higher cost
- More complexity
- Data synchronization needed

---

### Backup and Restore

**Disaster Recovery Strategy #1:** Backup & Restore

**Concept:** This is the simplest and cheapest DR strategy

**What:** You take backups of data and configurations and restore them in another region when a disaster happens

**Important:** No resources are running in the DR region until disaster occurs

---

#### How It Works

1. Application runs in Primary Region
2. Regular backups are taken:
   - EBS Snapshots
   - RDS automated backups
3. Backups are stored in another region
4. If disaster happens, you manually restore infrastructure in DR region

---

**AWS Services:**
- S3 – backup storage
- EBS Snapshots
- RDS Snapshots
- AWS Backup
- CloudFormation / Terraform (to rebuild infra)

---

#### Metrics

| Metric                         | Value                                |
|--------------------------------|--------------------------------------|
| RTO (Recovery Time Objective)  | High (hours to days)                 |
| RPO (Recovery Point Objective) | High (depends on backup frequency)   |

**This means:**
- Data loss is possible
- Recovery takes time

---

#### Cost & Complexity

| Factor     | Level          |
|------------|----------------|
| Cost       | Lowest         |
| Complexity | Low            |
| Automation | Mostly manual  |

---

#### When to Use Backup and Restore

- Non-critical applications
- Dev/test envs
- Cost-sensitive workloads

**Summary:** Backup & Restore is a disaster recovery strategy where data is backed up and restored in another region after a disaster, offering the lowest cost but the highest RTO and RPO

---

### Pilot Light

**What:** A minimal version of your core infrastructure is always running in the DR region

**Concept:**
- Only critical components stay active
- Full system is launched only during disaster

---

#### How It Works

**Primary Region:**
- Full Application is running
- Users actively served

**DR Region:**
- Minimal setup running
- (Databases, minimal EC2 config)
- No traffic

**If disaster occurs:**
- You scale up in DR region:
  - Launch EC2 / Auto Scaling
  - Attach Load Balancer
  - Update Route 53

---

#### What Stays ON in DR Region?

✅ **Yes:**
- Database (replica or restored snapshot)
- Configurations
- IAM roles
- Minimal compute

❌ **No:**
- Full fleet of EC2
- Load balancer traffic
- Auto Scaling at scale

---

**AWS Services Used:**
- RDS Read Replica (cross-region)
- DynamoDB Global Tables (optional)
- S3 Cross-Region Replication
- Route 53 Failover
- Auto Scaling

---

#### Metrics

| Metric | Value                               |
|--------|-------------------------------------|
| RTO    | Medium (minutes to hours)           |
| RPO    | Low (near real-time replication)    |

**Result:** Much better than Backup & Restore

---

#### Cost and Complexity

| Factor     | Level      |
|------------|------------|
| Cost       | 🟡 Medium  |
| Complexity | 🟡 Medium  |
| Automation | ⚠️ Partial |

---

#### When to Use

- Important apps
- Some downtime acceptable
- Faster recovery than backup
- Cost still matters

**Summary:** Pilot Light keeps a minimal version of core infrastructure running in another region, allowing faster recovery with lower RTO and RPO than backup & restore

---

### Warm Standby

**Analogy:** Think of it like a car engine already ON. Not full speed — but ready to move instantly.

**What:** A scaled-down but fully functional version of your application is always running in the DR region

**Concept:**
- Already deployed
- Running
- Only needs scaling up

---

#### How It Works

**Primary Region:**
- Full-scale production
- Handles all user traffic

**Secondary Region:**
- Application running at low capacity
- Minimal EC2 / containers
- Database synced (replica)

**If disaster happens:**
- Route 53 redirects traffic
- Auto Scaling increases capacity
- Users are served almost immediately

---

#### What is Running in DR Region?

✔ **Yes:**
- Load Balancer
- Application servers (small size)
- Auto Scaling group (min = low)
- Database replica
- Networking fully ready

❌ **No:**
- Full production scale

---

**AWS Services:**
- Route 53 Failover Routing
- ALB / NLB
- Auto Scaling
- RDS Read Replicas / Aurora Global DB
- DynamoDB Global Tables
- S3 CRR

---

#### Metrics

| Metric | Value            |
|--------|------------------|
| RTO    | 🟢 Low (minutes) |
| RPO    | 🟢 Very Low      |

---

#### Cost and Complexity

| Factor     | Level     |
|------------|-----------|
| Cost       | 🔴 High   |
| Complexity | 🟡 Medium |
| Automation | ✅ High   |

---

#### When to Use

- ✔ Business-critical apps
- ✔ Minimal downtime allowed
- ✔ Faster recovery needed
- ✔ Willing to pay more

---

### Active–Active

**Concept:** No standby, no waiting, no scaling up. Both regions are LIVE and serving users at the same time.

**What:** The application runs fully active in multiple regions, and traffic is distributed across them

**Benefit:** If one region fails → traffic automatically shifts to the healthy region. Users may not even notice.

---

#### How It Works

**Region A:**
- Full production setup
- Serving traffic

**Region B:**
- Full production setup
- Serving traffic simultaneously

**Route 53:**
Distributes traffic using:
- Latency-based
- Weighted
- Health checks

**If Region A fails:**
- Route 53 sends 100% traffic to B
- 0 or near 0 downtime

---

**Requirements:**
- Stateless application layer
- Global / replicated DB
- Shared data sources

**Result:** RTO, RPO nearly 0

---

#### Cost and Complexity

| Factor     | Level            |
|------------|------------------|
| Cost       | 🔴🔴 Very High   |
| Complexity | 🔴 High          |
| Automation | Fully automated  |

---

#### When Should We Use

- Mission critical
- Global user base
- Zero downtime required
- Financial and healthcare

---

### Comparison Table

| Strategy         | Regions Active | Cost      | RTO       | RPO       |
|------------------|----------------|-----------|-----------|-----------|
| Backup & Restore | 0              | 💰 Lowest | High      | High      |
| Pilot Light      | 0 (infra only) | 💰💰      | Medium    | Low       |
| Warm Standby     | 1 (low scale)  | 💰💰💰    | Low       | Very Low  |
| Active–Active    | 2+ (full)      | 💰💰💰💰  | Near-Zero | Near-Zero |

---

### Fault Tolerance Strategies

**What:** System continues to work even when a component fails

**Characteristics:**
- No manual intervention
- No noticeable impact to users

---

#### Comparison Table

| Concept               | Scope                    |
|-----------------------|--------------------------|
| Fault Tolerance       | Component-level failures |
| High Availability     | AZ-level failures        |
| Disaster Recovery     | Region-level failures    |

---

### Core Fault Tolerance Strategies

#### 1. Redundancy

**Concept:** Never rely on one thing. No single point of failure.

**Examples:**
- Multiple EC2 instances
- Multiple AZs
- Multiple load balancers
- Multiple databases / replicas

**Result:** If one fails → another takes over

---

#### 2. Load Balancing

**Concept:** Spread traffic so no single instance becomes critical

**AWS services:**
- ALB / NLB
- Route 53 health checks

**What it gives:**
- Failed instance → removed automatically
- Traffic → only healthy targets

---

#### 3. Auto Scaling

**Concept:** Replace failed resources automatically

**Example:**
```
EC2 crashes ❌
→ Auto Scaling launches new EC2 ✅
```

**Result:** No human involved

---

#### 4. Stateless Architecture

**Concept:** Don't store user state on a single server

**Instead use:**
- DynamoDB
- RDS
- ElastiCache
- S3

**Benefit:**
- Horizontal scaling
- Any instance can serve any request

---

#### 5. Decoupling Components

**Concept:** Components fail independently, not together

**Tools:**
- SQS
- SNS
- EventBridge

**Examples:**
- Producer fails ❌ → Consumer continues later
- Traffic spikes → Queue absorbs it

---

#### 6. Failover Mechanisms

**Concept:** Automatically switch to a healthy resource

**Examples:**
- Multi-AZ RDS failover
- Route 53 failover routing
- Aurora replica promotion

---

#### 7. Graceful Degradation

**Concept:** Partial failure ≠ total failure

**Examples:**
- Partial availability
- Degraded mode
- Recommendation engine fails, Core checkout still works

---

#### 8. Data Replication

**Concept:** Data must survive failures

**AWS examples:**
- RDS Multi-AZ
- Aurora replicas
- DynamoDB Global Tables
- S3 replication

---

**Summary:** Fault tolerance is achieved using redundancy, self-healing, decoupling, and automated failover so that individual component failures do not impact the system.

---



