# System-Design
# I. Horizontal vs Vertical Scaling in System Design

In system design, **scaling** is the process of increasing a system's capacity to handle more users, requests, or data. There are two primary approaches:

## 1. Vertical Scaling (Scale Up)

Vertical scaling means **adding more resources to a single server**.

### Example

Upgrade a server from:

- 8 CPU cores → 32 CPU cores
- 16 GB RAM → 128 GB RAM
- 500 GB SSD → 2 TB SSD

### Architecture

```text
Before:
+----------------+
|    Server      |
|----------------|
| 8 CPU          |
| 16 GB RAM      |
+----------------+

        ↓ Upgrade

+----------------+
| Bigger Server  |
|----------------|
| 32 CPU         |
| 128 GB RAM     |
+----------------+
```

### Advantages

- ✅ Simple to implement
- ✅ No application changes required in many cases
- ✅ Easier database management

### Disadvantages

- ❌ Hardware has limits (can't keep upgrading forever)
- ❌ Single point of failure
- ❌ More expensive at higher capacities
- ❌ Downtime may be required during upgrades

### Real-world Example

A small startup running its application and database on one server upgrades to a more powerful machine as traffic grows.

---

## 2. Horizontal Scaling (Scale Out)

Horizontal scaling means **adding more servers and distributing traffic among them**.

### Example

Instead of one server handling 10,000 requests/sec:

```text
                  +----------------+
                  | Load Balancer  |
                  +----------------+
                          |
        ---------------------------------------
        |                 |                  |
+---------------+ +---------------+ +---------------+
|   Server 1    | |   Server 2    | |   Server 3    |
+---------------+ +---------------+ +---------------+
```

Each server handles part of the traffic.

### Advantages

- ✅ Virtually unlimited growth
- ✅ High availability and fault tolerance
- ✅ Better reliability
- ✅ Supports massive traffic loads

### Disadvantages

- ❌ More complex architecture
- ❌ Requires load balancing
- ❌ Data consistency becomes challenging
- ❌ Distributed systems introduce network latency and synchronization issues

### Real-world Example

Large companies like Google, Netflix, and Amazon use thousands of servers behind load balancers to handle millions of users.

---

## Comparison

| Aspect | Vertical Scaling | Horizontal Scaling |
|----------|----------------|------------------|
| **Method** | Bigger server | More servers |
| **Cost** | Cheaper initially | Higher setup cost |
| **Complexity** | Low | High |
| **Fault Tolerance** | Low | High |
| **Scalability Limit** | Hardware limit | Nearly unlimited |
| **Downtime** | Often required | Usually minimal |
| **Best For** | Small to medium systems | Large-scale systems |

---

# II. Load Balancing

Load balancing is the process of **distributing incoming network traffic across multiple servers** to ensure that no single server becomes overloaded. It improves **performance, scalability, availability, and fault tolerance**.

---

## Why Do We Need Load Balancing?

Imagine a website receiving 1 million requests per day.

Without a load balancer:

```text
                Users
                  |
                  |
          +---------------+
          |   Server 1    |
          +---------------+

All requests go to a single server.
```

Problems:
- ❌ Server overload
- ❌ Slow response times
- ❌ Single point of failure
- ❌ Limited scalability

---

With a load balancer:

```text
                    Users
                      |
              +----------------+
              | Load Balancer  |
              +----------------+
                     |
        --------------------------------
        |              |              |
+---------------+ +---------------+ +---------------+
|   Server 1    | |   Server 2    | |   Server 3    |
+---------------+ +---------------+ +---------------+
```

The load balancer distributes requests evenly across all servers.

---

# Benefits of Load Balancing

- ✅ Improves application performance
- ✅ Prevents server overload
- ✅ Increases availability
- ✅ Enables horizontal scaling
- ✅ Improves fault tolerance
- ✅ Reduces downtime
- ✅ Better user experience

---

# Load Balancing Algorithms

**Round Robin** is one of the load balancing algorithms which distributes requests sequentially.

```text
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
Request 5 → Server 2
....
```

### Advantages

- Simple
- Fair when all servers are identical

---

# III. Consistent Hashing in System Design

Consistent Hashing is a technique used to **distribute data across multiple servers** while **minimizing data movement when servers are added or removed**. It is widely used in **distributed databases, caching systems, and distributed storage**.

---

# Why Do We Need Consistent Hashing?

Suppose we have **3 servers**.

### Traditional Hashing

A common approach is:

```text
Server = Hash(Key) % Number_of_Servers
```

Example:

```text
Number of Servers = 3

Hash(User123) = 27

27 % 3 = 0

→ Store on Server 0
```

Now suppose we add another server.

```text
Number of Servers = 4

27 % 4 = 3
```

Location changes.

Another key:

```text
Hash(User456) = 26

Before:
26 % 3 = 2

After:
26 % 4 = 2
```

Still same.

Another key:

```text
Hash(User789) = 29

Before:
29 % 3 = 2

After:
29 % 4 = 1
```

Its location changes.

In practice, **adding or removing one server changes the destination of most keys**, forcing massive data migration.

Problems:

- ❌ Large-scale data movement
- ❌ Cache misses
- ❌ High network traffic
- ❌ Increased downtime

---

# Idea Behind Consistent Hashing

Instead of using `% Number_of_Servers`, both **servers and keys are placed on a circular hash ring**.

<img width="527" height="390" alt="image" src="https://github.com/user-attachments/assets/6a2fa0a0-7184-48f4-b6c2-46f6cad41587" />

```text
                 0
                / \
               /   \
        S1            S2
       /                \
      /                  \
    75                    25
      \                  /
       \                /
        S3            Keys
             \      /
                50
```

Every server and key is assigned a position on the ring using a hash function.

---

# How It Works

Suppose:

```text
Server A → Hash = 20

Server B → Hash = 60

Server C → Hash = 90
```

```text
                0/100
                   |
                   |
              Server C (90)
                 /      \
                /        \
               /          \
 Server A (20)            Server B (60)
```

Now insert a key.

```text
Key = User123

Hash(User123) = 45
```

Starting from 45 and moving **clockwise**, the first server encountered is:

```text
Server B (60)
```

Therefore:

```text
User123 → Server B
```

---

# Another Example

```text
Server A = 20

Server B = 60

Server C = 90
```

Keys:

```text
Key1 = 10

Key2 = 35

Key3 = 80

Key4 = 95
```

Assignments:

```text
Key1 (10) → Server A (20)

Key2 (35) → Server B (60)

Key3 (80) → Server C (90)

Key4 (95) → Server A (20)
```

Notice that after reaching 90, the ring wraps back to 0.

---

# What Happens When a New Server Is Added?

Current ring:

```text
A = 20

B = 60

C = 90
```

Add:

```text
D = 40
```

```text
                0
                 |
                 |
            C (90)
          /        \
         /          \
A (20) -- D (40) -- B (60)
```

Only the keys between:

```text
20 → 40
```

move from **Server B** to **Server D**.

Everything else stays exactly where it is.

This is the key advantage of consistent hashing.

---

# What Happens When a Server Is Removed?

Suppose:

```text
Server B fails.
```

```text
Before

20 ---- 60 ---- 90

 A       B       C
```

After removal:

```text
20 ----------- 90

 A             C
```

Only the keys belonging to **Server B** move to **Server C**.

No other keys are affected.

---

# Virtual Nodes (VNodes)

One problem with basic consistent hashing is **uneven data distribution**.

<img width="516" height="397" alt="image" src="https://github.com/user-attachments/assets/30c14a41-54ea-4936-808b-ddb9dcfdd383" />

Example:

```text
Server A = 10

Server B = 20

Server C = 90
```

```text
Ring

10-----20-----------------------------90
 A      B                              C
```

Server C stores much more data than A or B.

---

## Solution: Virtual Nodes

Instead of placing one point per server, create multiple virtual nodes.

A virtual node (vnode) is not a copy of a server. It is a logical partition (or logical position) of a physical server on the hash ring.

Think of it like this:

Physical server = the actual machine (or VM/container) that stores data.
Virtual node = one of multiple logical identities that the same physical server has on the hash ring.

<img width="446" height="357" alt="image" src="https://github.com/user-attachments/assets/cd74b33f-b64e-4822-b441-a64e2cf7b56b" />

Example:

```text
Server A

A1 = 10

A2 = 35

A3 = 75
```

```text
Server B

B1 = 20

B2 = 55

B3 = 95
```

```text
Server C

C1 = 5

C2 = 45

C3 = 85
```

Now the ring becomes:

```text
5   10   20   35   45   55   75   85   95

C1--A1--B1--A2--C2--B2--A3--C3--B3
```

Benefits:

- Better load balancing
- More even data distribution
- Smoother scaling
- Reduced hotspots

Most modern systems use virtual nodes.

---

# Advantages

- ✅ Minimal data movement
- ✅ Easy to scale horizontally
- ✅ High availability
- ✅ Better fault tolerance
- ✅ Even data distribution (with virtual nodes)
- ✅ Efficient cache management

---

# Disadvantages

- ❌ More complex than simple hashing
- ❌ Requires virtual nodes for good balance
- ❌ Slightly higher implementation complexity

---

# Real-World Applications

Consistent hashing is commonly used in:

- Distributed caches (Redis Cluster, Memcached)
- Distributed databases (Apache Cassandra, DynamoDB)
- Distributed storage systems
- Content Delivery Networks (CDNs)
- Distributed key-value stores

---

# Comparison: Traditional vs Consistent Hashing

| Feature | Traditional Hashing | Consistent Hashing |
|----------|---------------------|--------------------|
| Formula | `Hash(Key) % N` | Hash Ring |
| Adding Server | Most keys move | Only a small fraction move |
| Removing Server | Most keys move | Only affected keys move |
| Scalability | Poor | Excellent |
| Cache Efficiency | Low | High |
| Used In | Small systems | Large distributed systems |

---

# IV. Monolithic vs Microservices Architecture

A **software architecture** defines how an application is structured and how its components interact. The two most common architectures are **Monolithic** and **Microservices**.

---

# 1. Monolithic Architecture

A **monolithic application** is one where **all components of the application are built, deployed, and run as a single unit**.

For example, an e-commerce application may contain:

- User Authentication
- Product Catalog
- Shopping Cart
- Payment Service
- Order Management

All of these are part of one application.

## Architecture

```text
                Users
                  |
                  |
          +------------------+
          |  Monolithic App  |
          |------------------|
          | Authentication   |
          | Product Catalog  |
          | Shopping Cart    |
          | Payment          |
          | Orders           |
          +------------------+
                  |
             Database
```

Whenever one module changes, the **entire application must be rebuilt and redeployed**.

---

# Advantages

- ✅ Simple to develop
- ✅ Easy to deploy initially
- ✅ Easy debugging
- ✅ Faster communication between modules (function calls)
- ✅ Good for small applications

---

# Disadvantages

- ❌ Large codebase becomes difficult to maintain
- ❌ Entire application must be redeployed for small changes
- ❌ Scaling requires scaling the whole application
- ❌ Single point of failure
- ❌ Newer interns/employees need context of the whole application rather than a particular unit

---

# Example

Suppose your shopping website receives heavy traffic only on the **Product Catalog**.

With a monolith:

```text
Need more Product capacity

↓

Scale Entire Application

+----------------------+
| Authentication       |
| Product Catalog      |
| Cart                 |
| Payment              |
| Orders               |
+----------------------+
```

Even though only the Product Catalog is busy, every module is scaled.

---

# 2. Microservices Architecture

A **microservices architecture** breaks an application into **small, independent services**, where each service is responsible for a single business capability.

Each service:

- Has its own codebase
- Can be deployed independently
- Can be scaled independently
- Often owns its own database

---

## Architecture

```text
                     Users
                       |
                +---------------+
                | API Gateway   |
                +---------------+
                       |
 -----------------------------------------------------
 |          |            |           |               |
 |          |            |           |               |
Auth     Product      Cart       Payment        Orders
Service   Service     Service     Service       Service
 |          |            |           |               |
 DB         DB           DB          DB              DB
```

Each service communicates with others using APIs or messaging systems.

---

# Advantages

- ✅ Independent deployment
- ✅ Independent scaling
- ✅ Better fault isolation
- ✅ Easier maintenance
- ✅ Teams can work independently
- ✅ Different services can use different technologies

---

# Disadvantages

- ❌ More complex architecture
- ❌ Network latency between services
- ❌ Harder debugging
- ❌ Distributed data consistency challenges
- ❌ More infrastructure (API Gateway, service discovery, monitoring)

---


# Fault Isolation

### Monolith

```text
Payment Module Crash

↓

Entire Application May Crash
```

### Microservices

```text
Payment Service Crash

↓

Authentication ✓

Products ✓

Orders ✓

Only Payment is affected
```

---

# Database Comparison

### Monolithic

```text
            Application
                 |
          ----------------
          |              |
       Shared Database
```

Every module shares the same database.

---

### Microservices

```text
Auth Service -------- Auth DB

Product Service ----- Product DB

Order Service ------- Order DB

Payment Service ----- Payment DB
```

Each service owns its own data.

---

# Communication

### Monolithic

```text
Authentication

↓

Function Call

↓

Payment
```

Modules communicate directly through in-memory function calls.

---

### Microservices

```text
Authentication

↓

REST / gRPC / Message Queue

↓

Payment Service
```

Communication happens over the network.

---

# When to Use Monolithic Architecture

Choose a monolith when:

- Small applications
- Startups or MVPs
- Small development teams
- Simple deployment requirements
- Limited infrastructure

---

# When to Use Microservices

Choose microservices when:

- Large-scale applications
- Many development teams
- High scalability requirements
- Frequent deployments
- Independent feature development
- High availability is important

---

# Comparison

| Feature | Monolithic | Microservices |
|----------|------------|---------------|
| Architecture | Single application | Multiple independent services |
| Deployment | Entire application | Individual services |
| Scaling | Whole application | Individual services |
| Database | Usually shared | Usually one database per service |
| Communication | Function calls | REST, gRPC, Messaging |
| Fault Isolation | Low | High |
| Development | Easier initially | More complex |
| Maintenance | Harder as app grows | Easier for large teams |
| Technology Stack | Usually one | Can differ by service |
| Best For | Small/medium applications | Large distributed systems |

---

# V. Database Sharding

**Database Sharding** is a technique of **horizontally partitioning a database** into multiple smaller databases called **shards**. Each shard stores **a subset of the total data**, allowing the database to scale horizontally.

Instead of storing all data on a single database server, the data is distributed across multiple servers.

---

## Why Do We Need Sharding?

Suppose an application has **500 million users**.

Without sharding:

```text
              Application
                    |
          +------------------+
          |   Database       |
          +------------------+
```

Problems:

- ❌ Database becomes too large
- ❌ Slow queries
- ❌ High CPU and memory usage
- ❌ Storage limits
- ❌ Difficult to scale

Adding more RAM or CPU (vertical scaling) eventually reaches a hardware limit.

---

## With Sharding

Instead of one database:

```text
                    Application
                          |
        -------------------------------------
        |               |                  |
   +-----------+   +-----------+    +-----------+
   | Shard 1   |   | Shard 2   |    | Shard 3   |
   +-----------+   +-----------+    +-----------+
```

Each shard stores **only part of the data**, reducing the load on individual databases.

---

## How Sharding Works

A **Shard Key** determines where each row is stored.

Example:

```text
User ID
```

Suppose:

```text
Shard 1 → User IDs 1–1,000,000

Shard 2 → User IDs 1,000,001–2,000,000

Shard 3 → User IDs 2,000,001–3,000,000
```

When a request comes:

```text
User ID = 1,500,000

↓

Query Shard 2
```

Only one shard is accessed.

---

## Example

Without sharding:

```text
Database

---------------------------------

User 1

User 2

User 3

...

User 10,000,000
```

With sharding:

```text
Shard 1

User 1

User 2

...

User 3,000,000
```

```text
Shard 2

User 3,000,001

...

User 6,000,000
```

```text
Shard 3

User 6,000,001

...

User 10,000,000
```

Each database handles fewer records.

---

## Types of Sharding

### 1. Range-Based Sharding

Data is divided based on a range of values.

Example:

```text
User ID

1 - 1000

↓

Shard 1
```

```text
1001 - 2000

↓

Shard 2
```

Advantages:

- Simple
- Easy to understand

Disadvantages:

- Uneven traffic if one range is more popular

---

### 2. Hash-Based Sharding

A hash function decides the shard.

```text
Shard = Hash(UserID) % Number_of_Shards
```

Example:

```text
User ID = 101

101 % 3 = 2

↓

Shard 2
```

Advantages:

- Even distribution
- Good load balancing

Disadvantages:

- Adding/removing shards requires redistributing data
- Often combined with **consistent hashing** to reduce data movement

---

### 3. Geographic (Location-Based) Sharding

Data is divided by region.

Example:

```text
India Users

↓

India Database
```

```text
Europe Users

↓

Europe Database
```

```text
USA Users

↓

USA Database
```

Advantages:

- Lower latency
- Helps meet data residency regulations

Disadvantages:

- Difficult if users move between regions

---

## Sharding vs Partitioning

Many people confuse these terms.

### Partitioning

A single database is divided into multiple partitions.

```text
          One Database

      ---------------------
      | P1 | P2 | P3 | P4 |
      ---------------------
```

Still one database server.

---

### Sharding

Data is split across **multiple database servers**.

```text
Shard 1

Server 1
```

```text
Shard 2

Server 2
```

```text
Shard 3

Server 3
```

Each shard runs on a different server.

---

## Advantages

- ✅ Horizontal scalability
- ✅ Faster queries
- ✅ Better write performance
- ✅ Larger storage capacity
- ✅ Reduced load per database
- ✅ High availability (when combined with replication)

---

## Disadvantages

- ❌ More complex architecture
- ❌ Cross-shard joins are difficult
- ❌ Cross-shard transactions are expensive
- ❌ Data rebalancing when adding shards
- ❌ Choosing a good shard key is challenging

---

## Choosing a Good Shard Key

A good shard key should:

- Distribute data evenly
- Avoid hotspots
- Be frequently used in queries
- Be stable (should not change often)

Examples:

- User ID
- Customer ID
- Order ID

Bad examples:

- Country (if one country has most users)
- Gender
- Boolean values (only two possible values)

---

## Problems with Database Sharding

While sharding improves scalability and write performance, it also introduces several challenges. Three of the most important ones are:

---

## 1. Cross-Shard Joins

### Problem

In a traditional database, all related tables are stored on the same database server.

Example:

```sql
Users
Orders
Products
```

A query like:

```sql
SELECT *
FROM Users u
JOIN Orders o
ON u.user_id = o.user_id;
```

can be executed by a single database.

---

### After Sharding

Suppose data is sharded by **User ID**.

```text
                 Application
                      |
      --------------------------------
      |              |               |
   Shard 1        Shard 2        Shard 3

Users 1-1000     Users 1001-2000    Users 2001-3000
Orders           Orders             Orders
```

Now suppose the query needs data from multiple shards.

Example:

```text
Find all orders placed by users in India.
```

Some Indian users may exist in:

- Shard 1
- Shard 2
- Shard 3

The database cannot simply execute one JOIN.

Instead, the application must:

1. Query every shard.
2. Collect the results.
3. Merge (join) them in the application layer.

```text
          Application

      Query Shard 1
            |

      Query Shard 2
            |

      Query Shard 3
            |

      Merge Results

            |

     Return Response
```

This increases:

- Network calls
- Query latency
- Application complexity

---

### Possible Solutions

- Choose a good shard key so related data stays together.
- Denormalize data (duplicate frequently accessed data).
- Perform joins in the application layer.
- Use distributed SQL databases (e.g., Google Spanner, CockroachDB) that support distributed joins.

---

## 2. Fixed Number of Shards

### Problem

Suppose you initially create **4 shards**.

```text
UserID % 4

↓

Shard 0

Shard 1

Shard 2

Shard 3
```

Now the application grows, and you need **8 shards**.

The formula changes:

```text
UserID % 8
```

Unfortunately, almost every key now maps to a different shard.

Example:

```text
User ID = 15

Old:

15 % 4 = 3

↓

Shard 3
```

After adding shards:

```text
User ID = 15

New:

15 % 8 = 7

↓

Shard 7
```

Millions of records need to be moved.

This process is called **resharding**, and it can be expensive and disruptive.

---

## Solution: Hierarchical Sharding (Directory-Based Sharding)

We can instead create further shards of a particular shard and reroute requests to the particular sub-shards.

---

# 3. What If a Shard Fails?

## Problem

Suppose Shard 2 crashes.

```text
Application
      |
------------------------
|        |             |
S1      S2 (DOWN)     S3
```

All data stored in Shard 2 becomes unavailable.

Users whose data resides there cannot access their information.

This creates a single point of failure for that shard.

---

## Solution: Master-Slave (Primary-Replica) Architecture

Each shard is replicated.

```text
             Application
                   |
      -----------------------------
      |            |             |
   Shard 1      Shard 2       Shard 3

      |            |             |

 Primary      Primary       Primary

      |            |             |

 Replica      Replica       Replica
```

The **Primary (Master)** handles all write requests.

The **Replica (Slave)** continuously copies data from the Primary.

---

### Normal Operation

```text
Write Request

↓

Primary

↓

Replicate

↓

Replica
```

Read requests can often be served by replicas, improving read scalability.

---

### Failure Scenario

Suppose:

```text
Primary (Shard 2)

↓

Fails
```

Before:

```text
Primary

↓

Replica
```

After automatic failover:

```text
Replica

↓

Promoted to Primary
```

The application starts sending requests to the promoted replica.

```text
Application

↓

New Primary
```

This process is called **Failover**.

---


