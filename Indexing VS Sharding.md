# Sharding vs Indexing

**Sharding** and **indexing** are both techniques used to improve database performance, but they solve completely different problems.

| Feature | Sharding | Indexing |
|---------|----------|----------|
| **Purpose** | Improves scalability by distributing data across multiple databases | Improves query speed by creating a fast lookup structure |
| **Problem Solved** | Database becomes too large or receives too much traffic | Queries are slow because the database has to scan many rows |
| **Data Storage** | Data is split into multiple shards | Data remains in the same database |
| **Number of Servers** | Multiple database servers | Usually a single database server |
| **Effect on Reads** | Faster because queries target only one shard | Faster because the index quickly locates rows |
| **Effect on Writes** | Improves write scalability by distributing writes | Slightly slower because indexes must be updated on every write |
| **Complexity** | High | Low to Moderate |

---

# Indexing

An **index** is a separate data structure (commonly a **B-tree** or **hash index**) that stores column values along with pointers to the corresponding rows.

Without an index, if you search for a user by email, the database may have to examine every row (**full table scan**).

With an index on the `email` column, the database can directly locate the matching row instead of scanning the entire table.

Think of it like the **index at the back of a textbook**. Instead of reading every page to find "Binary Trees," you look it up in the index and jump directly to the relevant pages.

### When to Use Indexing

- Frequently searched columns
- Columns used in `WHERE` clauses
- Columns used in `JOIN`s
- Columns used in `ORDER BY`
- Columns used in `GROUP BY`

### Drawbacks

- Consumes additional storage.
- Inserts, updates, and deletes become slightly slower because the index must also be maintained.

---

# Sharding

**Sharding** divides the database horizontally into multiple smaller databases called **shards**. Each shard stores only a subset of the total data.

Instead of storing all **500 million users** in one database, you might distribute them across several shards based on a **shard key** (such as `user_id` or geographic region).

When a request arrives, the application or a routing layer determines which shard contains the required data and queries only that shard.

Sharding addresses problems that indexing cannot solve:

- Database storage becomes too large for one server.
- Write traffic exceeds the capacity of a single database.
- More users and requests need to be handled by adding additional database servers.

### Drawbacks

- Cross-shard queries are more complex.
- Choosing a good shard key is difficult.
- Resharding and replication add operational complexity.

---

# Can They Be Used Together?

Yes. In fact, they almost always are.

Imagine an e-commerce platform with hundreds of millions of users.

- The data is **sharded** across multiple database servers based on `user_id`.
- Within **each shard**, indexes are created on columns like `email`, `order_id`, or `product_id` to speed up searches.

The flow is:

1. The application determines the correct **shard**.
2. The database on that shard uses an **index** to quickly find the requested record.

So,

- **Sharding** decides **which database to search**.
- **Indexing** decides **how to search efficiently within that database**.

---

# Simple Analogy

Imagine a library.

**Indexing** is like having a catalog that tells you exactly which shelf and position a book is on. The library building stays the same; you just find books faster.

**Sharding** is like splitting the library into multiple branches across different cities. Before finding the book, you first determine **which branch** has it, and then use that branch's catalog to locate it.

---

# Comparison Summary

| Aspect | Sharding | Indexing |
|--------|----------|----------|
| Goal | Scale the database | Speed up queries |
| Works By | Splitting data across multiple databases | Creating a lookup structure for faster access |
| Solves | Storage and scalability issues | Slow query performance |
| Best For | Very large datasets and high traffic | Frequently queried columns |
| Used Together? | Yes | Yes |

---

# Interview Summary

> **Indexing** improves query performance by creating a fast lookup structure within a database, allowing records to be found without scanning the entire table. It speeds up reads but adds some overhead to writes.
>
> **Sharding** improves scalability by splitting data across multiple database servers. It distributes storage and traffic, enabling the system to handle much larger datasets and higher write throughput. In practice, databases are often **sharded first**, and **each shard maintains its own indexes** for efficient querying.
