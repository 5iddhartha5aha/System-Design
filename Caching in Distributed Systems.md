# Caching in Distributed Systems

**Caching** is the process of storing **frequently accessed data in a fast storage layer (cache)** so that future requests can be served much faster than retrieving data from the primary database.

A cache is typically stored in **memory (RAM)**, making it significantly faster than disk-based databases.

---

# Why Do We Need Caching?

Suppose an application has **millions of users**.

Without caching:

<img width="907" height="366" alt="image" src="https://github.com/user-attachments/assets/b51a14bd-b08c-46e9-ac33-dab6a6223d8e" />

For every request:

1. Client sends request.
2. Application queries the database.
3. Database returns data.
4. Application sends response.

Problems:

- ❌ High database load
- ❌ Increased latency
- ❌ Slow response time
- ❌ Expensive database scaling

---

# With Caching

<img width="1092" height="562" alt="image" src="https://github.com/user-attachments/assets/af0b6512-1189-464f-b19d-d3d8f9a5f2a3" />

Now the application first checks the cache.

If data exists:

```text
Client

↓

Application

↓

Cache

↓

Response
```

The database is never accessed.

If data does not exist:

```text
Client

↓

Application

↓

Cache (Miss)

↓

Database

↓

Cache Update

↓

Response
```

The data is fetched from the database, stored in the cache, and returned to the client.

---

# Cache Hit vs Cache Miss

## Cache Hit

The requested data is found in the cache.

```text
Client

↓

Cache

↓

Data Found

↓

Response
```

Advantages:

- Very fast
- No database query
- Low latency

---

## Cache Miss

The requested data is not in the cache.

```text
Client

↓

Cache

↓

Not Found

↓

Database

↓

Cache Update

↓

Response
```

The first request is slower, but future requests become faster.

---

# Why is Cache Faster?

| Storage | Approximate Access Time |
|----------|------------------------:|
| CPU Cache | Nanoseconds |
| RAM (Cache) | Tens to hundreds of nanoseconds |
| SSD | Microseconds |
| HDD | Milliseconds |
| Network Database | Several milliseconds |

Since caches are stored in **RAM**, access is much faster than querying a database stored on disk.

---

# Cache Eviction Policies

Since cache memory is limited, old data must be removed to make room for new data.

## 1. LRU (Least Recently Used)

Removes the item that has **not been accessed for the longest time**.

Example:

```text
Cache Capacity = 3

[A] [B] [C]

Access A

[A] [C] [B]

Insert D

[A] [C] [D]
```

**B** is removed because it was the least recently used.

Advantages:

- Good for most applications
- Very commonly used

---

## 2. LFU (Least Frequently Used)

Removes the item with the **lowest access frequency**.

Example:

```text
A → Accessed 15 times

B → Accessed 2 times

C → Accessed 8 times
```

Insert D.

```text
B is removed.
```

Advantages:

- Good when popular data remains popular for a long time

---

## 3. FIFO (First In First Out)

Removes the oldest cached item.

Example:

```text
Inserted:

A

↓

B

↓

C

↓

Insert D

↓

Remove A
```

Simple but may remove frequently used data.

---

## 4. TTL (Time-To-Live)

Each cached item has an expiration time.

Example:

```text
User Profile

TTL = 5 minutes
```

After 5 minutes:

```text
Cache Entry

↓

Expired

↓

Removed
```

Useful for frequently changing data.

---

# Cache Invalidation

One of the hardest problems in distributed systems is ensuring the cache remains consistent with the database.

Suppose:

```text
Database

Product Price = $100
```

The cache stores:

```text
Product Price = $100
```

Now the database is updated:

```text
Database

Product Price = $120
```

If the cache is not updated:

```text
Client

↓

Cache

↓

$100 (Old Value)
```

The client receives stale data.

This is called **cache inconsistency**.

---

# Cache Invalidation Strategies

## 1. Write-Through Cache

Every write updates both the cache and the database.

```text
Client

↓

Cache Update

↓

Database Update
```

Advantages:

- Cache always remains consistent

Disadvantages:

- Slower writes

---

## 2. Write-Back (Write-Behind)

Writes go only to the cache initially.

Later, the cache asynchronously writes to the database.

```text
Client

↓

Cache

↓

Immediate Response

↓

Database Update (Later)
```

Advantages:

- Very fast writes

Disadvantages:

- Risk of data loss if the cache fails before syncing

---

## 3. Write-Around

Writes go directly to the database.

The cache is updated only when the data is read again.

```text
Write

↓

Database

↓

(No Cache Update)

↓

Later Read

↓

Cache Updated
```

Advantages:

- Avoids caching rarely accessed data

Disadvantages:

- First read after a write is slower

---

# Cache Placement

## Client-Side Cache

The idea of caching can be extended even to the browser or mobile application (client-side), storing data locally.


<img width="925" height="337" alt="image" src="https://github.com/user-attachments/assets/e6bcf1d2-ce86-4a3a-945f-17d71ed83511" />


Examples:

- Browser cache
- Mobile app cache

Advantages:

- No server request
- Very low latency

---

## Server-Side Cache

The application stores frequently accessed data in a centralized cache.

<img width="1092" height="562" alt="image" src="https://github.com/user-attachments/assets/af0b6512-1189-464f-b19d-d3d8f9a5f2a3" />

Common technologies:

- Redis
- Memcached

---
