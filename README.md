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

