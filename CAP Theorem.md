# CAP Theorem

The **CAP Theorem** states that a distributed system can guarantee at most **two out of three** properties simultaneously:

1. **Consistency (C)**
2. **Availability (A)**
3. **Partition Tolerance (P)**

---

# 1. Consistency (C)

**Every read receives the most recent write or an error.**

All nodes in the distributed system see the **same data at the same time**.

Example:

```text
Write:

User Balance = $100
        |
        v
   +---------+
   | Database|
   +---------+

Immediately after the write:

Node 1 → $100
Node 2 → $100
Node 3 → $100
```

If a user reads from any node, they get the latest value.

---

# 2. Availability (A)

**Every request receives a response**, even if that response may not contain the latest data.

The system should remain operational even when some nodes fail.

Example:

```text
          Request
             |
      ----------------
      |              |
   Node 1          Node 2
   (DOWN)         (ALIVE)
                      |
                   Response
```

Even though Node 1 is unavailable, Node 2 responds to the request.

---

# 3. Partition Tolerance (P)

**The system continues operating even when communication between nodes is lost.**

A network partition occurs when nodes cannot communicate with each other.

Example:

```text
       Node 1                Node 2
         |                      |
         |       Network        |
         X------ Partition -----X
```

Node 1 and Node 2 cannot communicate, but the system should continue functioning.

---

# Why Can't We Have All Three?

Consider two database nodes:

```text
        Node A              Node B
          |                   |
          |------ Network ----|
```

Now the network fails:

```text
        Node A       X       Node B
                  Partition
```

Suppose a user writes:

```text
Balance = $500
```

The system now has to choose between **Consistency** and **Availability**.

---

## Choose Consistency

Node A receives the write.

Since Node A cannot communicate with Node B, the system refuses requests that cannot guarantee the latest data.

```text
Node A → Write accepted

Node B → Cannot synchronize

↓

Some requests fail

↓

Consistency ✓
Availability ✗
Partition Tolerance ✓
```

This gives:

**CP**

---

## Choose Availability

Both nodes continue responding independently.

```text
Node A → $500

Node B → $400
```

Both nodes remain available, but they temporarily contain different values.

```text
Availability ✓
Consistency ✗
Partition Tolerance ✓
```

This gives:

**AP**

---

# CAP Combinations

## CP — Consistency + Partition Tolerance

The system prioritizes **correct and consistent data** over availability during a network partition.

```text
Consistency ✓
Availability ✗
Partition Tolerance ✓
```

During a partition, some requests may be rejected rather than returning potentially stale data.

### Examples

- Banking systems
- Financial transactions
- Systems where incorrect data is unacceptable

---

# AP — Availability + Partition Tolerance

The system prioritizes **availability** during a network partition, even if some data becomes temporarily inconsistent.

```text
Consistency ✗
Availability ✓
Partition Tolerance ✓
```

The system eventually synchronizes the data.

This is called **eventual consistency**.

### Examples

- Social media feeds
- Cassandra
- DynamoDB-style distributed workloads
- Shopping recommendations

---

# CA — Consistency + Availability

```text
Consistency ✓
Availability ✓
Partition Tolerance ✗
```

This means the system assumes that network partitions do not occur.

This is generally possible in a **single-node or non-distributed system**, but a truly distributed system must be prepared for network partitions.

Therefore, in distributed systems, **P is generally unavoidable**.

---

# Important Point

A common misconception is:

> "CAP means you choose two properties when designing the system."

More accurately:

> **When a network partition occurs, a distributed system must choose between Consistency and Availability.**

Partition Tolerance is generally considered essential for distributed systems because network failures are unavoidable.

---

# CAP Triangle

```text
                  Consistency
                     /\
                    /  \
                   /    \
                  /      \
                 /        \
                /          \
               /            \
              /              \
             /________________\
        Availability       Partition
                           Tolerance
```

You can think of the important choices during a partition as:

```text
              CAP
               |
        Network Partition
               |
        -----------------
        |               |
       CP              AP
        |               |
 Consistency       Availability
```

---


