# VIII. Single Point of Failure (SPOF)

A **Single Point of Failure (SPOF)** is any component in a system whose failure causes the **entire system or a critical part of it to stop functioning**.

In other words, if there is **only one instance** of a critical component and it fails, the system becomes unavailable.

---

# Why is SPOF a Problem?

Suppose an application is deployed on a single server.

```text
             Users
               |
               |
        +---------------+
        |   Server      |
        +---------------+
               |
          Database
```

If the server crashes:

```text
             Users
               |
               |
        +---------------+
        |   Server      |
        |   (DOWN)      |
        +---------------+
```

Result:

- ❌ Users cannot access the application.
- ❌ Business operations stop.
- ❌ Downtime occurs.

This server is a **Single Point of Failure**.

---

# Common Examples of SPOF

## 1. Single Application Server

```text
Users

↓

Application Server

↓

Database
```

If the application server fails, the entire application becomes unavailable.

---

## 2. Single Database Server

```text
Users

↓

Application

↓

Database
```

If the database crashes, no data can be read or written.

---

## 3. Single Load Balancer

```text
          Users
             |
     +----------------+
     | Load Balancer  |
     +----------------+
             |
     -----------------
     |       |       |
   Server1 Server2 Server3
```

Even if all servers are healthy, if the load balancer fails, users cannot reach them.

---

## 4. Single Cache Server

```text
Application

↓

Redis

↓

Database
```

If the cache is a critical dependency and it fails, application performance may degrade significantly or requests may fail.

---

## 5. Single Network Connection

```text
Internet

↓

Router

↓

Application
```

If the router or network link fails, the application becomes unreachable.

---

## 6. Single Region

A **region** is a geographically separate location where cloud providers (AWS, Azure, GCP) host their data centers.

Examples:

- US East
- Europe West
- Asia South (Mumbai)
- Asia Southeast (Singapore)

Suppose your application is deployed only in the **Mumbai** region.

```text
             Users
                |
         Mumbai Region
        ----------------
        | Application |
        | Database    |
        ----------------
```

If the Mumbai region experiences:

- Power outage
- Earthquake
- Flood
- Major network failure
- Cloud region outage

the **entire application becomes unavailable**.

Even though there may be multiple servers inside Mumbai, the **region itself is the Single Point of Failure**.

---

# How to Eliminate SPOF

The solution is **redundancy**—having multiple instances of critical components so that if one fails, another can take over.

---

## 1. Multiple Application Servers

Instead of one server:

```text
          Users
             |
      +----------------+
      | Load Balancer  |
      +----------------+
             |
    -----------------------
    |          |          |
 Server1    Server2    Server3
```

If one server fails, the load balancer routes traffic to the remaining healthy servers.

---

## 2. Database Replication (Master-Slave Architecture)

```text
            Application
                  |
             Primary DB
                  |
             Replication
                  |
             Replica DB
```

If the primary database fails:

- The replica is promoted to become the new primary.
- Applications continue to function with minimal downtime.

---

## 3. Multiple Load Balancers

```text
               Users
                  |
          ----------------
          |              |
      LB1 (Active)   LB2 (Standby)
                  |
          --------------------
          |        |         |
       Server1  Server2  Server3
```

If **LB1** fails, **LB2** immediately takes over.

---

## 4. Distributed Cache

Instead of one cache server:

```text
Application

↓

-------------------------

Cache1   Cache2   Cache3
```

Data is distributed across multiple cache nodes.

---

## 5. Multiple Regions

Deploying an application in **multiple regions** protects it from large-scale failures such as natural disasters, power outages, or regional network failures.

Instead of deploying in one region:

```text
                 Users
                    |
           Global Load Balancer
                    |
      ---------------------------------
      |                               |
 Mumbai Region                Singapore Region
 --------------              -----------------
 | App Servers |             | App Servers   |
 | Database    |             | Database      |
 --------------              -----------------
```

If one region fails, traffic is automatically routed to the other region.

---

## How Data Stays Consistent?

Since each region has its own database, data must be synchronized.

There are two common approaches.

### 1. Active-Passive

One region handles all traffic.

The second region acts as a backup.

```text
             Users
                |
      Mumbai (Primary)
                |
        Database Replication
                |
     Singapore (Standby)
```

If Mumbai fails:

```text
Singapore

↓

Becomes Primary
```

Advantages:

- Simpler architecture
- Easier data consistency
- Lower operational complexity

Disadvantages:

- Backup region is mostly idle
- Failover may take a short time

---

### 2. Active-Active

Both regions serve users simultaneously.

```text
                 Users
                    |
          Global Load Balancer
                    |
      ---------------------------------
      |                               |
 Mumbai Region                Singapore Region
      |                               |
      -------- Data Replication -------
```

Advantages:

- Better performance (users connect to the nearest region)
- Higher availability
- Better load distribution

Disadvantages:

- More complex synchronization
- Conflict resolution is required if the same data is updated in multiple regions

---
