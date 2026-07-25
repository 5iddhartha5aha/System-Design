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
