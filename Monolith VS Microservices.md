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
