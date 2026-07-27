
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
