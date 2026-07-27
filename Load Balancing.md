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

