# Complete System Design Guide: Zero to Hero 🚀

> **Your Journey**: This guide takes you from knowing nothing about system design to confidently cracking interviews at Google, Meta, Amazon, and other top tech companies.

---

## 📚 Table of Contents

1. [Introduction: What is System Design?](#introduction)
2. [Phase 1: Fundamentals (Week 1-2)](#phase-1-fundamentals)
3. [Phase 2: Intermediate Concepts (Week 3-4)](#phase-2-intermediate)
4. [Phase 3: Advanced Topics (Week 5-6)](#phase-3-advanced)
5. [Phase 4: Interview Preparation (Week 7-8)](#phase-4-interview-prep)
6. [Real-World System Design Examples](#real-world-examples)
7. [Interview Framework & Cheat Sheet](#interview-framework)

---

## Introduction: What is System Design? {#introduction}

### 🤔 The Simple Answer

System design is like being an architect for software. Just as a building architect decides:
- How many floors? (Scalability)
- Where are the exits? (Fault tolerance)
- How many people can fit? (Capacity planning)
- What materials to use? (Technology choices)

You, as a system designer, decide how to build software that serves millions of users reliably.

### Why Companies Ask This

They want to know if you can:
1. **Think at scale** - Can you design for 1 million users, not just 10?
2. **Make trade-offs** - Everything has pros/cons; can you choose wisely?
3. **Communicate clearly** - Can you explain complex ideas simply?

### The Interview Format

**Typical Question**: "Design YouTube" or "Design Uber" or "Design Instagram"

**What They're Really Asking**:
- How do you handle millions of users?
- How do you store massive amounts of data?
- How do you make it fast and reliable?
- How do you handle failures?

---

## Phase 1: Fundamentals (Beginner) {#phase-1-fundamentals}

### 🎯 Goal: Understand the building blocks

---

## 1.1 Understanding System Design Basics

### What is System Design?

**Simple Definition**: Planning how different parts of a software system work together to solve a problem at scale.

**Real-World Analogy**: 
- **Small restaurant** (simple app): 1 chef, 1 waiter, 10 customers
- **McDonald's** (scaled system): Multiple kitchens, drive-thru, delivery, thousands of customers

### Functional vs Non-Functional Requirements

#### Functional Requirements (What the system DOES)
These are the features:

**Example - YouTube:**
- ✅ Users can upload videos
- ✅ Users can watch videos
- ✅ Users can search for videos
- ✅ Users can like/comment

**Example - Uber:**
- ✅ Users can request rides
- ✅ Drivers can accept rides
- ✅ Users can track driver location
- ✅ Users can pay for rides

#### Non-Functional Requirements (How WELL it does it)
These are the quality attributes:

**Performance:**
- Video should start playing in < 2 seconds
- Search results in < 500ms

**Scalability:**
- Support 1 billion users
- Handle 1 million concurrent video uploads

**Availability:**
- System should be up 99.99% of time (only 52 minutes downtime per year!)

**Reliability:**
- No data loss
- Consistent experience

**Security:**
- User data is encrypted
- Authentication required

---

### Monolithic vs Microservices Architecture

#### Monolithic Architecture

**What is it?**: Everything in one big application.

```
┌─────────────────────────────────┐
│     Single Application          │
│  ┌──────────────────────────┐  │
│  │  User Management         │  │
│  │  Video Upload            │  │
│  │  Video Streaming         │  │
│  │  Comments                │  │
│  │  Search                  │  │
│  │  Payments                │  │
│  └──────────────────────────┘  │
│     Single Database             │
└─────────────────────────────────┘
```

**Pros:**
- ✅ Simple to develop initially
- ✅ Easy to test
- ✅ Easy to deploy (one package)

**Cons:**
- ❌ Hard to scale (must scale everything together)
- ❌ One bug can crash entire system
- ❌ Slow deployment (must redeploy everything)
- ❌ Technology lock-in (stuck with one language/framework)

**When to use**: Small teams, simple applications, MVPs

---

#### Microservices Architecture

**What is it?**: Break application into small, independent services.

```
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   User       │  │   Video      │  │   Search     │
│   Service    │  │   Service    │  │   Service    │
│   (Node.js)  │  │   (Go)       │  │   (Python)   │
└──────────────┘  └──────────────┘  └──────────────┘
       │                 │                  │
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   User DB    │  │   Video DB   │  │  Search DB   │
│  (Postgres)  │  │  (MongoDB)   │  │(Elasticsearch)│
└──────────────┘  └──────────────┘  └──────────────┘
```

**Pros:**
- ✅ Scale independently (only scale what needs it)
- ✅ Use different technologies for different services
- ✅ Fault isolation (one service fails, others continue)
- ✅ Faster deployments (deploy one service at a time)
- ✅ Team independence (different teams own different services)

**Cons:**
- ❌ Complex to manage
- ❌ Network overhead (services talk over network)
- ❌ Harder to debug
- ❌ Data consistency challenges

**When to use**: Large teams, complex applications, need to scale

---

### 3-Tier Architecture (The Foundation)

This is the most common architecture pattern. Think of it as a building with 3 floors:

```
┌─────────────────────────────────────────┐
│         Client Tier (Floor 3)           │
│  Web Browser, Mobile App, Desktop App   │
│  (What users see and interact with)     │
└─────────────────────────────────────────┘
                    ↕
┌─────────────────────────────────────────┐
│       Application Tier (Floor 2)        │
│     Backend Servers, Business Logic     │
│  (Processes requests, applies rules)    │
└─────────────────────────────────────────┘
                    ↕
┌─────────────────────────────────────────┐
│         Database Tier (Floor 1)         │
│      Stores all data permanently        │
│         (PostgreSQL, MongoDB)           │
└─────────────────────────────────────────┘
```

**Example Flow - User Posts a Tweet:**

1. **Client**: User types tweet, clicks "Post" → sends HTTP request
2. **Application**: Server validates tweet (< 280 chars), adds timestamp, user ID
3. **Database**: Stores tweet permanently
4. **Application**: Returns success response
5. **Client**: Shows "Tweet posted!" message

---

### Key Performance Metrics

#### Latency
**What**: Time taken to process a request (response time)

**Example:**
- You click "Search" → 200ms later results appear
- Latency = 200ms

**Good Latency:**
- Web page load: < 1 second
- API response: < 100ms
- Database query: < 10ms

**Bad Latency:**
- Web page load: > 3 seconds (users leave!)
- API response: > 1 second

---

#### Throughput
**What**: Number of requests processed per second

**Example:**
- Your server can handle 10,000 requests/second
- Throughput = 10,000 RPS (requests per second)

**Analogy**: 
- Latency = How fast one car goes through a toll booth
- Throughput = How many cars go through per hour

---

#### SLA, SLO, SLI (Service Level Agreements)

**SLA (Service Level Agreement)**: Contract with users
- "We promise 99.9% uptime"
- "If we fail, we'll refund you"

**SLO (Service Level Objective)**: Internal goal
- "Our goal is 99.95% uptime"
- (Higher than SLA to have buffer)

**SLI (Service Level Indicator)**: Actual measurement
- "Last month we achieved 99.97% uptime"

**Example:**
```
SLA: "YouTube will be available 99.9% of time"
     = Max 8.76 hours downtime per year

SLO: Internal goal = 99.95% uptime
     = Max 4.38 hours downtime per year

SLI: Actual measurement = 99.98% uptime last month
     = Only 8 minutes downtime
```

**Uptime Percentages:**
- 99% = 3.65 days downtime/year ❌ (Bad)
- 99.9% = 8.76 hours downtime/year ⚠️ (Okay)
- 99.99% = 52 minutes downtime/year ✅ (Good)
- 99.999% = 5 minutes downtime/year 🌟 (Excellent)

---

### Vertical vs Horizontal Scaling

#### Vertical Scaling (Scale Up) 🏢

**What**: Make your server bigger/more powerful

**Analogy**: Buy a bigger truck to carry more goods

```
Before:                    After:
┌──────────┐              ┌──────────┐
│ 4 GB RAM │    →         │ 32 GB RAM│
│ 2 Cores  │              │ 16 Cores │
│ 100 GB   │              │ 1 TB SSD │
└──────────┘              └──────────┘
```

**Pros:**
- ✅ Simple (just upgrade hardware)
- ✅ No code changes needed
- ✅ No network complexity

**Cons:**
- ❌ Expensive (high-end servers cost $$$)
- ❌ Single point of failure (if server dies, everything stops)
- ❌ Limited (can't upgrade forever)
- ❌ Downtime during upgrade

**When to use**: 
- Small to medium applications
- Databases (often need powerful single machines)

---

#### Horizontal Scaling (Scale Out) 🏘️

**What**: Add more servers

**Analogy**: Buy more trucks instead of one giant truck

```
Before:                    After:
┌──────────┐              ┌──────────┐ ┌──────────┐ ┌──────────┐
│ Server 1 │    →         │ Server 1 │ │ Server 2 │ │ Server 3 │
└──────────┘              └──────────┘ └──────────┘ └──────────┘
                                   ↑          ↑          ↑
                          ┌────────┴──────────┴──────────┴────┐
                          │      Load Balancer                │
                          └───────────────────────────────────┘
```

**Pros:**
- ✅ No limit to scaling (add infinite servers)
- ✅ Fault tolerant (one server dies, others continue)
- ✅ Cost effective (use commodity hardware)
- ✅ No downtime (add servers without stopping)

**Cons:**
- ❌ Complex (need load balancers, data sync)
- ❌ Code changes needed (must be stateless)
- ❌ Data consistency challenges

**When to use**: 
- Large applications (Facebook, Google)
- Need high availability
- Unpredictable traffic

---

## 1.2 Scalability & Performance

### Load Balancing

**What**: Distribute incoming requests across multiple servers

**Analogy**: Airport security - multiple lines instead of one long line

```
                    ┌──────────────────┐
         Requests → │  Load Balancer   │
                    └──────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        ↓                   ↓                   ↓
   ┌─────────┐         ┌─────────┐         ┌─────────┐
   │Server 1 │         │Server 2 │         │Server 3 │
   │ 30% CPU │         │ 25% CPU │         │ 35% CPU │
   └─────────┘         └─────────┘         └─────────┘
```

#### Load Balancing Algorithms

**1. Round Robin** (Most Common)
```
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1  (cycle repeats)
Request 5 → Server 2
```

**Pros**: Simple, fair distribution
**Cons**: Doesn't consider server load

---

**2. Least Connections**
```
Server 1: 10 active connections
Server 2: 5 active connections   ← Send here!
Server 3: 8 active connections

Next request goes to Server 2 (least busy)
```

**Pros**: Better for long-lived connections
**Cons**: Slightly more complex

---

**3. IP Hash**
```
User IP: 192.168.1.100
Hash(192.168.1.100) % 3 = 1
→ Always send to Server 1

Same user always goes to same server
```

**Pros**: Session persistence (user data stays on same server)
**Cons**: Uneven distribution if some IPs are more active

---

**4. Weighted Round Robin**
```
Server 1: Weight 5 (powerful)  → Gets 50% traffic
Server 2: Weight 3 (medium)    → Gets 30% traffic
Server 3: Weight 2 (weak)      → Gets 20% traffic
```

**Pros**: Handles servers with different capacities
**Cons**: Need to configure weights

---

### Caching (The Speed Booster)

**What**: Store frequently accessed data in fast memory

**Analogy**: Keep your favorite book on your desk instead of going to library every time

**Why Cache?**
- Database query: 100ms ❌
- Cache lookup: 1ms ✅
- **100x faster!**

#### Cache Architecture

```
┌─────────┐
│  User   │
└────┬────┘
     │ 1. Request data
     ↓
┌─────────────┐
│   Server    │
└─────┬───────┘
      │ 2. Check cache first
      ↓
┌─────────────┐
│   Cache     │  ← Redis/Memcached (In-Memory)
│  (Redis)    │
└─────┬───────┘
      │ 3. If not in cache (Cache Miss)
      ↓
┌─────────────┐
│  Database   │  ← PostgreSQL/MySQL (On-Disk)
└─────────────┘
```

#### Cache Hit vs Cache Miss

**Cache Hit** (Fast ✅):
```
1. User requests "Get user profile ID=123"
2. Server checks Redis
3. Data found in Redis! Return immediately (1ms)
```

**Cache Miss** (Slow ❌):
```
1. User requests "Get user profile ID=999"
2. Server checks Redis
3. Data NOT in Redis
4. Server queries Database (100ms)
5. Server stores result in Redis for next time
6. Return data to user
```

#### Caching Strategies

**1. Cache-Aside (Lazy Loading)**
```
Read:
  if data in cache:
    return from cache
  else:
    fetch from database
    store in cache
    return data

Write:
  write to database
  invalidate cache (delete old data)
```

**Pros**: Only cache what's needed
**Cons**: First request is slow (cache miss)

---

**2. Write-Through**
```
Write:
  write to cache
  write to database (synchronously)
  
Read:
  always read from cache
```

**Pros**: Cache always up-to-date
**Cons**: Slower writes (must write twice)

---

**3. Write-Back (Write-Behind)**
```
Write:
  write to cache immediately
  write to database asynchronously (later)
  
Read:
  read from cache
```

**Pros**: Super fast writes
**Cons**: Risk of data loss if cache crashes

---

#### What to Cache?

**Good Candidates:**
- ✅ User profiles (change rarely)
- ✅ Product catalogs
- ✅ Configuration settings
- ✅ Session data
- ✅ API responses

**Bad Candidates:**
- ❌ Real-time data (stock prices)
- ❌ Personalized data (different for each user)
- ❌ Large files (videos)
- ❌ Data that changes frequently

#### Cache Expiration (TTL - Time To Live)

```
Set cache with TTL:
  cache.set("user:123", userData, ttl=3600)  # 1 hour
  
After 1 hour:
  cache automatically deletes the data
  Next request will be cache miss
```

**Common TTL Values:**
- Session data: 30 minutes
- User profiles: 1 hour
- Product listings: 5 minutes
- Configuration: 24 hours

---

### Database Performance

#### Indexing (The Secret Weapon)

**What**: A data structure that makes searches fast

**Analogy**: Book index vs reading every page

**Without Index:**
```sql
SELECT * FROM users WHERE email = 'john@example.com';

Database must scan ALL rows:
Row 1: alice@example.com  ❌
Row 2: bob@example.com    ❌
Row 3: charlie@example.com ❌
...
Row 1,000,000: john@example.com ✅ Found!

Time: 10 seconds ❌
```

**With Index:**
```sql
CREATE INDEX idx_email ON users(email);

Database uses index (like a phone book):
Jump directly to 'john@example.com'

Time: 10 milliseconds ✅
```

#### Types of Indexes

**1. B-Tree Index** (Most Common)
- Good for: Range queries, sorting
- Example: `WHERE age > 25 AND age < 40`

**2. Hash Index**
- Good for: Exact matches
- Example: `WHERE id = 123`
- Bad for: Range queries

**3. Full-Text Index**
- Good for: Text search
- Example: `WHERE description CONTAINS 'machine learning'`

#### When to Use Indexes

**Create Index When:**
- ✅ Column used in WHERE clause frequently
- ✅ Column used in JOIN conditions
- ✅ Column used in ORDER BY
- ✅ Table has many rows (> 10,000)

**Don't Create Index When:**
- ❌ Table is small (< 1,000 rows)
- ❌ Column changes frequently (inserts/updates slow down)
- ❌ Column has few unique values (e.g., gender: M/F)

---

#### Query Optimization

**Bad Query:**
```sql
-- Fetches ALL columns (wasteful)
SELECT * FROM users 
WHERE city = 'New York' 
  AND age > 25;

-- No index on city or age
-- Scans entire table
```

**Good Query:**
```sql
-- Only fetch needed columns
SELECT id, name, email FROM users 
WHERE city = 'New York' 
  AND age > 25;

-- Create indexes
CREATE INDEX idx_city_age ON users(city, age);
```

**Performance Improvement**: 100x faster!

---

### Database Scaling

#### Replication (Master-Slave)

**What**: Copy data to multiple databases

**Architecture:**
```
                  ┌──────────────┐
                  │   Master DB  │ ← All WRITES go here
                  │  (Primary)   │
                  └───────┬──────┘
                          │ Replicate
          ┌───────────────┼───────────────┐
          ↓               ↓               ↓
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ Slave 1  │    │ Slave 2  │    │ Slave 3  │
    │(Replica) │    │(Replica) │    │(Replica) │
    └──────────┘    └──────────┘    └──────────┘
         ↑               ↑               ↑
         └───────────────┴───────────────┘
              All READS distributed here
```

**How It Works:**
1. Application writes to Master
2. Master replicates changes to Slaves (asynchronously)
3. Application reads from Slaves (load balanced)

**Benefits:**
- ✅ Read scalability (add more slaves)
- ✅ High availability (if master fails, promote slave)
- ✅ Backup (slaves are live backups)

**Challenges:**
- ⚠️ Replication lag (slaves might be slightly behind)
- ⚠️ Consistency issues (read might return old data)

---

#### Sharding (Horizontal Partitioning)

**What**: Split data across multiple databases

**Analogy**: Instead of one huge library, have multiple smaller libraries by topic

**Example - User Database Sharding:**

```
Shard by User ID:

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   Shard 1       │  │   Shard 2       │  │   Shard 3       │
│  Users 1-1M     │  │  Users 1M-2M    │  │  Users 2M-3M    │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ ID: 1           │  │ ID: 1,000,001   │  │ ID: 2,000,001   │
│ ID: 2           │  │ ID: 1,000,002   │  │ ID: 2,000,002   │
│ ...             │  │ ...             │  │ ...             │
│ ID: 1,000,000   │  │ ID: 2,000,000   │  │ ID: 3,000,000   │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

**Sharding Strategies:**

**1. Range-Based Sharding**
```
User ID 1-1M      → Shard 1
User ID 1M-2M     → Shard 2
User ID 2M-3M     → Shard 3
```

**Pros**: Simple, easy to add new ranges
**Cons**: Uneven distribution (some ranges more active)

---

**2. Hash-Based Sharding**
```
Shard = Hash(User ID) % Number of Shards

User ID 12345 → Hash = 2 → Shard 2
User ID 67890 → Hash = 1 → Shard 1
```

**Pros**: Even distribution
**Cons**: Hard to add new shards (must rehash everything)

---

**3. Geographic Sharding**
```
US Users      → US Shard (Virginia)
EU Users      → EU Shard (Ireland)
Asia Users    → Asia Shard (Singapore)
```

**Pros**: Low latency (data close to users)
**Cons**: Uneven distribution, legal compliance needed

---

**Sharding Challenges:**

**1. Cross-Shard Queries**
```
Bad: "Get all users with age > 25"
     Must query ALL shards, then merge results ❌

Good: "Get user with ID = 12345"
      Query only one shard ✅
```

**2. Joins Across Shards**
```
Users in Shard 1
Orders in Shard 2

JOIN users and orders = VERY SLOW ❌

Solution: Denormalize (store user info with orders)
```

**3. Rebalancing**
```
Adding new shard requires:
- Redistribute data
- Update routing logic
- Minimize downtime
```

---

### CAP Theorem (The Fundamental Trade-off)

**What**: You can only have 2 out of 3 guarantees in a distributed system

```
        Consistency
            /\
           /  \
          /    \
         /  CA  \
        /        \
       /    CP    \
      /            \
     /______________\
    /      AP        \
   /                  \
Availability -------- Partition Tolerance
```

#### The Three Guarantees

**C - Consistency**: All nodes see the same data at the same time
```
Write to Node 1: user.balance = $100
Read from Node 2: user.balance = $100 ✅ (consistent)
```

**A - Availability**: Every request gets a response (success or failure)
```
Even if some nodes are down, system still responds
```

**P - Partition Tolerance**: System works even if network fails between nodes
```
Node 1 and Node 2 can't communicate
But system still functions
```

#### Why Can't We Have All Three?

**Scenario**: Network partition happens (nodes can't talk)

**Option 1: Choose CP (Consistency + Partition Tolerance)**
```
Network partition occurs
System blocks writes until partition heals
Ensures consistency but sacrifices availability ❌
```

**Option 2: Choose AP (Availability + Partition Tolerance)**
```
Network partition occurs
Both nodes accept writes independently
System available but data inconsistent ❌
```

**Option 3: Choose CA (Consistency + Availability)**
```
Only works if no network partition
In distributed systems, partitions WILL happen
So CA is not realistic ❌
```

#### Real-World Examples

**CP Systems** (Consistency over Availability):
- **Banking systems**: Can't have inconsistent balances
- **Inventory management**: Can't oversell products
- **Examples**: HBase, MongoDB (with strong consistency), Redis (with replication)

**AP Systems** (Availability over Consistency):
- **Social media feeds**: Okay if you see slightly old posts
- **Shopping cart**: Okay if cart syncs in few seconds
- **Examples**: Cassandra, DynamoDB, Riak

---

### Consistency Models

#### Strong Consistency
```
Write: user.balance = $100
Read immediately: user.balance = $100 ✅

Guarantee: Reads always return latest write
```

**Pros**: Simple to reason about
**Cons**: Slower, less available

**Use Cases**: Banking, inventory

---

#### Eventual Consistency
```
Write to Node 1: user.balance = $100
Read from Node 2 immediately: user.balance = $90 (old value)
Wait 100ms...
Read from Node 2 again: user.balance = $100 ✅ (synced)

Guarantee: All nodes will eventually have same data
```

**Pros**: Fast, highly available
**Cons**: Temporary inconsistencies

**Use Cases**: Social media, analytics, caching

---

#### Read-After-Write Consistency
```
User writes: "Post new tweet"
User reads immediately: Sees their own tweet ✅
Other users might see it few seconds later

Guarantee: User sees their own writes immediately
```

**Use Cases**: Social media, comments

---

## 1.3 Networking Basics

### HTTP Versions

#### HTTP/1.1 (1997-2015)

**How It Works:**
```
Browser wants 3 files:
1. index.html
2. style.css
3. script.js

HTTP/1.1:
Request 1 → index.html → Response 1
Request 2 → style.css  → Response 2
Request 3 → script.js  → Response 3

Each request waits for previous to complete ❌
```

**Problems:**
- Head-of-line blocking (requests wait in line)
- Multiple TCP connections needed (slow)
- Large headers (repeated for each request)

---

#### HTTP/2 (2015)

**Improvements:**
```
Single TCP connection
Multiplexing (send all requests at once):

Request 1 (index.html)  ──┐
Request 2 (style.css)   ──┼→ Server
Request 3 (script.js)   ──┘

Response 1 ──┐
Response 2 ──┼→ Browser (all at once!)
Response 3 ──┘
```

**Benefits:**
- ✅ Multiplexing (parallel requests)
- ✅ Header compression (smaller overhead)
- ✅ Server push (server sends files before requested)
- ✅ Binary protocol (faster parsing)

**Performance**: 2-3x faster than HTTP/1.1

---

#### HTTP/3 (2020+)

**Key Change**: Uses QUIC protocol (UDP-based) instead of TCP

**Why?**
```
TCP Problem:
Packet 1 lost → All packets wait ❌

QUIC Solution:
Packet 1 lost → Only Packet 1 waits, others continue ✅
```

**Benefits:**
- ✅ Faster connection setup (0-RTT)
- ✅ Better mobile performance (handles network switches)
- ✅ No head-of-line blocking

---

### WebSockets (Real-Time Communication)

**HTTP**: Request-Response (one-way, client initiates)
```
Client: "Give me data" → Server: "Here's data"
Client: "Give me data" → Server: "Here's data"
(Must keep asking)
```

**WebSocket**: Persistent connection (two-way)
```
Client: "Connect" → Server: "Connected"
Server: "Here's data" (anytime!)
Client: "Here's data" (anytime!)
(Always connected)
```

**Use Cases:**
- ✅ Chat applications (WhatsApp, Slack)
- ✅ Live sports scores
- ✅ Stock trading platforms
- ✅ Multiplayer games
- ✅ Collaborative editing (Google Docs)

**Example Flow:**
```
1. Client: HTTP Upgrade request
2. Server: Agree to upgrade
3. Connection becomes WebSocket
4. Both can send messages anytime
```

---

### API Styles

#### REST (Representational State Transfer)

**Concept**: Resources accessed via HTTP methods

**Example - Blog API:**
```
GET    /posts          → Get all posts
GET    /posts/123      → Get post 123
POST   /posts          → Create new post
PUT    /posts/123      → Update post 123
DELETE /posts/123      → Delete post 123
```

**Pros:**
- ✅ Simple, widely understood
- ✅ Cacheable (HTTP caching works)
- ✅ Stateless (each request independent)

**Cons:**
- ❌ Over-fetching (get data you don't need)
- ❌ Under-fetching (need multiple requests)
- ❌ Multiple round trips

---

#### GraphQL

**Concept**: Client specifies exactly what data it needs

**Example:**
```graphql
# Client request
query {
  user(id: 123) {
    name
    email
    posts {
      title
      likes
    }
  }
}

# Server response (exactly what was asked)
{
  "user": {
    "name": "John",
    "email": "john@example.com",
    "posts": [
      {"title": "Post 1", "likes": 50},
      {"title": "Post 2", "likes": 30}
    ]
  }
}
```

**Pros:**
- ✅ No over-fetching (get only what you need)
- ✅ No under-fetching (get everything in one request)
- ✅ Strongly typed schema

**Cons:**
- ❌ Complex to implement
- ❌ Caching harder
- ❌ Can be slow if query too complex

---

#### gRPC (Google Remote Procedure Call)

**Concept**: Call remote functions like local functions

**Example:**
```protobuf
// Define service
service UserService {
  rpc GetUser(UserRequest) returns (UserResponse);
  rpc CreateUser(CreateUserRequest) returns (UserResponse);
}

// Client code (looks like local function)
user = userService.GetUser(userId: 123)
```

**Pros:**
- ✅ Very fast (binary protocol)
- ✅ Strongly typed
- ✅ Bidirectional streaming
- ✅ Multiple language support

**Cons:**
- ❌ Not human-readable (binary)
- ❌ Browser support limited
- ❌ Steeper learning curve

**When to Use:**
- Microservices communication (internal)
- High-performance requirements
- Real-time bidirectional streaming

---

### API Gateway

**What**: Single entry point for all API requests

**Without API Gateway:**
```
Mobile App ──→ Auth Service
          ──→ User Service
          ──→ Order Service
          ──→ Payment Service
(Client must know all services)
```

**With API Gateway:**
```
Mobile App ──→ API Gateway ──→ Auth Service
                          ──→ User Service
                          ──→ Order Service
                          ──→ Payment Service
(Client only knows gateway)
```

**Responsibilities:**

**1. Routing**
```
/api/users/*    → User Service
/api/orders/*   → Order Service
/api/payments/* → Payment Service
```

**2. Authentication**
```
Check JWT token before forwarding request
```

**3. Rate Limiting**
```
Max 100 requests/minute per user
```

**4. Request/Response Transformation**
```
Client sends: {"user_id": 123}
Gateway transforms: {"userId": 123}
Service receives: {"userId": 123}
```

**5. Caching**
```
Cache frequent responses
```

**6. Load Balancing**
```
Distribute requests across service instances
```

**Popular API Gateways:**
- AWS API Gateway
- Kong
- Nginx
- Envoy

---

### Rate Limiting (Prevent Abuse)

**Why?**
- Prevent DDoS attacks
- Ensure fair usage
- Protect backend from overload

#### Token Bucket Algorithm

**Concept**: Bucket holds tokens, each request consumes one token

```
Bucket capacity: 100 tokens
Refill rate: 10 tokens/second

Time 0s:  100 tokens available
Request 1: 99 tokens left
Request 2: 98 tokens left
...
Request 100: 0 tokens left
Request 101: REJECTED (no tokens) ❌

Time 1s: Refill 10 tokens → 10 tokens available
```

**Pros:**
- ✅ Allows bursts (use all tokens at once)
- ✅ Simple to implement

**Cons:**
- ❌ Can be abused (burst then wait)

---

#### Leaky Bucket Algorithm

**Concept**: Requests enter bucket, leak out at constant rate

```
Bucket capacity: 100 requests
Leak rate: 10 requests/second

Requests come in at any rate
But processed at constant 10/second

If bucket full, reject new requests
```

**Pros:**
- ✅ Smooth traffic (constant output rate)
- ✅ Prevents bursts

**Cons:**
- ❌ Doesn't allow bursts (even if system can handle)

---

#### Fixed Window Counter

**Concept**: Count requests in fixed time windows

```
Window: 1 minute
Limit: 100 requests

00:00 - 00:59: Count requests
If count > 100: Reject

01:00: Reset counter to 0
```

**Pros:**
- ✅ Very simple

**Cons:**
- ❌ Burst at window boundaries
  ```
  00:59: 100 requests ✅
  01:00: 100 requests ✅
  Total: 200 requests in 1 second! ❌
  ```

---

#### Sliding Window Log

**Concept**: Keep timestamp of each request

```
Limit: 100 requests/minute

Current time: 12:00:30
Look back 1 minute (11:59:30 - 12:00:30)
Count requests in this window
If > 100: Reject
```

**Pros:**
- ✅ No burst issues
- ✅ Accurate

**Cons:**
- ❌ Memory intensive (store all timestamps)

---

### Reverse Proxy

**What**: Server that sits in front of backend servers

**Forward Proxy** (Client-side):
```
User → Proxy → Internet
(Hides user's identity)
```

**Reverse Proxy** (Server-side):
```
User → Reverse Proxy → Backend Servers
(Hides server's identity)
```

**Benefits:**

**1. Load Balancing**
```
Distribute traffic across servers
```

**2. SSL Termination**
```
Handle HTTPS encryption/decryption
Backend servers don't need to
```

**3. Caching**
```
Cache static content
Reduce backend load
```

**4. Security**
```
Hide backend server IPs
Filter malicious requests
```

**5. Compression**
```
Compress responses (gzip)
Reduce bandwidth
```

**Popular Reverse Proxies:**
- Nginx (most popular)
- HAProxy
- Envoy
- Traefik

---

## 1.4 Database Selection

### SQL vs NoSQL

#### SQL Databases (Relational)

**Structure**: Tables with rows and columns

**Example:**
```sql
Users Table:
+----+-------+-------------------+-----+
| id | name  | email             | age |
+----+-------+-------------------+-----+
| 1  | Alice | alice@example.com | 25  |
| 2  | Bob   | bob@example.com   | 30  |
+----+-------+-------------------+-----+

Orders Table:
+----+---------+------------+--------+
| id | user_id | product    | amount |
+----+---------+------------+--------+
| 1  | 1       | Laptop     | 1000   |
| 2  | 1       | Mouse      | 20     |
| 3  | 2       | Keyboard   | 50     |
+----+---------+------------+--------+
```

**Characteristics:**
- ✅ ACID transactions (Atomicity, Consistency, Isolation, Durability)
- ✅ Strong consistency
- ✅ Complex queries (JOINs)
- ✅ Schema enforced (data structure defined)

**When to Use:**
- Financial systems (banking, payments)
- E-commerce (orders, inventory)
- Traditional business applications
- Complex relationships between data

**Popular SQL Databases:**
- PostgreSQL (most feature-rich)
- MySQL (most popular)
- Oracle (enterprise)
- SQL Server (Microsoft)

---

#### NoSQL Databases (Non-Relational)

**Types:**

**1. Document Databases** (MongoDB, CouchDB)

**Structure**: JSON-like documents

```json
{
  "_id": "123",
  "name": "Alice",
  "email": "alice@example.com",
  "age": 25,
  "orders": [
    {"product": "Laptop", "amount": 1000},
    {"product": "Mouse", "amount": 20}
  ]
}
```

**When to Use:**
- Flexible schema (structure changes often)
- Hierarchical data
- Content management systems
- User profiles

---

**2. Key-Value Stores** (Redis, DynamoDB)

**Structure**: Simple key-value pairs

```
Key: "user:123"
Value: {"name": "Alice", "email": "alice@example.com"}

Key: "session:abc"
Value: {"userId": 123, "loginTime": "2024-01-01"}
```

**When to Use:**
- Caching
- Session storage
- Shopping carts
- Simple lookups

---

**3. Column-Family Stores** (Cassandra, HBase)

**Structure**: Columns grouped into families

```
Row Key: user:123
Column Family: profile
  - name: "Alice"
  - email: "alice@example.com"
Column Family: activity
  - lastLogin: "2024-01-01"
  - loginCount: 50
```

**When to Use:**
- Time-series data
- Analytics
- Large-scale data (petabytes)
- Write-heavy workloads

---

**4. Graph Databases** (Neo4j, Amazon Neptune)

**Structure**: Nodes and relationships

```
(Alice) -[FRIENDS_WITH]-> (Bob)
(Alice) -[LIKES]-> (Post1)
(Bob) -[COMMENTED_ON]-> (Post1)
```

**When to Use:**
- Social networks
- Recommendation engines
- Fraud detection
- Knowledge graphs

---

### SQL vs NoSQL Comparison

| Feature | SQL | NoSQL |
|---------|-----|-------|
| **Schema** | Fixed (must define upfront) | Flexible (can change anytime) |
| **Scaling** | Vertical (harder) | Horizontal (easier) |
| **Transactions** | ACID (strong) | BASE (eventual consistency) |
| **Queries** | Complex JOINs supported | Limited JOIN support |
| **Consistency** | Strong | Eventual (usually) |
| **Speed** | Slower for simple queries | Faster for simple queries |
| **Use Case** | Complex relationships | Simple, high-scale |

---

### ACID vs BASE

#### ACID (SQL Databases)

**A - Atomicity**: All or nothing
```
Transfer $100 from Alice to Bob:
1. Deduct $100 from Alice
2. Add $100 to Bob

If step 2 fails, step 1 is rolled back
Both succeed or both fail ✅
```

**C - Consistency**: Database always in valid state
```
Before: Alice $500, Bob $300, Total $800
After:  Alice $400, Bob $400, Total $800 ✅
Total always $800 (consistent)
```

**I - Isolation**: Transactions don't interfere
```
Transaction 1: Transfer $100 Alice → Bob
Transaction 2: Transfer $50 Alice → Charlie

Both run independently
No interference ✅
```

**D - Durability**: Once committed, data persists
```
Transaction committed → Power outage
Data still there after restart ✅
```

---

#### BASE (NoSQL Databases)

**BA - Basically Available**: System always responds
```
Even if some nodes down, system works
Might return stale data, but responds ✅
```

**S - Soft State**: State may change without input
```
Data might change due to eventual consistency
Not always in consistent state ⚠️
```

**E - Eventual Consistency**: Eventually all nodes sync
```
Write to Node 1 → Read from Node 2 (old data)
Wait 100ms → Read from Node 2 (new data) ✅
```

---

### OLTP vs OLAP

#### OLTP (Online Transaction Processing)

**Purpose**: Handle day-to-day transactions

**Characteristics:**
- Many small, fast queries
- INSERT, UPDATE, DELETE heavy
- Current data
- Normalized schema

**Examples:**
```
- Place an order
- Update user profile
- Transfer money
- Book a ticket
```

**Databases**: PostgreSQL, MySQL, Oracle

---

#### OLAP (Online Analytical Processing)

**Purpose**: Analyze data for insights

**Characteristics:**
- Few large, complex queries
- SELECT heavy (read-only)
- Historical data
- Denormalized schema

**Examples:**
```
- Total sales last quarter
- Customer segmentation
- Trend analysis
- Revenue forecasting
```

**Databases**: Snowflake, BigQuery, Redshift

---

### Query Optimization

#### Use Indexes
```sql
-- Slow (full table scan)
SELECT * FROM users WHERE email = 'alice@example.com';

-- Fast (index lookup)
CREATE INDEX idx_email ON users(email);
SELECT * FROM users WHERE email = 'alice@example.com';
```

#### Select Only Needed Columns
```sql
-- Bad (fetches all columns)
SELECT * FROM users WHERE city = 'NYC';

-- Good (fetches only needed)
SELECT id, name, email FROM users WHERE city = 'NYC';
```

#### Use LIMIT
```sql
-- Bad (fetches all rows)
SELECT * FROM users ORDER BY created_at DESC;

-- Good (fetches only 10)
SELECT * FROM users ORDER BY created_at DESC LIMIT 10;
```

#### Avoid N+1 Queries
```sql
-- Bad (N+1 queries)
users = SELECT * FROM users;
for each user:
  orders = SELECT * FROM orders WHERE user_id = user.id;

-- Good (2 queries with JOIN)
SELECT users.*, orders.*
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```

---

## Phase 2: Intermediate Concepts {#phase-2-intermediate}

### 🎯 Goal: Build distributed systems knowledge

---

## 2.1 Distributed Systems

### Event-Driven Architecture

**What**: Components communicate through events (messages)

**Traditional Architecture:**
```
User places order → Order Service:
  1. Update inventory
  2. Charge payment
  3. Send email
  4. Update analytics
(All synchronous, slow ❌)
```

**Event-Driven Architecture:**
```
User places order → Order Service:
  1. Create order
  2. Publish "OrderCreated" event
  3. Return success immediately ✅

Event Queue (Kafka):
  - InventoryService listens → Updates stock
  - PaymentService listens → Charges card
  - EmailService listens → Sends confirmation
  - AnalyticsService listens → Updates dashboard
(All asynchronous, fast ✅)
```

**Benefits:**
- ✅ Loose coupling (services independent)
- ✅ Scalability (add more listeners)
- ✅ Resilience (if one service down, others continue)
- ✅ Flexibility (easy to add new features)

---

### Message Queues (Kafka, RabbitMQ)

#### Apache Kafka

**Architecture:**
```
Producers → Kafka Cluster → Consumers
            (Topics)
```

**Concepts:**

**Topic**: Category of messages
```
Topic: "orders"
  - Message 1: {"orderId": 1, "amount": 100}
  - Message 2: {"orderId": 2, "amount": 200}
```

**Partition**: Topic split for parallelism
```
Topic: "orders" (3 partitions)
  Partition 0: Messages 1, 4, 7...
  Partition 1: Messages 2, 5, 8...
  Partition 2: Messages 3, 6, 9...
```

**Consumer Group**: Multiple consumers share work
```
Consumer Group: "order-processors"
  Consumer 1 → Reads Partition 0
  Consumer 2 → Reads Partition 1
  Consumer 3 → Reads Partition 2
(Parallel processing ✅)
```

**Key Features:**
- ✅ High throughput (millions of messages/second)
- ✅ Persistent (messages stored on disk)
- ✅ Replay (can re-read old messages)
- ✅ Scalable (add more partitions)

**Use Cases:**
- Activity tracking
- Log aggregation
- Stream processing
- Event sourcing

---

#### RabbitMQ

**Architecture:**
```
Producer → Exchange → Queue → Consumer
```

**Exchange Types:**

**1. Direct Exchange**
```
Message with routing key "error" → Error Queue
Message with routing key "info" → Info Queue
```

**2. Fanout Exchange**
```
Message → All queues (broadcast)
```

**3. Topic Exchange**
```
Message "user.created" → Matches "user.*"
Message "order.shipped" → Matches "order.*"
```

**Key Features:**
- ✅ Flexible routing
- ✅ Message acknowledgment
- ✅ Dead letter queues (failed messages)
- ✅ Priority queues

**Use Cases:**
- Task queues (background jobs)
- Work distribution
- RPC (request-response)

---

### Distributed Caching

**Problem**: Single cache server is bottleneck

**Solution**: Multiple cache servers

#### Consistent Hashing

**Traditional Hashing:**
```
Cache server = Hash(key) % 3

Key "user:123" → Hash = 5 → 5 % 3 = 2 → Server 2
Key "user:456" → Hash = 7 → 7 % 3 = 1 → Server 1

Add Server 4:
Key "user:123" → Hash = 5 → 5 % 4 = 1 → Server 1 ❌
(Most keys move to different servers - cache miss!)
```

**Consistent Hashing:**
```
Servers placed on ring (0-360°):
Server 1 at 0°
Server 2 at 120°
Server 3 at 240°

Key "user:123" → Hash = 50° → Next server clockwise = Server 2
Key "user:456" → Hash = 200° → Next server clockwise = Server 3

Add Server 4 at 60°:
Key "user:123" → Hash = 50° → Next server = Server 4
Key "user:456" → Hash = 200° → Still Server 3 ✅
(Only keys between Server 1 and Server 4 move)
```

**Benefits:**
- ✅ Minimal key redistribution when adding/removing servers
- ✅ Load balancing
- ✅ Fault tolerance

---

### Idempotency (Critical Concept)

**What**: Operation can be repeated without changing result

**Example:**

**Non-Idempotent:**
```
Request: "Add $10 to balance"
Balance: $100

Request 1: Balance = $110 ✅
Request 2 (duplicate): Balance = $120 ❌ (Wrong!)
```

**Idempotent:**
```
Request: "Set balance to $110" (with request ID)
Balance: $100

Request 1 (ID: abc): Balance = $110 ✅
Request 2 (ID: abc, duplicate): Balance = $110 ✅ (Correct!)
```

**How to Implement:**

**1. Request IDs**
```
Client generates unique ID for each request
Server checks if ID already processed
If yes, return cached result
```

**2. Database Constraints**
```
CREATE UNIQUE INDEX ON transactions(request_id);

If duplicate request, database rejects (unique constraint)
```

**Why Important:**
- Network failures cause retries
- Duplicate requests happen
- Must handle gracefully

---

### Consensus Protocols

**Problem**: Multiple servers must agree on a value

**Example**: Leader election
```
Server 1: "I'm the leader!"
Server 2: "No, I'm the leader!"
Server 3: "I'm confused..."

Need consensus protocol to agree ✅
```

#### Raft (Simpler than Paxos)

**Roles:**
- Leader: Handles all requests
- Follower: Replicates leader's data
- Candidate: Wants to become leader

**Leader Election:**
```
1. All start as followers
2. If no heartbeat from leader, become candidate
3. Candidate requests votes from others
4. If majority votes yes, become leader
5. Leader sends heartbeats to maintain leadership
```

**Log Replication:**
```
1. Client sends request to leader
2. Leader appends to its log
3. Leader sends to followers
4. Followers append to their logs
5. Once majority appends, leader commits
6. Leader notifies followers to commit
```

**Use Cases:**
- etcd (Kubernetes)
- Consul (service discovery)
- Distributed databases

---

### Leader Election

**Why Needed:**
- Coordinate distributed tasks
- Avoid conflicts
- Single source of truth

**Using Zookeeper:**
```
1. All servers try to create "/leader" node
2. Only one succeeds (becomes leader)
3. Others watch the node
4. If leader dies, node deleted
5. Others compete again
```

**Using etcd:**
```
1. All servers try to acquire lease on key
2. Only one succeeds (becomes leader)
3. Leader must renew lease periodically
4. If leader dies, lease expires
5. Others compete again
```

---

## 2.2 Storage and Databases

### Data Warehousing

**What**: Central repository for analytical data

**Architecture:**
```
OLTP Databases → ETL Process → Data Warehouse → BI Tools
(Operational)    (Extract,      (Analytical)    (Reports)
                 Transform,
                 Load)
```

**Example Flow:**
```
1. E-commerce site (PostgreSQL)
   - Orders table
   - Users table
   - Products table

2. ETL Process (nightly):
   - Extract data from PostgreSQL
   - Transform (clean, aggregate)
   - Load into Snowflake

3. Data Warehouse (Snowflake):
   - Fact table: sales
   - Dimension tables: users, products, time

4. BI Tools (Tableau):
   - "Total sales by region"
   - "Top 10 products"
   - "Customer lifetime value"
```

**Popular Data Warehouses:**
- Snowflake (cloud-native)
- Google BigQuery (serverless)
- Amazon Redshift (AWS)
- Azure Synapse (Microsoft)

---

### Columnar vs Row Storage

#### Row Storage (Traditional)

**How Data Stored:**
```
Row 1: [ID=1, Name=Alice, Age=25, City=NYC]
Row 2: [ID=2, Name=Bob, Age=30, City=LA]
Row 3: [ID=3, Name=Charlie, Age=35, City=NYC]
```

**Query: "SELECT Name WHERE City = 'NYC'"**
```
Must read entire rows:
Row 1: [1, Alice, 25, NYC] → Match ✅
Row 2: [2, Bob, 30, LA] → No match
Row 3: [3, Charlie, 35, NYC] → Match ✅

Read: 3 full rows (wasteful)
```

**Good For:**
- OLTP (transactional)
- Fetching entire rows
- INSERT/UPDATE heavy

---

#### Column Storage (Analytical)

**How Data Stored:**
```
Column ID: [1, 2, 3]
Column Name: [Alice, Bob, Charlie]
Column Age: [25, 30, 35]
Column City: [NYC, LA, NYC]
```

**Query: "SELECT Name WHERE City = 'NYC'"**
```
Only read City and Name columns:
City: [NYC, LA, NYC] → Positions 0, 2 match
Name: [Alice, Charlie] → Return these

Read: Only 2 columns (efficient ✅)
```

**Benefits:**
- ✅ Better compression (similar data together)
- ✅ Faster aggregations (SUM, AVG, COUNT)
- ✅ Read only needed columns

**Good For:**
- OLAP (analytical)
- Aggregations
- SELECT heavy

**Popular Columnar Databases:**
- ClickHouse
- Apache Druid
- Amazon Redshift
- Google BigQuery

---

### Time-Series Databases

**What**: Optimized for time-stamped data

**Example Data:**
```
Timestamp           | Server | CPU% | Memory%
2024-01-01 10:00:00 | srv1   | 50   | 60
2024-01-01 10:00:01 | srv1   | 52   | 61
2024-01-01 10:00:02 | srv1   | 48   | 59
...
```

**Optimizations:**

**1. Compression**
```
Instead of storing:
50, 52, 48, 51, 49, 50, 52...

Store:
Base: 50
Deltas: +2, -4, +3, -2, +1, +2...
(Much smaller ✅)
```

**2. Downsampling**
```
Raw data (1-second intervals): 86,400 points/day
1-minute aggregates: 1,440 points/day
1-hour aggregates: 24 points/day

Keep raw for 7 days
Keep 1-min for 30 days
Keep 1-hour forever
```

**3. Time-based Partitioning**
```
Partition by day:
2024-01-01: Partition 1
2024-01-02: Partition 2
...

Query "Last 24 hours" → Only read 1 partition ✅
```

**Use Cases:**
- Monitoring (CPU, memory, disk)
- IoT sensors
- Financial data (stock prices)
- Application metrics

**Popular Time-Series DBs:**
- InfluxDB
- TimescaleDB (PostgreSQL extension)
- VictoriaMetrics
- Prometheus

---

### Hot vs Warm vs Cold Storage

**Analogy**: Kitchen (hot), garage (warm), attic (cold)

#### Hot Storage
**What**: Frequently accessed, needs fast access

**Characteristics:**
- SSD storage
- Low latency (< 10ms)
- Expensive ($$$)

**Examples:**
- Active user data
- Recent orders (last 30 days)
- Current session data

**Technologies:**
- Redis (in-memory)
- PostgreSQL (SSD)
- DynamoDB

---

#### Warm Storage
**What**: Occasionally accessed, moderate speed okay

**Characteristics:**
- HDD or cheaper SSD
- Medium latency (< 100ms)
- Moderate cost ($$)

**Examples:**
- Historical data (1-12 months old)
- Archived logs
- Backup data

**Technologies:**
- Amazon S3 Standard-IA
- Azure Cool Blob Storage
- Google Cloud Nearline

---

#### Cold Storage
**What**: Rarely accessed, slow retrieval okay

**Characteristics:**
- Tape or glacier storage
- High latency (hours)
- Very cheap ($)

**Examples:**
- Compliance data (must keep 7 years)
- Old backups
- Archived videos

**Technologies:**
- Amazon S3 Glacier
- Azure Archive Storage
- Google Cloud Coldline

**Cost Comparison:**
```
Hot: $0.023/GB/month
Warm: $0.0125/GB/month
Cold: $0.004/GB/month

1 TB for 1 year:
Hot: $276
Warm: $150
Cold: $48
```

---

## 2.3 Security in System Design

### Authentication vs Authorization

**Authentication**: Who are you?
```
User: "I'm Alice"
System: "Prove it" (password, fingerprint, etc.)
```

**Authorization**: What can you do?
```
Alice: "I want to delete post 123"
System: "Are you the author?" (check permissions)
```

---

### OAuth 2.0

**What**: Delegated authorization (login with Google, Facebook, etc.)

**Flow:**
```
1. User clicks "Login with Google"
2. Redirected to Google login page
3. User logs in to Google
4. Google asks: "Allow MyApp to access your profile?"
5. User clicks "Allow"
6. Google redirects back with authorization code
7. MyApp exchanges code for access token
8. MyApp uses token to fetch user profile from Google
```

**Benefits:**
- ✅ No need to store passwords
- ✅ User trusts Google more than your app
- ✅ Single sign-on (SSO)

---

### JWT (JSON Web Token)

**What**: Self-contained token with user info

**Structure:**
```
Header.Payload.Signature

Header: {"alg": "HS256", "typ": "JWT"}
Payload: {"userId": 123, "email": "alice@example.com", "exp": 1640000000}
Signature: HMAC(Header + Payload, secret)
```

**Flow:**
```
1. User logs in
2. Server creates JWT with user info
3. Server signs JWT with secret key
4. Server returns JWT to client
5. Client stores JWT (localStorage)
6. Client sends JWT with every request
7. Server verifies JWT signature
8. Server extracts user info from JWT
```

**Benefits:**
- ✅ Stateless (no session storage needed)
- ✅ Scalable (any server can verify)
- ✅ Self-contained (all info in token)

**Security:**
- ⚠️ Store secret key securely
- ⚠️ Use HTTPS (prevent token theft)
- ⚠️ Set expiration time (refresh tokens)

---

### API Security

#### HMAC (Hash-based Message Authentication Code)

**What**: Verify request hasn't been tampered with

**Flow:**
```
Client:
1. Create request: GET /api/users
2. Generate signature: HMAC(request + timestamp, secret)
3. Send: Request + Signature + Timestamp

Server:
1. Receive request
2. Generate signature: HMAC(request + timestamp, secret)
3. Compare signatures
4. If match, request is authentic ✅
```

**Benefits:**
- ✅ Prevents tampering
- ✅ Prevents replay attacks (timestamp)

---

#### mTLS (Mutual TLS)

**What**: Both client and server verify each other

**Traditional TLS:**
```
Client verifies server's certificate
(Browser checks if website is legitimate)
```

**mTLS:**
```
Client verifies server's certificate
Server verifies client's certificate
(Both prove identity)
```

**Use Cases:**
- Microservices communication
- API-to-API calls
- High-security environments

---

### Rate Limiting & WAF

#### Rate Limiting (Already Covered)

**Purpose**: Prevent abuse, ensure fair usage

**Example:**
```
Max 100 requests/minute per user
Max 1000 requests/minute per IP
```

---

#### WAF (Web Application Firewall)

**What**: Filter malicious HTTP traffic

**Protections:**

**1. SQL Injection**
```
Malicious: /api/users?id=1 OR 1=1
WAF blocks: Detects SQL keywords
```

**2. XSS (Cross-Site Scripting)**
```
Malicious: <script>alert('hacked')</script>
WAF blocks: Detects script tags
```

**3. DDoS Protection**
```
10,000 requests/second from same IP
WAF blocks: Rate limiting
```

**4. Bot Detection**
```
Request pattern looks like bot
WAF challenges: CAPTCHA
```

**Popular WAFs:**
- Cloudflare
- AWS WAF
- Azure WAF
- Akamai

---

### Zero-Trust Architecture

**Traditional Security**: Trust inside network, don't trust outside
```
Inside corporate network → Trusted ✅
Outside → Not trusted ❌
```

**Zero-Trust**: Never trust, always verify
```
Every request verified, even from inside ✅
```

**Principles:**

**1. Verify Explicitly**
```
Always authenticate and authorize
Based on: user, device, location, behavior
```

**2. Least Privilege Access**
```
Grant minimum permissions needed
User can only access what they need
```

**3. Assume Breach**
```
Assume attacker is already inside
Segment network, monitor everything
```

**Implementation:**
- Multi-factor authentication (MFA)
- Device health checks
- Micro-segmentation
- Continuous monitoring

---

## 2.4 Designing Real-World Systems

### URL Shortener (like Bit.ly)

#### Requirements

**Functional:**
- Shorten long URL to short URL
- Redirect short URL to original URL
- Custom short URLs (optional)
- Analytics (click count)

**Non-Functional:**
- Low latency (< 100ms)
- High availability (99.9%)
- Scale: 100M URLs, 10K requests/second

---

#### Design

**1. API Design**
```
POST /shorten
Body: {"url": "https://example.com/very/long/url"}
Response: {"shortUrl": "https://bit.ly/abc123"}

GET /abc123
Response: Redirect to original URL
```

**2. URL Encoding**

**Base62 Encoding** (a-z, A-Z, 0-9 = 62 characters)
```
ID: 12345
Base62: "3D7"

6 characters = 62^6 = 56 billion URLs ✅
```

**3. Database Schema**
```sql
CREATE TABLE urls (
  id BIGINT PRIMARY KEY,
  short_code VARCHAR(10) UNIQUE,
  original_url TEXT,
  created_at TIMESTAMP,
  click_count INT DEFAULT 0
);

CREATE INDEX idx_short_code ON urls(short_code);
```

**4. Architecture**
```
┌─────────┐
│  User   │
└────┬────┘
     │ 1. POST /shorten
     ↓
┌─────────────┐
│ API Gateway │
└─────┬───────┘
      │ 2. Forward
      ↓
┌─────────────┐
│   Server    │
└─────┬───────┘
      │ 3. Generate ID, encode
      ↓
┌─────────────┐
│  Database   │ ← Store mapping
└─────────────┘

Redirect Flow:
User → GET /abc123 → Server → Cache (Redis)
                            ↓ (if miss)
                         Database
                            ↓
                    Redirect to original URL
```

**5. Optimizations**

**Caching:**
```
Cache popular URLs in Redis
TTL: 24 hours
Hit rate: 80%+ (most URLs accessed multiple times)
```

**Analytics (Async):**
```
Don't update click_count in real-time (slow)
Instead:
1. Increment counter in Redis
2. Batch update database every 5 minutes
```

**Rate Limiting:**
```
Max 10 URL shortens per user per minute
Prevent spam
```

---

### Video Streaming Platform (like YouTube)

#### Requirements

**Functional:**
- Upload videos
- Watch videos
- Search videos
- Like/comment

**Non-Functional:**
- Low latency (video starts < 2 seconds)
- High availability (99.99%)
- Scale: 1 billion users, 500 hours uploaded/minute

---

#### Design

**1. Video Upload Flow**
```
┌─────────┐
│  User   │
└────┬────┘
     │ 1. Upload video
     ↓
┌─────────────────┐
│  Upload Service │
└────┬────────────┘
     │ 2. Store raw video
     ↓
┌─────────────────┐
│  Object Storage │ ← S3/GCS (raw video)
│   (S3/GCS)      │
└────┬────────────┘
     │ 3. Trigger processing
     ↓
┌─────────────────┐
│ Encoding Service│ ← Transcode to multiple formats
│  (FFmpeg)       │    - 1080p, 720p, 480p, 360p
└────┬────────────┘
     │ 4. Store encoded videos
     ↓
┌─────────────────┐
│      CDN        │ ← Distribute globally
└─────────────────┘
```

**2. Video Encoding**

**Why Multiple Formats?**
- Different devices (phone, TV, laptop)
- Different network speeds (4G, WiFi, slow)

**Adaptive Bitrate Streaming (HLS/DASH):**
```
Video split into chunks (10 seconds each)
Each chunk in multiple qualities:
- 1080p (5 Mbps)
- 720p (2.5 Mbps)
- 480p (1 Mbps)
- 360p (0.5 Mbps)

Player automatically switches based on network speed
```

**3. Video Playback Flow**
```
┌─────────┐
│  User   │
└────┬────┘
     │ 1. Request video
     ↓
┌─────────────────┐
│  API Gateway    │
└────┬────────────┘
     │ 2. Get video metadata
     ↓
┌─────────────────┐
│  Metadata DB    │ ← PostgreSQL (video info)
└────┬────────────┘
     │ 3. Get video URL
     ↓
┌─────────────────┐
│      CDN        │ ← Serve video chunks
│  (CloudFront)   │
└─────────────────┘
```

**4. Database Schema**
```sql
-- Videos table
CREATE TABLE videos (
  id UUID PRIMARY KEY,
  user_id UUID,
  title VARCHAR(255),
  description TEXT,
  duration INT,
  views BIGINT DEFAULT 0,
  likes BIGINT DEFAULT 0,
  created_at TIMESTAMP
);

-- Comments table
CREATE TABLE comments (
  id UUID PRIMARY KEY,
  video_id UUID,
  user_id UUID,
  text TEXT,
  created_at TIMESTAMP
);

-- Indexes
CREATE INDEX idx_user_videos ON videos(user_id);
CREATE INDEX idx_video_comments ON comments(video_id);
```

**5. Search**

**Elasticsearch:**
```
Index video metadata:
- Title
- Description
- Tags
- Transcript (speech-to-text)

Search query: "machine learning"
Elasticsearch returns relevant videos
```

**6. Recommendations**

**Collaborative Filtering:**
```
Users who watched video A also watched video B
Recommend video B to users watching A
```

**Content-Based:**
```
User watches "Python tutorial"
Recommend similar videos (same tags, category)
```

**7. Scalability**

**CDN:**
```
Videos served from edge locations
User in Tokyo → Tokyo CDN server
User in NYC → NYC CDN server
(Low latency ✅)
```

**Sharding:**
```
Videos sharded by upload date:
2024-01 → Shard 1
2024-02 → Shard 2
...
```

**Caching:**
```
Cache popular videos in memory
Cache video metadata in Redis
```

---

### Messaging System (like WhatsApp)

#### Requirements

**Functional:**
- Send/receive messages
- Group chats
- Media sharing (images, videos)
- Online status
- Read receipts

**Non-Functional:**
- Real-time (< 100ms delivery)
- High availability (99.99%)
- Scale: 2 billion users, 100 billion messages/day

---

#### Design

**1. Architecture**
```
┌─────────┐                    ┌─────────┐
│ User A  │                    │ User B  │
└────┬────┘                    └────┬────┘
     │                              │
     │ WebSocket                    │ WebSocket
     ↓                              ↓
┌─────────────────────────────────────────┐
│          Gateway Servers                │
│       (WebSocket connections)           │
└─────────────────┬───────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────┐
│         Message Queue (Kafka)           │
└─────────────────┬───────────────────────┘
                  │
     ┌────────────┼────────────┐
     ↓            ↓            ↓
┌──────────┐ ┌──────────┐ ┌──────────┐
│ Message  │ │ Delivery │ │Analytics │
│ Service  │ │ Service  │ │ Service  │
└────┬─────┘ └────┬─────┘ └──────────┘
     │            │
     ↓            ↓
┌──────────┐ ┌──────────┐
│ Cassandra│ │  Redis   │
│(Messages)│ │ (Online) │
└──────────┘ └──────────┘
```

**2. Message Flow**

**Sending Message:**
```
1. User A sends message via WebSocket
2. Gateway receives, publishes to Kafka
3. Message Service stores in Cassandra
4. Delivery Service checks if User B online
5. If online, push via WebSocket
6. If offline, store in queue (deliver when online)
```

**3. Database Schema**

**Cassandra (Messages):**
```
CREATE TABLE messages (
  conversation_id UUID,
  message_id TIMEUUID,
  sender_id UUID,
  receiver_id UUID,
  text TEXT,
  media_url TEXT,
  timestamp TIMESTAMP,
  PRIMARY KEY (conversation_id, message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);

Query: "Get last 50 messages in conversation"
SELECT * FROM messages 
WHERE conversation_id = ? 
LIMIT 50;
```

**Redis (Online Status):**
```
Key: "user:123:online"
Value: "true"
TTL: 60 seconds (refresh every 30s)

If user disconnects, key expires → offline
```

**4. Group Chats**

**Fan-Out on Write:**
```
User sends message to group (100 members)
Message Service creates 100 copies
Each member gets their own copy

Pros: Fast reads
Cons: More storage
```

**5. Media Sharing**

**Upload Flow:**
```
1. User uploads image
2. Upload to S3
3. Generate thumbnail (async)
4. Send message with S3 URL
```

**6. End-to-End Encryption**

**Signal Protocol:**
```
1. Each user has public/private key pair
2. Sender encrypts with receiver's public key
3. Only receiver can decrypt (with private key)
4. Server can't read messages ✅
```

**7. Scalability**

**WebSocket Connections:**
```
1 server = 10K connections
1M users = 100 servers
Use load balancer to distribute
```

**Message Delivery:**
```
If user offline, store in queue
When user comes online, deliver all pending
```

**Read Receipts:**
```
Async process (don't block message delivery)
Update in background
```

---

## Phase 3: Advanced Topics {#phase-3-advanced}

### 🎯 Goal: Google/Meta level expertise

---

## 3.1 Cloud Architecture & DevOps

### Cloud Providers Overview

#### AWS (Amazon Web Services)

**Key Services:**
- EC2 (Virtual Machines)
- S3 (Object Storage)
- RDS (Managed Databases)
- Lambda (Serverless Functions)
- ECS/EKS (Container Orchestration)
- CloudFront (CDN)
- Route 53 (DNS)

**Strengths:**
- Most mature (2006)
- Largest market share
- Most services available

---

#### GCP (Google Cloud Platform)

**Key Services:**
- Compute Engine (VMs)
- Cloud Storage (Object Storage)
- Cloud SQL (Managed Databases)
- Cloud Functions (Serverless)
- GKE (Kubernetes)
- Cloud CDN
- Cloud DNS

**Strengths:**
- Best for data/ML (BigQuery, TensorFlow)
- Strong Kubernetes support
- Competitive pricing

---

#### Azure (Microsoft)

**Key Services:**
- Virtual Machines
- Blob Storage
- Azure SQL Database
- Azure Functions
- AKS (Kubernetes)
- Azure CDN
- Azure DNS

**Strengths:**
- Best for enterprises (Microsoft stack)
- Hybrid cloud (on-prem + cloud)
- Active Directory integration

---

### Kubernetes (K8s)

**What**: Container orchestration platform

**Why Needed?**
```
Without K8s:
- Manually deploy containers to servers
- Manually restart if crashes
- Manually scale up/down
- Manually load balance
(Nightmare ❌)

With K8s:
- Declare desired state
- K8s handles everything automatically
(Easy ✅)
```

#### Core Concepts

**1. Pod**: Smallest unit (one or more containers)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app
    image: my-app:1.0
    ports:
    - containerPort: 8080
```

**2. Deployment**: Manages multiple pods
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3  # Run 3 pods
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: my-app:1.0
```

**3. Service**: Load balancer for pods
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer
```

**4. Autoscaling**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

**How It Works:**
```
CPU > 70% → Scale up (add pods)
CPU < 70% → Scale down (remove pods)
Min: 2 pods, Max: 10 pods
```

---

### CI/CD Pipelines

**What**: Automate build, test, deploy

**Traditional Deployment:**
```
1. Developer writes code
2. Manually test on local machine
3. Manually copy to server
4. Manually restart service
5. Hope it works ❌
```

**CI/CD Pipeline:**
```
1. Developer pushes code to Git
2. Automated tests run
3. If tests pass, build Docker image
4. Deploy to staging automatically
5. Run integration tests
6. Deploy to production automatically ✅
```

#### Example Pipeline (GitHub Actions)

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run tests
        run: npm test

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build Docker image
        run: docker build -t my-app:${{ github.sha }} .
      - name: Push to registry
        run: docker push my-app:${{ github.sha }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Kubernetes
        run: kubectl set image deployment/my-app app=my-app:${{ github.sha }}
```

**Benefits:**
- ✅ Fast feedback (know if code breaks immediately)
- ✅ Consistent (same process every time)
- ✅ Reliable (automated tests catch bugs)
- ✅ Frequent deployments (deploy multiple times/day)

---

### Infrastructure as Code (Terraform)

**What**: Define infrastructure in code files

**Traditional Way:**
```
1. Log into AWS console
2. Click "Create EC2 instance"
3. Fill form (instance type, security groups, etc.)
4. Click "Launch"
5. Repeat for each resource ❌
(Manual, error-prone, not reproducible)
```

**Terraform Way:**
```hcl
# main.tf
resource "aws_instance" "web" {
  ami           = "ami-12345678"
  instance_type = "t3.medium"
  
  tags = {
    Name = "WebServer"
  }
}

resource "aws_security_group" "web" {
  name = "web-sg"
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# Run: terraform apply
# Creates all resources automatically ✅
```

**Benefits:**
- ✅ Version control (track changes)
- ✅ Reproducible (same infrastructure every time)
- ✅ Collaborative (team can review)
- ✅ Automated (no manual clicking)

---

### Service Mesh (Istio, Linkerd)

**What**: Infrastructure layer for microservices communication

**Without Service Mesh:**
```
Service A → Service B
- Must implement: retry logic, timeouts, circuit breakers
- Must implement: TLS, authentication
- Must implement: monitoring, tracing
(Every service implements same logic ❌)
```

**With Service Mesh:**
```
Service A → Sidecar Proxy → Sidecar Proxy → Service B
            (Envoy)          (Envoy)

Sidecar handles:
- Retries, timeouts
- TLS, auth
- Monitoring, tracing
(Services focus on business logic ✅)
```

**Features:**

**1. Traffic Management**
```
Route 90% traffic to v1
Route 10% traffic to v2 (canary deployment)
```

**2. Security**
```
Automatic mTLS between services
```

**3. Observability**
```
Automatic metrics, logs, traces
```

**4. Resilience**
```
Automatic retries, circuit breakers
```

---

## 3.2 High Availability & Fault Tolerance

### Disaster Recovery

#### RPO (Recovery Point Objective)

**What**: How much data loss is acceptable?

**Example:**
```
RPO = 1 hour
Database backed up every hour

Disaster at 3:30 PM
Last backup: 3:00 PM
Data loss: 30 minutes ❌

RPO = 5 minutes
Database backed up every 5 minutes
Data loss: Max 5 minutes ✅
```

**Strategies:**
- RPO = 0: Real-time replication (expensive)
- RPO = 5 min: Frequent backups
- RPO = 24 hours: Daily backups (cheap)

---

#### RTO (Recovery Time Objective)

**What**: How long until system is back up?

**Example:**
```
RTO = 4 hours
Disaster at 3:00 PM
Must be back up by 7:00 PM

RTO = 15 minutes
Disaster at 3:00 PM
Must be back up by 3:15 PM
```

**Strategies:**
- RTO = 0: Active-active (instant failover)
- RTO = 15 min: Hot standby
- RTO = 4 hours: Warm standby
- RTO = 24 hours: Cold backup

---

### Multi-Zone & Multi-Region Deployment

#### Single Zone (Bad ❌)
```
All servers in one data center
Data center fails → Everything down
```

#### Multi-Zone (Better ✅)
```
Servers in multiple data centers (same region)
Zone A fails → Zone B takes over

AWS Regions:
- us-east-1 (Virginia)
  - Zone A (Data center 1)
  - Zone B (Data center 2)
  - Zone C (Data center 3)
```

**Benefits:**
- ✅ Protects against data center failure
- ✅ Low latency (zones close together)

**Limitations:**
- ⚠️ Entire region can fail (rare but possible)

---

#### Multi-Region (Best 🌟)
```
Servers in multiple geographic regions
Region fails → Another region takes over

Example:
- Primary: us-east-1 (Virginia)
- Secondary: us-west-2 (Oregon)
- Tertiary: eu-west-1 (Ireland)
```

**Benefits:**
- ✅ Protects against regional disasters
- ✅ Low latency for global users
- ✅ Compliance (data residency)

**Challenges:**
- ❌ Data consistency (replication lag)
- ❌ Complex routing
- ❌ Expensive

---

### Deployment Strategies

#### Blue-Green Deployment

**Concept**: Two identical environments

```
Blue (Current):  v1.0 (100% traffic)
Green (New):     v2.0 (0% traffic)

Deploy v2.0 to Green
Test Green thoroughly
Switch traffic: Blue → Green
Green now 100% traffic ✅

If issues, switch back: Green → Blue (instant rollback)
```

**Benefits:**
- ✅ Zero downtime
- ✅ Instant rollback
- ✅ Test in production environment

**Cons:**
- ❌ Expensive (2x infrastructure)

---

#### Canary Deployment

**Concept**: Gradual rollout

```
v1.0: 100% traffic
Deploy v2.0

Stage 1: v1.0 (95%), v2.0 (5%)
Monitor metrics, errors
If good, proceed

Stage 2: v1.0 (80%), v2.0 (20%)
Monitor
If good, proceed

Stage 3: v1.0 (50%), v2.0 (50%)
Monitor
If good, proceed

Stage 4: v1.0 (0%), v2.0 (100%) ✅
```

**Benefits:**
- ✅ Low risk (catch issues early)
- ✅ Real user feedback
- ✅ Cost-effective

**Cons:**
- ❌ Slower rollout
- ❌ Complex routing

---

### Distributed Tracing

**Problem**: Microservices make debugging hard

**Example Request:**
```
User request → API Gateway → Auth Service → User Service → Database
                          ↓
                      Order Service → Inventory Service → Database
                                   ↓
                              Payment Service → External API

Request takes 5 seconds. Where's the bottleneck? ❓
```

**Solution**: Distributed Tracing

**How It Works:**
```
1. Generate unique Trace ID for request
2. Each service adds Span (timing info)
3. Collect all spans
4. Visualize timeline

Trace ID: abc123
- API Gateway: 10ms
- Auth Service: 50ms
- User Service: 100ms
- Order Service: 200ms
- Inventory Service: 4500ms ← Bottleneck! ❌
- Payment Service: 140ms
```

**Tools:**
- Jaeger (open-source)
- Zipkin (open-source)
- AWS X-Ray
- Google Cloud Trace
- Datadog APM

---

### Circuit Breaker Pattern

**Problem**: Cascading failures

**Example:**
```
Payment Service down
Order Service keeps calling Payment Service
Order Service times out (30 seconds each)
Order Service becomes slow
API Gateway times out calling Order Service
Entire system slow ❌
```

**Solution**: Circuit Breaker

**States:**

**1. Closed (Normal)**
```
Requests flow normally
Track failures
```

**2. Open (Failing)**
```
Too many failures (e.g., 50% in 1 minute)
Stop sending requests
Return error immediately (fail fast)
Wait 30 seconds
```

**3. Half-Open (Testing)**
```
After 30 seconds, try one request
If succeeds → Close (back to normal)
If fails → Open again (wait longer)
```

**Benefits:**
- ✅ Prevent cascading failures
- ✅ Fail fast (don't wait for timeout)
- ✅ Automatic recovery

**Implementation:**
```python
class CircuitBreaker:
    def __init__(self):
        self.state = "CLOSED"
        self.failures = 0
        self.threshold = 5
        
    def call(self, func):
        if self.state == "OPEN":
            raise Exception("Circuit breaker open")
        
        try:
            result = func()
            self.failures = 0  # Reset on success
            return result
        except Exception as e:
            self.failures += 1
            if self.failures >= self.threshold:
                self.state = "OPEN"
            raise e
```

---

## 3.3 Real-Time Data Processing

### Batch vs Stream Processing

#### Batch Processing

**What**: Process large amounts of data at once

**Example:**
```
Daily sales report:
1. Collect all sales from yesterday
2. Process at midnight
3. Generate report
4. Send email

Data: 24 hours old
Latency: Hours
```

**Use Cases:**
- Daily reports
- Monthly analytics
- Data warehousing
- Machine learning training

**Tools:**
- Apache Hadoop
- Apache Spark
- AWS EMR

---

#### Stream Processing

**What**: Process data as it arrives

**Example:**
```
Real-time fraud detection:
1. Transaction happens
2. Process immediately
3. Block if fraud detected
4. All within milliseconds

Data: Real-time
Latency: Milliseconds
```

**Use Cases:**
- Fraud detection
- Real-time analytics
- IoT sensor data
- Stock trading

**Tools:**
- Apache Kafka + Flink
- Apache Storm
- AWS Kinesis

---

### Kafka + Flink Architecture

```
Data Sources → Kafka → Flink → Data Sinks
(Producers)   (Queue)  (Process) (Consumers)
```

**Example: Real-Time Analytics**

**1. Kafka (Message Queue)**
```
Topic: "user-clicks"
Messages:
- {"userId": 123, "page": "/home", "timestamp": "2024-01-01 10:00:00"}
- {"userId": 456, "page": "/product", "timestamp": "2024-01-01 10:00:01"}
- ...
```

**2. Flink (Stream Processing)**
```java
// Count clicks per page in 1-minute windows
DataStream<Click> clicks = env.addSource(kafkaConsumer);

clicks
  .keyBy(click -> click.page)
  .window(TumblingEventTimeWindows.of(Time.minutes(1)))
  .sum("count")
  .addSink(elasticsearchSink);
```

**3. Output**
```
Window: 10:00-10:01
- /home: 1000 clicks
- /product: 500 clicks
- /checkout: 200 clicks
```

---

### Data Lake vs Data Warehouse vs Lakehouse

#### Data Lake

**What**: Store raw data (all formats)

**Characteristics:**
- Unstructured data (JSON, logs, images, videos)
- Schema-on-read (define schema when reading)
- Cheap storage
- Flexible

**Example:**
```
S3 bucket:
- /logs/2024-01-01/*.log
- /images/2024-01-01/*.jpg
- /videos/2024-01-01/*.mp4
- /json/2024-01-01/*.json
```

**Use Cases:**
- Data science (need raw data)
- Machine learning (training data)
- Backup (store everything)

**Cons:**
- ❌ Can become "data swamp" (messy)
- ❌ Slow queries (no optimization)

---

#### Data Warehouse

**What**: Store structured data for analytics

**Characteristics:**
- Structured data (tables)
- Schema-on-write (define schema when writing)
- Optimized for queries
- Expensive

**Example:**
```
Snowflake:
- sales_fact table
- customers_dim table
- products_dim table
(Star schema)
```

**Use Cases:**
- Business intelligence
- Reports and dashboards
- SQL analytics

**Cons:**
- ❌ Expensive
- ❌ Rigid schema

---

#### Lakehouse

**What**: Combines data lake + data warehouse

**Characteristics:**
- Store raw data (like data lake)
- ACID transactions (like data warehouse)
- Schema enforcement (like data warehouse)
- Cheap storage (like data lake)

**Technologies:**
- Delta Lake (Databricks)
- Apache Iceberg
- Apache Hudi

**Benefits:**
- ✅ Best of both worlds
- ✅ Single source of truth
- ✅ Cost-effective

---

## 3.4 AI/ML in System Design

### Recommendation System

**Goal**: Suggest relevant items to users

#### Collaborative Filtering

**Concept**: "Users like you also liked..."

**Example:**
```
User A liked: Movie 1, Movie 2, Movie 3
User B liked: Movie 1, Movie 2, Movie 4
User C liked: Movie 1, Movie 2

Recommendation for User C: Movie 3, Movie 4
(Because similar users liked them)
```

**Algorithm:**
```
1. Find similar users (based on past behavior)
2. Recommend items those users liked
```

**Pros:**
- ✅ No need to understand items
- ✅ Discovers unexpected recommendations

**Cons:**
- ❌ Cold start problem (new users/items)
- ❌ Scalability (compute similarity for millions)

---

#### Content-Based Filtering

**Concept**: "You liked X, here's similar Y"

**Example:**
```
User watched: "Inception" (Sci-fi, Action, Christopher Nolan)
Recommend: "Interstellar" (Sci-fi, Action, Christopher Nolan)
```

**Algorithm:**
```
1. Extract features from items (genre, director, actors)
2. Find similar items (cosine similarity)
3. Recommend top matches
```

**Pros:**
- ✅ No cold start for items (can recommend new items)
- ✅ Explainable (know why recommended)

**Cons:**
- ❌ Limited diversity (only similar items)
- ❌ Need good features

---

#### Hybrid Approach

**Combine both:**
```
1. Collaborative filtering (70% weight)
2. Content-based filtering (30% weight)
3. Blend recommendations
```

**Architecture:**
```
User → API → Recommendation Service
                     ↓
         ┌───────────┴───────────┐
         ↓                       ↓
  Collaborative Model    Content-Based Model
         ↓                       ↓
         └───────────┬───────────┘
                     ↓
              Ranking/Blending
                     ↓
              Top 10 Items
```

---

### Feature Store

**What**: Centralized repository for ML features

**Problem Without Feature Store:**
```
Data Scientist 1: Computes "user_age" feature
Data Scientist 2: Computes "user_age" feature (different logic)
Production: Computes "user_age" feature (different again)

Result: Inconsistent features, training/serving skew ❌
```

**Solution: Feature Store**
```
Define feature once:
  user_age = current_year - birth_year

Use everywhere:
- Training (offline)
- Serving (online)
- Batch predictions

Result: Consistent features ✅
```

**Architecture:**
```
┌─────────────────────────────────────┐
│         Feature Store (Feast)        │
├─────────────────────────────────────┤
│  Offline Store        Online Store  │
│  (S3/BigQuery)        (Redis/DynamoDB)│
│  - Historical data    - Real-time    │
│  - Training           - Serving      │
└─────────────────────────────────────┘
         ↑                    ↑
         │                    │
    Training              Prediction
    Pipeline              Service
```

**Benefits:**
- ✅ Consistency (same features everywhere)
- ✅ Reusability (share features across teams)
- ✅ Monitoring (track feature quality)

---

### Vector Databases

**What**: Store and search high-dimensional vectors

**Use Case**: Semantic search

**Example:**
```
Traditional search:
Query: "dog"
Results: Documents containing word "dog"
Misses: "puppy", "canine" ❌

Semantic search:
Query: "dog" → Vector: [0.2, 0.8, 0.1, ...]
Find similar vectors:
- "puppy" → [0.3, 0.7, 0.2, ...]  (similar ✅)
- "canine" → [0.25, 0.75, 0.15, ...] (similar ✅)
- "cat" → [0.8, 0.2, 0.1, ...]  (not similar)
```

**How It Works:**
```
1. Convert text to embeddings (vectors)
   "dog" → [0.2, 0.8, 0.1, ...]
   
2. Store in vector database
   
3. Query with vector
   
4. Find nearest neighbors (cosine similarity)
```

**Popular Vector Databases:**
- Pinecone (managed)
- Weaviate (open-source)
- Milvus (open-source)
- Qdrant (open-source)

**Use Cases:**
- Semantic search
- Recommendation systems
- Image similarity
- Chatbots (RAG - Retrieval Augmented Generation)

---

## Phase 4: Interview Preparation {#phase-4-interview-prep}

### Interview Framework (RADIO)

Use this framework for EVERY system design interview.

#### R - Requirements (5-10 minutes)

**Clarify functional and non-functional requirements**

**Questions to Ask:**

**Functional:**
- What are the core features?
- What's the user flow?
- What's in scope vs out of scope?

**Non-Functional:**
- How many users?
- How many requests per second?
- What's the expected latency?
- What's the availability requirement?
- Read-heavy or write-heavy?
- Any data consistency requirements?

**Example (URL Shortener):**
```
Interviewer: "Design a URL shortener"

You: "Let me clarify the requirements:

Functional:
- Shorten long URL to short URL? Yes
- Redirect short URL to original? Yes
- Custom short URLs? Out of scope
- Analytics? Yes, basic click count

Non-Functional:
- Scale: 100M URLs, 10K requests/second
- Latency: < 100ms
- Availability: 99.9%
- Read/Write ratio: 100:1 (read-heavy)

Does this sound right?"
```

---

#### A - API Design (5 minutes)

**Define the API endpoints**

**Example (URL Shortener):**
```
POST /api/v1/shorten
Request:
{
  "url": "https://example.com/very/long/url",
  "userId": "123"
}
Response:
{
  "shortUrl": "https://bit.ly/abc123",
  "createdAt": "2024-01-01T10:00:00Z"
}

GET /api/v1/{shortCode}
Response: 302 Redirect to original URL

GET /api/v1/{shortCode}/stats
Response:
{
  "shortCode": "abc123",
  "originalUrl": "https://example.com/very/long/url",
  "clickCount": 1000,
  "createdAt": "2024-01-01T10:00:00Z"
}
```

---

#### D - Data Model (5-10 minutes)

**Design database schema**

**Example (URL Shortener):**
```sql
-- URLs table
CREATE TABLE urls (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  short_code VARCHAR(10) UNIQUE NOT NULL,
  original_url TEXT NOT NULL,
  user_id BIGINT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  click_count BIGINT DEFAULT 0,
  INDEX idx_short_code (short_code),
  INDEX idx_user_id (user_id)
);

-- Why these choices?
- id: Auto-increment for unique IDs
- short_code: Indexed for fast lookups
- original_url: TEXT for long URLs
- click_count: Denormalized for performance
```

**Database Choice:**
```
SQL (PostgreSQL) because:
- Structured data
- ACID transactions needed
- Complex queries (analytics)
- Moderate scale (can shard if needed)
```

---

#### I - Implementation (20-30 minutes)

**Design high-level architecture**

**Start Simple:**
```
┌─────────┐
│  User   │
└────┬────┘
     │
     ↓
┌─────────────┐
│   Server    │
└─────┬───────┘
     │
     ↓
┌─────────────┐
│  Database   │
└─────────────┘
```

**Then Scale:**
```
┌─────────┐
│  User   │
└────┬────┘
     │
     ↓
┌─────────────────┐
│  Load Balancer  │
└────┬────────────┘
     │
     ├──────────────────────┐
     ↓                      ↓
┌──────────┐          ┌──────────┐
│ Server 1 │          │ Server 2 │
└────┬─────┘          └────┬─────┘
     │                     │
     ↓                     ↓
┌──────────┐          ┌──────────┐
│  Cache   │          │  Cache   │
│ (Redis)  │          │ (Redis)  │
└────┬─────┘          └────┬─────┘
     │                     │
     └──────────┬──────────┘
                ↓
         ┌─────────────┐
         │  Database   │
         │  (Primary)  │
         └──────┬──────┘
                │ Replicate
         ┌──────┴──────┐
         ↓             ↓
    ┌─────────┐   ┌─────────┐
    │ Replica │   │ Replica │
    └─────────┘   └─────────┘
```

**Key Components:**

**1. Load Balancer**
- Distribute traffic
- Health checks
- SSL termination

**2. Application Servers**
- Stateless (can scale horizontally)
- Handle business logic
- Generate short codes

**3. Cache (Redis)**
- Cache popular URLs (80/20 rule)
- TTL: 24 hours
- Reduce database load

**4. Database**
- Primary: All writes
- Replicas: All reads
- Sharding if needed (by user_id or short_code)

**5. CDN (Optional)**
- Serve static content
- Reduce latency globally

---

#### O - Optimizations (10-15 minutes)

**Discuss bottlenecks and solutions**

**1. Short Code Generation**

**Problem**: Collisions (two URLs get same short code)

**Solution 1: Base62 Encoding**
```
Use auto-increment ID
Encode to Base62
ID 12345 → "3D7"
(No collisions ✅)
```

**Solution 2: Hash + Check**
```
Hash URL → MD5
Take first 6 characters
Check if exists in database
If yes, append counter
```

**2. Database Bottleneck**

**Problem**: Single database can't handle 10K requests/second

**Solutions:**
- Caching (Redis) - 80% cache hit rate
- Read replicas - Distribute reads
- Sharding - Split data across databases

**3. Hot URLs**

**Problem**: Some URLs very popular (celebrity tweet)

**Solution:**
- CDN caching
- Dedicated cache for hot URLs
- Rate limiting per URL

**4. Analytics**

**Problem**: Updating click_count on every request is slow

**Solution:**
- Increment counter in Redis (fast)
- Batch update database every 5 minutes
- Use message queue (Kafka) for async processing

---

### Common Trade-offs

#### 1. Consistency vs Availability (CAP Theorem)

**Scenario**: Multi-region database

**Option A: Strong Consistency (CP)**
```
Pros:
- Always read latest data
- No conflicts

Cons:
- Slower (wait for all regions to sync)
- Less available (if region down, block writes)

Use when: Banking, inventory
```

**Option B: Eventual Consistency (AP)**
```
Pros:
- Fast (don't wait for sync)
- Highly available (always accept writes)

Cons:
- Temporary inconsistencies
- Conflict resolution needed

Use when: Social media, analytics
```

---

#### 2. SQL vs NoSQL

**SQL:**
```
Pros:
- ACID transactions
- Complex queries (JOINs)
- Mature ecosystem

Cons:
- Harder to scale horizontally
- Rigid schema

Use when: 
- Complex relationships
- Need transactions
- Moderate scale
```

**NoSQL:**
```
Pros:
- Easy horizontal scaling
- Flexible schema
- High performance (simple queries)

Cons:
- No ACID (usually)
- Limited query capabilities
- Eventual consistency

Use when:
- Simple queries
- Massive scale
- Flexible schema
```

---

#### 3. Normalization vs Denormalization

**Normalized (SQL):**
```sql
-- Users table
users: id, name, email

-- Orders table
orders: id, user_id, product_id, amount

-- Products table
products: id, name, price

Query: "Get order with user and product info"
SELECT * FROM orders
JOIN users ON orders.user_id = users.id
JOIN products ON orders.product_id = products.id;

Pros: No data duplication, easy updates
Cons: Slow (multiple JOINs)
```

**Denormalized (NoSQL):**
```json
{
  "orderId": "123",
  "user": {
    "id": "456",
    "name": "Alice",
    "email": "alice@example.com"
  },
  "product": {
    "id": "789",
    "name": "Laptop",
    "price": 1000
  },
  "amount": 1000
}

Pros: Fast (single query), no JOINs
Cons: Data duplication, harder to update
```

**When to Denormalize:**
- Read-heavy workload
- Need low latency
- Data doesn't change often

---

#### 4. Synchronous vs Asynchronous

**Synchronous:**
```
User places order → Wait for:
  1. Inventory check
  2. Payment processing
  3. Email sending
  4. Analytics update
→ Return response (5 seconds ❌)

Pros: Simple, immediate feedback
Cons: Slow, blocks user
```

**Asynchronous:**
```
User places order → 
  1. Create order
  2. Publish event to queue
  3. Return response immediately (100ms ✅)

Background workers:
  - Inventory service listens → Updates stock
  - Payment service listens → Charges card
  - Email service listens → Sends email
  - Analytics service listens → Updates dashboard

Pros: Fast, scalable, resilient
Cons: Complex, eventual consistency
```

---

### Interview Tips

#### 1. Start Simple, Then Scale

**Bad:**
```
Immediately jump to complex architecture with:
- Kafka
- Kubernetes
- Microservices
- Multi-region
(Interviewer thinks: "Overengineering")
```

**Good:**
```
Start: Single server + database
Then: "To handle 10K requests/second, we need..."
- Load balancer
- Multiple servers
- Caching
- Read replicas
(Shows you understand trade-offs)
```

---

#### 2. Ask Clarifying Questions

**Bad:**
```
Interviewer: "Design Twitter"
You: "Okay, here's my design..."
(Interviewer thinks: "Didn't clarify requirements")
```

**Good:**
```
Interviewer: "Design Twitter"
You: "Let me clarify:
- Are we focusing on timeline, tweets, or both?
- What's the expected scale?
- Any specific features (DMs, trending, etc.)?
- Read-heavy or write-heavy?"
(Shows you think before designing)
```

---

#### 3. Discuss Trade-offs

**Bad:**
```
"We'll use NoSQL because it's scalable"
(Interviewer thinks: "Doesn't understand trade-offs")
```

**Good:**
```
"We have two options:
1. SQL: Better for transactions, harder to scale
2. NoSQL: Easier to scale, eventual consistency

Given our read-heavy workload and need for scale,
NoSQL makes sense. We can handle eventual consistency
because social media feeds don't need strong consistency."
(Shows you understand pros/cons)
```

---

#### 4. Think Out Loud

**Bad:**
```
*Silent for 2 minutes*
"Here's my design..."
(Interviewer has no idea what you're thinking)
```

**Good:**
```
"I'm thinking about the database choice...
We have structured data, so SQL makes sense.
But we need to scale to millions of users.
We could use sharding or switch to NoSQL.
Let me go with SQL + sharding because..."
(Interviewer follows your thought process)
```

---

#### 5. Handle Unknowns Gracefully

**Bad:**
```
Interviewer: "How would you handle this edge case?"
You: "I don't know"
(Interviewer thinks: "Gives up easily")
```

**Good:**
```
Interviewer: "How would you handle this edge case?"
You: "I'm not familiar with this specific scenario,
but here's how I'd approach it:
1. First, I'd...
2. Then, I'd...
What do you think?"
(Shows problem-solving skills)
```

---

### Practice Questions

#### Easy
1. Design a URL shortener (Bit.ly)
2. Design a pastebin (Pastebin.com)
3. Design a rate limiter
4. Design a key-value store (Redis)
5. Design a parking lot system

#### Medium
6. Design Instagram
7. Design Twitter
8. Design Uber
9. Design Netflix
10. Design WhatsApp
11. Design Dropbox
12. Design Ticketmaster
13. Design a web crawler
14. Design a notification system
15. Design a news feed

#### Hard
16. Design YouTube
17. Design Google Search
18. Design Amazon
19. Design Airbnb
20. Design a distributed cache
21. Design a distributed message queue
22. Design a stock exchange
23. Design a payment system
24. Design a recommendation system
25. Design a multiplayer game backend

---

## Interview Cheat Sheet {#interview-framework}

### Quick Reference

#### Numbers to Remember

**Latency:**
- L1 cache: 1 ns
- L2 cache: 4 ns
- RAM: 100 ns
- SSD: 100 μs
- HDD: 10 ms
- Network (same datacenter): 0.5 ms
- Network (cross-country): 50 ms

**Throughput:**
- Single server: 1K-10K requests/second
- Database: 1K-10K queries/second
- Redis: 100K operations/second
- Kafka: 1M messages/second

**Storage:**
- 1 KB = 1,000 bytes
- 1 MB = 1,000 KB
- 1 GB = 1,000 MB
- 1 TB = 1,000 GB
- 1 PB = 1,000 TB

**Availability:**
- 99% = 3.65 days downtime/year
- 99.9% = 8.76 hours downtime/year
- 99.99% = 52 minutes downtime/year
- 99.999% = 5 minutes downtime/year

---

### Common Patterns

**1. Caching**
- Use for: Frequently accessed data
- Tools: Redis, Memcached
- Strategies: Cache-aside, Write-through

**2. Load Balancing**
- Use for: Distribute traffic
- Algorithms: Round robin, Least connections
- Tools: Nginx, HAProxy, AWS ELB

**3. Database Scaling**
- Replication: Master-slave for reads
- Sharding: Horizontal partitioning
- Indexing: Speed up queries

**4. Asynchronous Processing**
- Use for: Long-running tasks
- Tools: Kafka, RabbitMQ, SQS
- Pattern: Event-driven architecture

**5. CDN**
- Use for: Static content, global distribution
- Tools: CloudFront, Cloudflare, Akamai

**6. Rate Limiting**
- Use for: Prevent abuse
- Algorithms: Token bucket, Leaky bucket
- Tools: Redis, API Gateway

---

### Technology Choices

**Databases:**
- PostgreSQL: General purpose, ACID
- MySQL: Web applications
- MongoDB: Flexible schema, documents
- Cassandra: High write throughput
- Redis: Caching, sessions
- Elasticsearch: Full-text search

**Message Queues:**
- Kafka: High throughput, streaming
- RabbitMQ: Flexible routing
- SQS: AWS managed
- Redis Pub/Sub: Simple, fast

**Storage:**
- S3: Object storage
- EBS: Block storage (databases)
- EFS: File storage (shared)

**Compute:**
- EC2: Virtual machines
- Lambda: Serverless functions
- ECS/EKS: Containers

---

## Final Tips for Success

### 1. Practice, Practice, Practice

**Weekly Plan:**
- Week 1-2: Study fundamentals (this guide)
- Week 3-4: Practice 5 easy questions
- Week 5-6: Practice 10 medium questions
- Week 7-8: Practice 5 hard questions + mock interviews

**How to Practice:**
1. Set timer (45 minutes)
2. Design system on whiteboard/paper
3. Explain out loud (record yourself)
4. Review and improve
5. Compare with online solutions

---

### 2. Build Real Systems

**Projects to Build:**
1. URL shortener (weekend project)
2. Chat application (WebSockets)
3. File storage (S3-like)
4. Social media feed
5. E-commerce site

**Why?**
- Understand real challenges
- Learn from mistakes
- Have concrete examples in interview

---

### 3. Read System Design Blogs

**Must-Read:**
- Engineering blogs: Netflix, Uber, Airbnb, Meta
- System Design Primer (GitHub)
- High Scalability blog
- AWS Architecture blog

---

### 4. Mock Interviews

**Where:**
- Pramp (free)
- Interviewing.io (paid)
- Friends/colleagues

**Why:**
- Get comfortable talking through designs
- Receive feedback
- Identify weak areas

---

### 5. Stay Updated

**Follow:**
- Tech company engineering blogs
- System design YouTube channels
- Hacker News
- Reddit (r/cscareerquestions, r/ExperiencedDevs)

---

## Conclusion

**You've learned:**
- ✅ Fundamentals (scaling, databases, networking)
- ✅ Intermediate (distributed systems, security)
- ✅ Advanced (cloud, ML, real-time processing)
- ✅ Interview framework (RADIO)
- ✅ Real-world examples (YouTube, Uber, WhatsApp)

**Next Steps:**
1. Review this guide 2-3 times
2. Practice 20+ system design questions
3. Build 2-3 real projects
4. Do 5+ mock interviews
5. Ace your interviews! 🚀

**Remember:**
- Start simple, then scale
- Ask clarifying questions
- Discuss trade-offs
- Think out loud
- Be confident!

**Good luck! You've got this! 💪**

---

## Additional Resources

### Books
1. "Designing Data-Intensive Applications" by Martin Kleppmann
2. "System Design Interview" by Alex Xu (Vol 1 & 2)
3. "Database Internals" by Alex Petrov

### Online Courses
1. Grokking the System Design Interview (Educative)
2. System Design for Interviews and Beyond (Exponent)
3. Scalability & System Design (Udemy)

### YouTube Channels
1. System Design Interview
2. Gaurav Sen
3. Tech Dummies Narendra L

### Websites
1. System Design Primer (GitHub)
2. ByteByteGo
3. High Scalability

---

**End of Guide**

*Last Updated: 2024*
*Version: 1.0*
*Author: System Design Expert*
