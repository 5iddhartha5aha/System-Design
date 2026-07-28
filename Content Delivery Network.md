# IX. Content Delivery Network (CDN)

A **Content Delivery Network (CDN)** is a geographically distributed network of servers that **caches and delivers content from locations closer to users**. This reduces latency, improves performance, and reduces the load on the origin server.

A CDN is mainly used to deliver **static content**, although modern CDNs can also accelerate dynamic content.

---

# Why Do We Need a CDN?

Suppose a website is hosted only in **Mumbai**.

```text
               Users (USA)
                     |
                     |
             Internet
                     |
                     |
             Mumbai Server
```

A user in the USA must communicate with the Mumbai server.

Problems:

- ❌ High latency
- ❌ Slow page loading
- ❌ Increased bandwidth usage
- ❌ Heavy load on the origin server

---

# With a CDN

Instead of serving every request from Mumbai:

```text
                      Users
                         |
          -------------------------------
          |              |              |
      CDN (USA)     CDN (Europe)   CDN (India)
          |              |              |
          -------------------------------
                         |
                  Origin Server
```

Users receive content from the **nearest CDN server**, resulting in much faster response times.

---

# How a CDN Works

Suppose a user requests:

```text
https://example.com/logo.png
```

The request flow is:

1. User sends a request.
2. DNS routes the request to the nearest CDN edge server.
3. The CDN checks whether the file exists in its cache.

### Case 1: Cache Hit

```text
User

↓

Nearest CDN

↓

Image Found

↓

Response
```

The file is returned immediately.

---

### Case 2: Cache Miss

```text
User

↓

Nearest CDN

↓

Cache Miss

↓

Origin Server

↓

Cache File

↓

Response
```

The CDN downloads the file from the origin server, stores it locally, and serves it to the user.

Future requests are served directly from the CDN.

---

# Components of a CDN

## 1. Origin Server

The original server where content is stored.

```text
Application

↓

Origin Server
```

---

## 2. Edge Server (PoP)

A CDN server located close to users.

Example:

```text
India User

↓

Mumbai Edge Server
```

```text
Germany User

↓

Frankfurt Edge Server
```

Each edge server caches frequently requested content.

---

## 3. DNS Routing

DNS directs users to the nearest or healthiest edge server.

```text
User

↓

DNS

↓

Nearest CDN Edge Server
```

Routing decisions may be based on:

- Geographic location
- Network latency
- Server health
- Current traffic load

---

# What Can Be Cached?

### Static Content

- Images
- CSS
- JavaScript
- Videos
- PDFs
- Fonts

These are ideal CDN candidates because they change infrequently.

---

### Dynamic Content

Modern CDNs can also accelerate:

- API responses
- Dynamic web pages
- Personalized content

This is typically achieved through intelligent caching rules rather than caching every response.

---

# Popular CDN Providers

- Cloudflare
- Amazon CloudFront
- Akamai
- Fastly
- Google Cloud CDN
- Azure CDN

---

# Real-World Example

Suppose YouTube stores a popular video in its origin servers.

A user in India requests the video.

Without a CDN:

```text
India User

↓

USA Server
```

With a CDN:

```text
India User

↓

Mumbai CDN Edge Server
```

The video loads much faster because it is served from a nearby edge server instead of a distant origin.

---

# Advantages

- ✅ Faster content delivery
- ✅ Reduced latency
- ✅ Lower origin server load
- ✅ Better scalability
- ✅ High availability
- ✅ DDoS protection
- ✅ Lower bandwidth costs

---

# Disadvantages

- ❌ Additional cost
- ❌ Cache invalidation complexity
- ❌ Dynamic content is harder to cache
- ❌ Possible stale content until cache refresh

---

# Interview Summary

> A **Content Delivery Network (CDN)** is a globally distributed network of **edge servers** that cache and deliver content from locations closer to users. When a user requests content, the request is routed to the nearest edge server. On a **cache hit**, the content is served immediately; on a **cache miss**, the CDN fetches it from the **origin server**, caches it, and returns it to the user. CDNs reduce **latency**, improve **performance**, lower **origin server load**, provide **high availability**, and help mitigate **DDoS attacks**. They are primarily used for **static content** such as images, videos, CSS, and JavaScript, though modern CDNs can also accelerate dynamic content.
