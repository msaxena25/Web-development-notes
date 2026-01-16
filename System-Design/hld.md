# ✅ Meaning of **System Design**

**System Design** means:

> **Planning and structuring a software system** by deciding
> **how different components (frontend, backend, database, APIs, servers, etc.) will work together**.

---

### 🔹 Simple words (easy to remember)

**System Design = Soch + Planning + Architecture**
---

# Types of System design

## 1️⃣ High-Level Design (HLD)

### 👉 What it focuses on:
- Overall **architecture**
- Major **components**
- How components **communicate**
- Technology choices

### 🔹 Includes:
- Frontend
- Backend services
- Load balancer
- Database
- Cache
- Message queue
- CDN

## 2️⃣ Low-Level Design (LLD)

### 👉 What it focuses on:
- **Internal details**
- Classes & objects
- APIs
- DB tables
- Relationships
- Design patterns

### 🔹 Includes:
- Class diagrams
- API contracts
- Database schema
- Method-level design

---

## 3️⃣ (Bonus) Detailed Design / Micro Design  
Some companies split LLD further:
- Error handling
- Validation
- Concurrency
- Transactions
- Edge cases

---

## Easy way to remember 🧠
| Type | Think Like |
|----|----|
| HLD | **Architect** 🏗️ |
| LLD | **Developer** 💻 |

---

## 🔷 What is High-Level Design (HLD)?

**HLD** explains:

> **What components exist in the system and how they talk to each other**,
> without going into class-level or code-level details.

Think like a **software architect**, not a coder.

---

## 🔷 HLD – Step-by-Step Approach (INTERVIEW GOLD ⭐)

### 1️⃣ Clarify Requirements (ALWAYS FIRST)

Split into:

#### ✅ Functional Requirements

What the system **must do**

* User can register/login
* User can place order
* User can search products

#### ✅ Non-Functional Requirements

How well the system should work:

* Scalability (millions of users)
* High availability
* Low latency
* Security
* Consistency

📌 **Never skip this step in interviews**

---

### 2️⃣ Identify Major Components

Almost every scalable system has:

```
Client (Web / Mobile)
      ↓
Load Balancer
      ↓
API Servers
      ↓
Cache (Redis)
      ↓
Database
      ↓
Message Queue
```

---

### 3️⃣ Request Flow (Explain Clearly)

Example: **User places an order**

1. User sends request from app
2. Load Balancer distributes traffic
3. API Server validates request
4. Cache checked first
5. DB updated
6. Event sent to queue (email, notification)

💡 Interviewers love **clear flow explanation**

---

### 4️⃣ Scalability Design (VERY IMPORTANT)

#### Horizontal Scaling

* Add more servers
* Stateless APIs

#### Vertical Scaling

* Bigger machine
* Limited scaling

👉 **Preferred: Horizontal**

---

### 5️⃣ Database Design (HLD Level)

* SQL → transactions (orders, payments)
* NoSQL → large scale reads (products, logs)

#### Techniques:

* Read replicas
* Sharding
* Indexing

---

### 6️⃣ Caching Strategy

Why cache?

* Reduce DB load
* Faster response

Where cache?

* User sessions
* Product details
* Search results

Types:

* Read-through
* Write-through
* Write-behind

---

### 7️⃣ Load Balancer

Distributes traffic:

* Round Robin
* Least connections

Also:

* Health checks
* Failover

---

### 8️⃣ Message Queue (Async Processing)

Used for:

* Emails
* Notifications
* Payment processing
* Logging

Examples:

* Kafka
* RabbitMQ
* SQS

💡 Helps in **decoupling services**

---

### 9️⃣ Fault Tolerance & Reliability

* Retry mechanisms
* Circuit breakers
* Timeouts
* Replication
* Failover

---

### 🔷 Typical HLD Diagram (Text View)

```
Mobile/Web App
      |
   Load Balancer
      |
  API Gateway
      |
  Microservices
   |      |
 Cache   Queue
   |      |
 Database |
          |
     Notification Service
```

---

## 1️⃣ Read Replica

A **Read Replica** is a **copy of the main (primary) database** that is used **only for read operations**.

### Why it is used

* When **read traffic is very high**
* To reduce load on the **primary database**
* To improve **read performance**

### How it works

* All **write operations** (INSERT, UPDATE, DELETE) go to **Primary DB**
* **Read operations** (SELECT) are routed to **Read Replicas**

### Benefits

* Better performance
* High availability
* Easy horizontal scaling

### Drawback

* Data can be **slightly stale** (replication lag)

---

## 2️⃣ Sharding

**Sharding** means **splitting data across multiple databases** instead of keeping all data in one database.

Each shard contains **only a portion of data**.

### Why it is used

* When database size becomes very large
* When one database cannot handle traffic
* To scale **both read and write**

### How it works

Data is divided using a **shard key**, for example:

* User ID
* Order ID
* Region

```
Shard 1 → Users 1–1M
Shard 2 → Users 1M–2M
Shard 3 → Users 2M–3M
```

### Benefits

* Massive scalability
* Better performance
* Distributed load

### Drawbacks

* Complex queries (joins across shards)
* Re-sharding is difficult
* More operational complexity

---

## 3️⃣ Indexing

**Indexing** is a data structure (like a **table of contents**) that helps the database **find data faster**.

### Why it is used

* To speed up **SELECT queries**
* To avoid full table scans

### How it works

Without index:

```
Search → Scan every row ❌
```

With index:

```
Search → Direct lookup ✔️
```

Example:

```sql
CREATE INDEX idx_email ON users(email);
```

### Benefits

* Faster query execution
* Improved read performance

### Drawbacks

* Slower write operations
* Extra storage needed

---

##  Types of Sharding

There are **4 main types of sharding** used in real systems.

---

### 🔹 1. Horizontal Sharding (Most Common ⭐)

Rows are divided across multiple databases.

Each shard has the **same schema** but **different rows**.

#### Example

```
Shard 1 → User IDs 1 – 1,000,000
Shard 2 → User IDs 1,000,001 – 2,000,000
Shard 3 → User IDs 2,000,001 – 3,000,000
```

#### Used when

* Large number of users
* High read & write traffic

#### Pros

* Scales very well
* Most practical approach

#### Cons

* Cross-shard queries are hard

---

### 🔹 2. Vertical Sharding

Columns are split across different databases.

#### Example

```
User_Core_DB  → userId, name, email
User_Extra_DB → address, preferences, logs
```

#### Used when

* Some columns are accessed frequently
* Some data is rarely used

#### Pros

* Smaller tables
* Faster queries for core data

#### Cons

* Multiple DB calls needed
* Joins across databases

---

### 🔹 3. Hash-Based Sharding

A **hash function** decides which shard the data goes to.

#### Example

```
shardId = hash(userId) % number_of_shards
```

User with ID 101 → hash → Shard 2

#### Pros

* Very even data distribution
* No hot shards

#### Cons

* Adding/removing shards is hard
* Re-hashing needed

---

### 🔹 4. Range-Based Sharding

Data is divided based on **value ranges**.

#### Example

```
Shard 1 → Orders from Jan–Mar
Shard 2 → Orders from Apr–Jun
Shard 3 → Orders from Jul–Sep
```

#### Pros

* Simple
* Easy range queries

#### Cons

* Uneven load (hot shards)

---

## How Sharding Actually Works (Step-by-Step)

Let’s take **User Service** example.

---

### Step 1: Choose Shard Key

A field used to decide shard.
Examples:

* userId (best)
* orderId
* region

👉 Shard key must be:

* Present in most queries
* Evenly distributed

---

### Step 2: Shard Router / Lookup Layer

A **routing layer** decides Which shard should handle this request based on shard techniques.

```
Request → Shard Router → Correct DB Shard
```

---


Great 👍
This is a **must-know HLD topic**. I’ll explain **Scaling**, **its types**, and **when scaling is required** — **clearly, practically, and interview-ready**.

---

## 🔷 What is Scaling?

**Scaling** means:

> **Increasing a system’s capacity to handle more load**
> (users, requests, data, traffic) **without breaking performance**.

In simple terms:
➡️ *More users + more data + more requests = need scaling*

---

## 🔷 Why Scaling is Needed?

Scaling is required when:

* System becomes **slow**
* Requests start **failing**
* CPU / memory / DB is overloaded
* Traffic grows beyond current capacity

---

## 🔷 Types of Scaling (Very Important ⭐)

There are **TWO main types of scaling**.

---

## 1️⃣ Vertical Scaling (Scale Up)

### Meaning

Increase the **power of a single machine**.

Examples:

* 8GB RAM → 32GB RAM
* 4 CPU → 16 CPU
* Bigger DB instance

### Diagram

```
Before:  Small Server
After:   Bigger Server
```

### Pros

* Simple to implement
* No code changes
* Works well initially

### Cons

* Hardware limit
* Expensive
* Single point of failure

### Used When

* Early stage system
* Low to medium traffic
* Quick fix

---

## 2️⃣ Horizontal Scaling (Scale Out) ⭐ (Preferred)

### Meaning

Add **more machines/servers** instead of making one bigger.

Examples:

* 1 server → 10 servers
* Multiple DB shards
* Multiple API instances

### Diagram

```
User
 ↓
Load Balancer
 ↓
Server 1
Server 2
Server 3
```

### Pros

* High scalability
* Fault tolerant
* No single point of failure

### Cons

* More complex
* Requires stateless services
* Needs load balancing

### Used When

* High traffic
* Millions of users
* Enterprise systems

---

## 🔷 Scaling Types by Layer (INTERVIEW BONUS)

### 🔹 1. Application Scaling

* Add more API servers
* Stateless services
* Load balancer

### 🔹 2. Database Scaling

* Read replicas
* Sharding
* Partitioning

### 🔹 3. Cache Scaling

* Redis cluster
* Distributed cache

### 🔹 4. Storage Scaling

* Distributed storage (S3, HDFS)

---

## 🔷 When EXACTLY to Scale? (VERY IMPORTANT)

### Scale when you see:

1️⃣ High latency
2️⃣ High CPU / memory usage
3️⃣ DB connections exhausted
4️⃣ Timeouts / errors
5️⃣ Traffic growth trends
6️⃣ SLA violations

---

## 🔷 Real Example (Easy to Remember)

### Problem:

E-commerce site slow during sale.

### Solution:

1. Cache product data
2. Add read replicas
3. Scale API servers horizontally
4. Shard orders DB if needed

---

## ✅ What is a **Stateless Service**?

A **stateless service** is a service that **does NOT store any client/session data in its own memory** between requests.

👉 **Each request is independent** and contains **all the information needed** to process it.

> A stateless service does not remember previous requests; every request is handled as a new one.

---

## 🔹 Example (Stateless API)

```http
GET /orders
Headers:
Authorization: Bearer <JWT_TOKEN>
```

* The server does **not remember the user**
* Authentication info comes with **every request**
* Any server can handle the request. ( Each server does NOT have its own database,
but YES ✅ each server has the same application (API) code.)

---

## 🔹 Why Stateless Services are Important?

Because they allow **horizontal scaling**.

➡️ Any server can handle any request
➡️ No dependency on previous requests

---

## 🔹 How Statelessness is Achieved

### 1️⃣ No Session Stored in Server Memory ❌

Bad:

```text
Server stores user session in RAM
```

### 2️⃣ Use Tokens (JWT / OAuth) ✅

* Client sends token every time
* Server validates token

### 3️⃣ External State Storage ✅

If state is needed, store it in:

* Redis
* Database
* Distributed cache

---

## 🔹 Stateful Service Example

* Shopping cart stored in server memory
* User must always hit **same server**
* Requires **sticky sessions**

---

## 🔹 Stateless Service Example

* REST APIs
* Microservices
* Authentication using JWT

---

## Load Balancer - 

A load balancer continuously monitors backend health and deterministically routes each incoming request to a healthy server using a configured algorithm.

Load Balancer Machine Actually Has Two “sides” of networking

Front side (Client-facing):

Public IP, Receives internet traffic, Terminates TCP / TLS (HTTPS)

Back side (Server-facing):

Private IPs of backend servers, Talks to them inside VPC / data center

### But Isn’t LB a Single Point of Failure?

In production, LB itself is replicated.

--------------

## What is a Message Queue?

A Message Queue (MQ) is:

A buffer that stores messages so that one service can send work to another service asynchronously.

In simple words:
➡️ “Do this task later, not now.”

Without MQ:

Services call each other synchronously. If one service is slow or down → whole flow breaks. User waits longer.

With MQ: Sender and receiver are decoupled, System becomes resilient & scalable

**Think of Kafka as:**

A set of servers that store ordered files (logs) and allow many systems to read those files independently.
Producers append events and consumer groups read them independently.

-----------------------------

## 🔁 What Is the Saga Pattern?

The Saga pattern manages distributed transactions by executing a sequence of local transactions and using compensating actions to handle failures, avoiding the need for distributed locks and enabling scalability in microservices architectures.

---

## ❌ Why Traditional Transactions Don’t Work

### Problem with 2-Phase Commit (2PC):

* Locks multiple services
* Poor performance
* Not scalable
* Single point of failure

👉 **Modern microservices avoid 2PC**

---

## ✅ Why Saga Pattern Works

* No global lock
* Each service controls its own DB
* Failure is handled by **compensation**, not rollback
* Asynchronous & scalable

---

## 🧩 Core Concepts of Saga

1️⃣ Local transaction
2️⃣ Event/message
3️⃣ Next step
4️⃣ Compensating transaction

---

## 📦 Example: Food Delivery Order Saga

### Services Involved:

* Order Service
* Payment Service
* Delivery Service

---

### Step-by-Step Saga Flow

#### 1️⃣ Order Created

* Order Service saves order
* Status = `PENDING_PAYMENT`
* Emits event

```
ORDER_CREATED
```

---

#### 2️⃣ Payment Processed

* Payment Service processes payment

**Success**

```
PAYMENT_SUCCESS
```

**Failure**

```
PAYMENT_FAILED
```

---

#### 3️⃣ Delivery Assigned

* Delivery Service assigns rider

**Failure here?**
👉 Trigger **refund** (compensation)

---

## 🔄 Compensating Transactions (Key Idea)

| Step            | Action       | Compensation   |
| --------------- | ------------ | -------------- |
| Create Order    | Save order   | Cancel order   |
| Process Payment | Deduct money | Refund         |
| Assign Delivery | Assign rider | Unassign rider |

✔ No rollback
✔ Forward recovery

---

## 🏗️ Two Types of Saga Patterns

## 1️⃣ Choreography-Based Saga

### How It Works:

* No central coordinator
* Services react to events

```
Order → Payment → Delivery
```

### Pros:

* Simple
* Loosely coupled

### Cons:

* Hard to track flow
* Debugging is difficult

---

## 2️⃣ Orchestration-Based Saga ⭐⭐⭐

### How It Works:

* Central **Saga Orchestrator**
* Controls flow

```
Orchestrator → Payment → Delivery
```

---

## ❓ How Do You Ensure No Duplicate Orders Are Created?

Duplicate orders usually happen because of:

* Network retries
* Client resubmitting request
* Timeout but backend already processed
* User clicking “Place Order” multiple times

So the system must be **idempotent**.

---

## 1️⃣ Core Concept: Idempotency ⭐⭐⭐

> An operation is idempotent if **executing it multiple times gives the same result**.

In simple words:
➡️ Same request → **only one order created**

---

## 2️⃣ Idempotency Key (Main Solution)

### How It Works:

* Client generates a **unique idempotency key**
* Sends it with order request

```
POST /orders
Headers:
Idempotency-Key: abc123
```

---

### Backend Logic:

1. Check if this key already exists
2. If YES → return existing order
3. If NO → create new order

---

## 3️⃣ Database-Level Protection (VERY IMPORTANT)

Even if application fails, DB should protect.

### Unique Constraints

```sql
UNIQUE (idempotency_key)
or
UNIQUE (user_id, cart_hash)
```

✔ Last line of defense
✔ Prevents race conditions

---