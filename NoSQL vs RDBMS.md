# NoSQL vs RDBMS

Databases are broadly classified into two categories:

1. **Relational Database Management Systems (RDBMS)**
2. **NoSQL (Not Only SQL) Databases**

The main difference lies in **how data is stored, organized, and scaled**.

---

# 1. Relational Database (RDBMS)

An **RDBMS** stores data in the form of **tables (rows and columns)**.

Relationships between tables are established using **Primary Keys** and **Foreign Keys**.

Example:

### Users Table

| User ID | Name | Email |
|----------|------|-------|
| 101 | Alice | alice@example.com |
| 102 | Bob | bob@example.com |

### Orders Table

| Order ID | User ID | Product |
|----------|----------|----------|
| 1 | 101 | Laptop |
| 2 | 102 | Phone |

Here, **User ID** acts as a **Foreign Key** in the Orders table.

---

## Characteristics

- Structured data
- Fixed schema
- ACID compliant
- Supports joins
- Uses SQL

---

## Advantages

- ✅ Strong consistency
- ✅ Complex joins
- ✅ ACID transactions
- ✅ Mature ecosystem
- ✅ Ideal for structured data

---

## Disadvantages

- ❌ Difficult to scale horizontally
- ❌ Fixed schema
- ❌ Schema changes can be expensive
- ❌ Less suitable for rapidly changing data

---

# 2. NoSQL Database

A **NoSQL** database stores data in **non-tabular formats**.

Unlike RDBMS, NoSQL databases usually **do not require a fixed schema**.

Each record can have different fields.

Example (Document Database):

```json
{
  "userId": 101,
  "name": "Alice",
  "email": "alice@example.com",
  "orders": [
    "Laptop",
    "Keyboard"
  ]
}
```

Another document:

```json
{
  "userId": 102,
  "name": "Bob",
  "phone": "9999999999",
  "address": "New York"
}
```

Notice that both documents have different fields.

---

## Advantages

- ✅ Flexible schema
- ✅ Insertion & retrievals are easier since it gets the whole document at once and doesn't require using joins.
- ✅ Built for scalability

---

## Disadvantages

- ❌ Doesn't support ACID properties
- ❌ Relationships aren't implicit
- ❌ Joins are hard

---

# Schema Comparison

## RDBMS

Every row follows the same schema.

```text
Users

-------------------------

ID

Name

Email
```

Adding a new column:

```text
Phone Number
```

requires altering the table.

---

## NoSQL

Each document can have its own structure.

Document 1:

```json
{
  "name": "Alice",
  "email": "alice@example.com"
}
```

Document 2:

```json
{
  "name": "Bob",
  "phone": "9999999999"
}
```

No schema migration is required.

---

# Scaling

## RDBMS

Usually scales **vertically**.

```text
Database

↓

More CPU

More RAM

More Storage
```

Horizontal scaling is possible but more complex.

---

## NoSQL

Designed for **horizontal scaling**.

```text
Application

↓

----------------------------

DB1

DB2

DB3
```

Data is distributed across multiple servers using **sharding**.

---

# Transactions

## RDBMS

Supports **ACID** transactions.

ACID stands for:

- Atomicity
- Consistency
- Isolation
- Durability

Ideal for:

- Banking
- Payments
- Financial systems

---

## NoSQL

Many NoSQL databases follow the **BASE** model.

BASE stands for:

- Basically Available
- Soft State
- Eventual Consistency

Instead of immediate consistency, data may become consistent after a short delay.

---

# Relationships

## RDBMS

Supports joins.

Example:

```text
Users

↓

JOIN

↓

Orders
```

Relationships are normalized.

---

## NoSQL

Joins are generally avoided.

Related data is often stored together in the same document.

Example:

```json
{
  "user": "Alice",
  "orders": [
    "Laptop",
    "Phone"
  ]
}
```

This is called **denormalization**.

---

# Performance

## RDBMS

Excellent for:

- Complex queries
- Transactions
- Joins

---

## NoSQL

Excellent for:

- Massive datasets
- High write throughput
- Large-scale distributed systems
- Flexible schemas

---

# When to Use RDBMS

Choose RDBMS when:

- Strong consistency is required
- Complex joins are needed
- Financial transactions
- Inventory management
- ERP systems
- Banking applications

Examples:

- MySQL
- PostgreSQL
- Oracle
- Microsoft SQL Server

---

# When to Use NoSQL

Choose NoSQL when:

- Rapid development
- Flexible schema
- Big data
- High traffic
- Horizontal scalability
- Real-time applications

Examples:

- Social media
- Chat applications
- IoT
- Content management systems

Popular databases:

- MongoDB
- Cassandra
- Redis
- DynamoDB

---

# Real-World Example

## Banking System

A banking application requires:

- Accurate balances
- ACID transactions
- Strong consistency

**Best Choice:** RDBMS

---

## Social Media Platform

A social media platform stores:

- User profiles
- Posts
- Likes
- Comments
- Images

The schema changes frequently and the system serves millions of users.

**Best Choice:** NoSQL

---

# Comparison

| Feature | RDBMS | NoSQL |
|----------|-------|--------|
| Data Model | Tables | Documents, Key-Value, Column, Graph |
| Schema | Fixed | Flexible |
| Query Language | SQL | Database-specific APIs/queries |
| Joins | Supported | Usually avoided |
| Transactions | Strong ACID support | Often BASE (many also support ACID within limits) |
| Horizontal Scaling | Difficult | Built for it |
| Vertical Scaling | Common | Possible but less emphasized |
| Best For | Structured data | Semi-structured or unstructured data |
| Consistency | Strong | Often eventual consistency |
| Flexibility | Lower | Higher |

---

# Interview Summary

> An **RDBMS** stores data in **tables** with a **fixed schema**, supports **SQL**, **ACID transactions**, and **complex joins**, making it ideal for applications that require strong consistency, such as banking and financial systems.
>
> A **NoSQL** database stores data in flexible formats such as **documents**, **key-value pairs**, **column families**, or **graphs**. It is designed for **horizontal scalability**, **high availability**, and handling **large volumes of semi-structured or unstructured data**, making it suitable for social media platforms, real-time applications, and distributed systems.
>
> In modern architectures, it is common to use **both**—an RDBMS for transactional data and a NoSQL database for caching, sessions, analytics, or high-scale workloads.
