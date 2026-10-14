# System Design Handbook
### A Layer-by-Layer Guide Following the Request Flow

> Based on the *System Design Blueprint: The Ultimate Guide* diagram. The blueprint uses colour-coded sticky notes: **Metrics** (yellow), **Security** (orange), **Options / Algorithms** (blue). This handbook mirrors that convention with the tags **[Metrics]**, **[Security]**, and **[Options]**.

---

## Table of Contents

1. [Client & DNS Resolution Layer](#1-client--dns-resolution-layer)
2. [Edge & Content Delivery Network (CDN) Layer](#2-edge--content-delivery-network-cdn-layer)
3. [API Gateway & Security Layer](#3-api-gateway--security-layer)
4. [Load Balancing Layer](#4-load-balancing-layer)
5. [Frontend Servers & Connection Management Layer](#5-frontend-servers--connection-management-layer)
6. [Distributed Coordination & Utilities Layer](#6-distributed-coordination--utilities-layer)
7. [Backend Application & Messaging Layer](#7-backend-application--messaging-layer)
8. [Caching & Memory Management Layer](#8-caching--memory-management-layer)
9. [Storage, Databases & Media Pipeline Layer](#9-storage-databases--media-pipeline-layer)
10. [Common Fan-Out & Microservices Layer](#10-common-fan-out--microservices-layer)
11. [Appendix: End-to-End Request Flow & Quick Reference](#appendix-end-to-end-request-flow--quick-reference)

---

## The Big Picture

```
Client ─► DNS ─► CDN/Edge ─► API Gateway ─► Load Balancer ─► Frontend Servers
                                                                   │
                         ┌─────────────────────────────────────────┤
                         ▼                                         ▼
                 Message Dispatcher                         Backend Servers
                                                  ┌────────┬───────┼─────────┬──────────┐
                                                  ▼        ▼       ▼         ▼          ▼
                                             ID Gen /   Caches  Databases  Upload    Pub/Sub ─► Fan-out
                                             Locking                       Media               Services
```

---

## 1. Client & DNS Resolution Layer

### 1.1 Client Device & Request Parameters

The journey starts on a **mobile or web client** (for example, a login screen on a phone). The client builds an HTTP request and needs to resolve the server's IP before sending it.

**Request anatomy**

| Part | Purpose | Example |
|---|---|---|
| Method | Intent of the call | `GET`, `POST`, `PUT`, `PATCH`, `DELETE` |
| URL / path | Resource identification | `/v1/users/42/orders` |
| Query parameters | Filtering, sorting, pagination | `?page=2&limit=20&sort=created_at` |
| Headers | Metadata about the request | `Authorization`, `Content-Type`, `Accept-Encoding` |
| Body | Payload for writes | JSON, form data, multipart |

**Request-side items called out in the blueprint**

- **Compression (Gzip, deflate)**: reduces payload size; negotiated through `Accept-Encoding` / `Content-Encoding`.
- **Request ID**: unique ID per request (e.g. `X-Request-ID`) for tracing across services and logs.
- **Idempotency key**: client-generated unique key so retries of non-idempotent calls (like payments) don't repeat the side effect. The server stores the key with the result and returns the stored result on replay.
- **Many more**: user-agent, locale, API version, correlation IDs, content negotiation, etc.

**[Options / Algorithms]**: choice of REST vs gRPC vs GraphQL; retry with exponential backoff and jitter; client-side caching; connection reuse (HTTP/2, keep-alive).

**[Security]**: TLS everywhere, token-based auth (`Bearer` JWT / OAuth access token), CSRF protection for cookie-based sessions, CORS policy, no secrets in query strings, certificate pinning on mobile.

**[Metrics]**: client-observed latency, request success rate, retry count, payload size.

### 1.2 Response & Error Handling

**Status code families**

| Range | Meaning | Common codes |
|---|---|---|
| **2XX** | Success | `200 OK`, `201 Created`, `202 Accepted`, `204 No Content` |
| **3XX** | Redirection / caching | `301`, `302`, `304 Not Modified` |
| **4XX** | Client error | `400`, `401`, `403`, `404`, `409`, `422`, `429 Too Many Requests` |
| **5XX** | Server error | `500`, `502 Bad Gateway`, `503 Service Unavailable`, `504 Gateway Timeout` |

**Response-side items called out in the blueprint**

- **Pagination**: offset/limit, page-number, or (preferred at scale) **cursor/keyset** pagination.
- **Expiry headers**: `Cache-Control`, `Expires`, `ETag`, `Last-Modified` to control caching.
- **MIME type**: `Content-Type` tells the client how to parse the body.
- **Cookies**: `Set-Cookie` with `HttpOnly`, `Secure`, `SameSite`.
- **Error codes / messages**: a stable machine-readable code plus a human-readable message.

**Recommended error shape**

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Retry after 30 seconds.",
    "request_id": "9f1c2e7a-...",
    "details": []
  }
}
```

**[Metrics]**: response time (p50 / p95 / p99), failed status code counts, error-code distribution, error budget burn.

### 1.3 DNS Infrastructure

DNS translates a human-readable domain such as `google.com` into an IP address.

**Resolution flow**

```
Client ─► ISP / Recursive Resolver
              │ 1. Where is google.com?
              ├──────────────► Root Nameserver        ─► "Ask the .com TLD server"
              ├──────────────► TLD Nameserver (.com)  ─► "Ask google.com's authoritative NS"
              └──────────────► Authoritative NS       ─► "google.com = 12.34.56.78"
         ◄─── IP cached (per TTL) and returned to client
```

| Component | Role |
|---|---|
| **ISP / recursive resolver** | Receives the client query, walks the hierarchy, and caches answers. |
| **Root nameserver** | Top of the hierarchy; points to the right TLD servers. |
| **TLD nameserver** | Manages `.com`, `.net`, `.org`, etc.; points to the authoritative server for the domain. |
| **Authoritative nameserver** | Holds the actual records (A, AAAA, CNAME, MX, TXT) and answers with the final IP, e.g. `12.34.56.78`. |

**[Options]**: DNS-based load balancing (round robin, weighted, latency-based, geo-routing), Anycast, low TTL for fast failover vs high TTL for fewer lookups.

**[Security]**: DNSSEC, DNS over HTTPS/TLS, protection from cache poisoning and DDoS on DNS.

**[Metrics]**: resolution time, cache hit ratio at the resolver, query volume, SERVFAIL rate.

---

## 2. Edge & Content Delivery Network (CDN) Layer

### 2.1 CDN / Edge Infrastructure

A **CDN** places servers close to users so static and streaming content is served from the edge instead of the origin.

**Deployment tags from the blueprint**: `#Global`, `#Regional`, `#Static`.

**Responsibilities**

- **Static content caching**: images, JS/CSS, fonts, video segments, downloadable files.
- **Stream delivery with Adaptive Bitrate (ABR)**: video is encoded at multiple bitrates and split into small segments (HLS / MPEG-DASH). The player switches quality based on available bandwidth.
- **TLS termination at the edge** and reduced round-trip time.
- **Origin shielding**: a mid-tier cache that collapses many edge misses into one origin request.

**Content population strategies**

| Strategy | How it works | Best for |
|---|---|---|
| **Push (hot resource)** | Origin proactively uploads content to the edge | Predictable, popular, rarely-changing content |
| **Pull (rare resource)** | Edge fetches from origin on first miss, then caches | Long-tail content |
| **Hybrid** | Push hot items, pull the rest | Most production systems |

**Cache-miss optimization**: request coalescing, tiered caching, longer TTLs for immutable assets (content-hashed filenames), stale-while-revalidate, prefetching next video segments.

**Invalidation**: TTL expiry, explicit purge by URL or tag, versioned URLs.

**[Metrics]** (from the blueprint)

- **Cache miss count** and cache hit ratio
- **Active resources**
- **Total resources** tracked
- Edge bandwidth, origin offload percentage, time-to-first-byte

**[Security]**: signed URLs/cookies for private content, WAF, DDoS absorption, hotlink protection.

---

## 3. API Gateway & Security Layer

### 3.1 API Gateway Controls

The **API Gateway** is the single entry point for client traffic. It centralizes cross-cutting concerns so backend services stay focused on business logic.

| Control | What it does |
|---|---|
| **Input validation** | Rejects malformed or oversized payloads, enforces schema, sanitizes input (anti-injection). |
| **Authentication** | Verifies *who* the caller is (API keys, JWT, session, **OAuth 2.0**). |
| **Authorization** | Verifies *what* the caller may do (scopes, roles, policies). |
| **Rate limiting / throttling** | Caps requests per client/IP/key to protect backends and ensure fairness. |
| **Whitelisting / blacklisting** | Allow or deny specific IPs, ranges, API keys, or geographies. |
| **Flow control** | Backpressure, concurrency limits, circuit breaking, load shedding. |
| **OAuth 2.0** | Delegated authorization via access/refresh tokens (authorization code + PKCE for apps, client credentials for service-to-service). |
| **TLS / SSL termination** | Decrypts HTTPS at the gateway; traffic inside may be plaintext or re-encrypted (mTLS). |
| **Request header validation** | Checks required headers, content type, size limits, and signatures. |
| **Request deduplication** | Uses request IDs/idempotency keys to drop duplicate submissions. |
| **Metering / usage data collection** | Records usage per client for billing, quotas, and analytics. |
| **Request dispatching** | Routes to the right upstream (path/host/header-based routing, version routing). |

**Rate limiting algorithms [Options]**

| Algorithm | Idea | Trade-off |
|---|---|---|
| **Token bucket** | Tokens refill at a fixed rate; each request consumes one | Allows bursts; most popular |
| **Leaky bucket** | Requests drain at a constant rate | Smooth output, no bursts |
| **Fixed window counter** | Count per time window | Simple; boundary spikes |
| **Sliding window log** | Store timestamps of requests | Accurate; memory heavy |
| **Sliding window counter** | Weighted blend of current and previous windows | Good accuracy/efficiency balance |

Rate-limit state is typically kept in a shared store such as Redis so every gateway node sees the same counters. Respond with `429` plus `Retry-After`.

**[Security]**: the gateway is the main trust boundary. Combine WAF rules, bot detection, schema validation, mTLS to upstreams, and secrets kept out of logs.

**[Metrics]**: requests per second per route/client, auth failure rate, throttled request count, gateway latency overhead.

---

## 4. Load Balancing Layer

### 4.1 Load Balancer Configuration

A **load balancer (LB)** distributes traffic across multiple servers to improve availability, throughput, and fault tolerance.

**Topology**: the blueprint tags the LB as **#MultiPrimary**, meaning several active LB nodes share traffic (active-active) rather than relying on a single primary with a passive standby. Typically fronted by DNS/Anycast or a floating IP, with health checks and automatic failover.

**Layer 4 vs Layer 7**

| | L4 (transport) | L7 (application) |
|---|---|---|
| Decides on | IP, port, protocol | URL, headers, cookies, method |
| Speed | Faster, simpler | Smarter routing, more CPU |
| Features | TCP/UDP balancing | Path routing, TLS termination, sticky sessions, retries |

**Balancing algorithms [Options]**

- **Round robin**: rotate through servers in order.
- **Weighted round robin**: more traffic to higher-capacity servers.
- **Least connections**: pick the server with the fewest active connections.
- **Hash / server stickiness**: consistent hash on client IP, user ID, or session so a client keeps landing on the same server.
- **Random** (and "power of two choices": sample two, pick the less loaded).

**Health checking**: active (probe `/health`) and passive (observe errors/timeouts). Unhealthy nodes are removed and slowly reintroduced (slow start, connection draining).

**[Metrics]** (from the blueprint)

- **Access logs**
- **Status codes**
- **Number of requests**
- **Active connections**
- Also useful: backend response time, unhealthy host count, connection errors.

**[Security]**: DDoS protection, connection limits, TLS policy, hiding backend topology.

---

## 5. Frontend Servers & Connection Management Layer

### 5.1 Frontend Infrastructure

The **frontend servers** terminate client connections and manage session state for real-time features. The blueprint tags them **#InMemoryConnections** and highlights use for **live streaming and chat**.

**Why in-memory connection state?** Long-lived connections (WebSocket, SSE) are bound to a specific server. That server keeps a map of *which user is connected* in memory, so it can push messages immediately.

**Connection mapping table (from the blueprint)**

| User | Connection |
|---|---|
| `user_1` | `ada_inst_obj` |

**Real-time and delivery protocols [Options]**

| Protocol | Direction | Typical use |
|---|---|---|
| **HTTP short polling** | Client repeatedly asks | Simple, wasteful |
| **HTTP long polling** | Server holds the request until data is ready | Fallback for chat |
| **WebSocket** | Full duplex over one TCP connection | Chat, gaming, collaboration |
| **SSE (Server-Sent Events)** | Server → client stream | Feeds, notifications, tickers |
| **Webhook** | Server → server HTTP callback | Third-party event delivery |
| **WebRTC** | Peer-to-peer media/data | Video calls |
| **RTMP** | Ingest streaming | Live video ingest |
| **Stream API** | Long-lived streaming responses | Continuous data feeds |

**Dispatching messages to other FE servers**

A user may be connected to a different frontend server from the sender. The frontend therefore forwards messages to a **Message Dispatcher** (see §7.2), which looks up which server holds the recipient's connection and routes the message.

```
Sender ─► FE Server A ─► Message Dispatcher ─► FE Server B ─► Recipient
                          (lookup: user → server)
```

**Task management**: heartbeats/ping-pong for liveness, reconnect with resume tokens, graceful drain during deploys, per-connection rate limits, backpressure for slow consumers.

**Scaling**: horizontal scaling by adding servers; connection counts per server are bounded by memory and file descriptors.

**[Metrics]** (from the blueprint): **bandwidth** and **topics** (subscriptions) per server; also concurrent connections, message latency, reconnect rate.

**[Security]**: authenticate at handshake, validate origin, per-message authorization, limit message size.

---

## 6. Distributed Coordination & Utilities Layer

### 6.1 Distributed ID Generator

In a distributed system, multiple nodes need to create **unique IDs** without a central bottleneck.

| Approach | Pros | Cons |
|---|---|---|
| **UUID (v4)** | No coordination, trivial | 128 bits, not sortable, poor index locality |
| **UUID v7 / ULID** | Time-ordered, no coordination | Larger than 64-bit |
| **Auto-increment (single DB)** | Simple, sortable | Single point of failure and bottleneck |
| **Auto-increment, multiple servers (odd/even)** | Removes the single bottleneck | Hard to add servers; not globally time-ordered |
| **Twitter Snowflake** | 64-bit, time-sortable, high throughput | Needs unique machine IDs; depends on clock sanity |
| **Offline generation** | IDs created without contacting a service | Needs a collision-avoidance scheme |
| **Baidu UID generator** | Snowflake variant with tuned bit allocation | Library/config dependent |
| **Sonyflake** | Snowflake variant: longer lifetime, fewer machines | Lower per-node rate |

**Snowflake layout (64 bits)**

```
| 1 bit unused | 41 bits timestamp (ms since custom epoch) | 10 bits machine ID | 12 bits sequence |
```

- **Epoch**: a **custom epoch** (e.g. your product's launch date) instead of 1970 extends the usable lifetime (~69 years from the epoch with 41 bits).
- **Sequence**: up to 4096 IDs per millisecond per machine.
- **Clock skew**: protect against clocks moving backwards (refuse to generate, wait, or use logical clocks).

**[Metrics]**: ID generation rate, collisions (should be zero), clock drift.

### 6.2 Distributed Resource Locking

Locks ensure only one process modifies a shared resource at a time across many nodes.

| Tool | Notes |
|---|---|
| **Redlock (Redis)** | Acquire locks on a majority of independent Redis nodes with a TTL. Debated for correctness under clock/pause issues; use fencing tokens when safety matters. |
| **Redisson** | Java client offering distributed locks, semaphores, and more on Redis. |
| **Google Chubby** | Coarse-grained lock service built on Paxos; the inspiration for ZooKeeper. |
| **Apache ZooKeeper** | CP coordination service (ZAB consensus) using ephemeral sequential znodes for locks, leader election, and config. |
| **etcd / Consul** | Raft-based alternatives with leases and sessions. |

**Concurrency control strategies (from the blueprint's backend notes)**

- **Serially**: one at a time; simplest, lowest throughput.
- **Serially in batches**: process groups sequentially to amortize cost.
- **Pessimistic locking**: lock first, then work. Safe under high contention, but blocks.
- **Optimistic locking**: work first, check version at commit (compare-and-swap, `version` column). Great for low contention; retry on conflict.

**Best practices**: always set a TTL/lease, use unique lock owner tokens, use **fencing tokens** (monotonic) to reject stale holders, keep critical sections short, and prefer idempotent operations over locking when possible.

**[Metrics]**: lock wait time, lock hold time, contention rate, timeouts/lease expirations.

---

## 7. Backend Application & Messaging Layer

### 7.1 Backend Servers

The **backend servers** run business logic and are tagged **#ClusterOfServers** and **#MicroService**.

**Scaling**

- **Vertical scaling (scale up)**: bigger machines. Simple, but has a ceiling and is a single point of failure.
- **Horizontal scaling (scale out)**: more machines behind the LB. Needs stateless services or externalized state.

**Cluster coordination**

| Model | Description |
|---|---|
| **Leader / followers** | One leader handles writes/coordination; followers replicate and serve reads. Needs leader election (Raft, Paxos, ZooKeeper). |
| **Multi-leader** | Several leaders accept writes; requires conflict resolution. |
| **Leaderless (gossip protocol)** | Nodes exchange state peer-to-peer (as in Dynamo-style systems); eventually consistent, no single point of failure. |

**Design principles**: stateless request handling, idempotent endpoints, timeouts + retries with backoff, circuit breakers, bulkheads, graceful degradation, health/readiness probes, config via environment, observability (logs, metrics, traces).

**Microservice considerations**: service discovery, API versioning, contract testing, per-service data ownership, saga pattern for distributed transactions.

**[Metrics]** (from the blueprint): **disk I/O (if buffering)**, **CPU**, **memory**, **latency**, **bandwidth**.

### 7.2 Message Dispatcher

The **Message Dispatcher** routes messages between frontend servers using a lookup of who is subscribed to what.

**Routing table (from the blueprint)**

| Server | Topic / Object ID |
|---|---|
| `server_1` | `asdf23dsf3oj23098asfdf3` |

**How it works**

1. A frontend server subscribes on behalf of a connected client to a **topic** or **object ID** (a chat room, a stream, a document).
2. The dispatcher records `server → topic/object`.
3. When a message is published to that topic, the dispatcher finds all servers holding interested subscribers and forwards it.
4. Each server pushes the message down its in-memory client connections.

**Implementation options**: Redis Pub/Sub, Kafka topics, NATS, or a custom service with a consistent-hash ring so a topic maps to a stable dispatcher node.

**Design notes**: at-most-once vs at-least-once delivery, ordering per topic/partition, message IDs for deduplication, handling dead servers (TTL on subscription entries).

**[Metrics]**: dispatch latency, fan-out factor, undeliverable messages, subscription count.

---

## 8. Caching & Memory Management Layer

### 8.1 In-Memory & Distributed Caches

An **in-memory cache** (tagged **#Fast**) holds hot data in RAM (e.g. Redis, Memcached) to cut latency and database load. A **distributed cache** spreads data across nodes for capacity and availability.

**Caching patterns [Options]**

| Pattern | Read path | Write path | Notes |
|---|---|---|---|
| **Cache-aside (lazy loading)** | App checks cache; on miss, loads from DB and populates cache | App writes DB, then invalidates/updates cache | Most common; app owns the logic |
| **Read-through** | Cache itself loads from DB on miss | n/a | Simpler app code |
| **Write-through** | n/a | Write to cache and DB synchronously | Consistent, slower writes |
| **Write-around** | n/a | Write straight to DB, bypass cache | Avoids polluting cache with rarely-read data |
| **Write-back (write-behind)** | n/a | Write to cache; flush to DB asynchronously | Fast writes; risk of data loss |
| **Refresh-ahead (auto-refresh)** | Cache proactively refreshes popular keys before expiry | n/a | Avoids latency spikes |

**TTL (Time-To-Live)**: every entry gets an expiry so stale data eventually disappears. Add **jitter** to TTLs to avoid synchronized mass expiry.

**Common problems and fixes**

| Problem | Fix |
|---|---|
| **Cache stampede (thundering herd)** | Request coalescing / single-flight, locking, refresh-ahead, jittered TTL |
| **Cache penetration** (queries for non-existent keys) | Cache negative results, Bloom filter |
| **Cache avalanche** (many keys expire together) | Randomized TTLs, multi-layer caching |
| **Hot key** | Local replicas, key splitting, request coalescing |
| **Stale data / invalidation** | Event-driven invalidation, short TTLs, versioned keys |

**Distributed cache design**: partitioning via **consistent hashing**, replication for failover, client-side or proxy-based routing (e.g. Redis Cluster hash slots).

**[Metrics]** (from the blueprint): number of items, **cache hit and miss** ratio, **disk and memory usage**; also eviction count, latency, replication lag.

### 8.2 Eviction Policies & Garbage Collection

When memory is full, the cache must decide what to remove.

| Policy | Evicts | Good when |
|---|---|---|
| **LRU** (Least Recently Used) | Entry not accessed for the longest time | Recency predicts reuse (general default) |
| **LFU** (Least Frequently Used) | Entry with the fewest accesses | Stable, skewed popularity |
| **FIFO** | Oldest inserted entry | Simple, predictable |
| **MRU** (Most Recently Used) | Newest accessed entry | Scan-like access patterns |
| **Random eviction** | A random entry | Cheap, surprisingly effective |
| **Least used** | Lowest-usage entry | Simple usage-based variant |
| **On-demand expiration** | Expired entries removed when accessed (lazy) | Low overhead |
| **Garbage collector (active expiry)** | Background sweep removes expired entries | Prevents memory bloat from never-read keys |

**LRU implementation**: hash map + doubly linked list gives O(1) get and put.

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.data = OrderedDict()

    def get(self, key):
        if key not in self.data:
            return None
        self.data.move_to_end(key)          # mark as most recently used
        return self.data[key]

    def put(self, key, value):
        self.data[key] = value
        self.data.move_to_end(key)
        if len(self.data) > self.cap:
            self.data.popitem(last=False)   # evict least recently used
```

**Invalidation** (tagged **#Invalidation** in the blueprint): TTL, explicit delete on write, publish/subscribe invalidation events, versioned keys. Remember the classic quote: invalidation is one of the two hard problems in computer science.

---

## 9. Storage, Databases & Media Pipeline Layer

### 9.1 Database Cluster

The blueprint tags databases with **#ACID** and **#BASE**:

- **ACID**: Atomicity, Consistency, Isolation, Durability (traditional relational transactions).
- **BASE**: Basically Available, Soft state, Eventual consistency (many distributed NoSQL stores).

**Database types**

| Type | Examples | Best for |
|---|---|---|
| **RDBMS** | PostgreSQL, MySQL | Transactions, relationships, structured data |
| **Document** | MongoDB, Couchbase | Flexible schema, nested data |
| **Key-value** | Redis, DynamoDB | Simple lookups, sessions, caching |
| **Wide-column (column family)** | Cassandra, HBase | Massive write throughput, time-ordered data |
| **Graph** | Neo4j, JanusGraph | Relationship traversal (social, fraud) |
| **Quad store** | Triple/quad-based RDF stores | Linked data, knowledge graphs |
| **Time-series** | InfluxDB, TimescaleDB | Metrics, IoT, monitoring |

**Row-oriented vs column-oriented storage**

| | Row-oriented | Column-oriented |
|---|---|---|
| Layout | Whole rows stored together | Each column stored together |
| Strength | OLTP: fast single-row reads/writes | OLAP: fast scans and aggregates, high compression |
| Examples | MySQL, PostgreSQL | ClickHouse, Redshift, Parquet, Cassandra (partially) |

**Replication**

- **Primary (master) → replicas**: the primary takes writes; replicas serve reads and provide failover.
- **Synchronous vs asynchronous**: sync gives stronger durability but higher latency; async is faster but allows replication lag and possible loss on failover.
- **Quorum reads/writes**: with `N` replicas, choose `R` and `W` such that `R + W > N` for strong consistency.
- **Cross-geo replication (#CrossGeo)**: replicas across regions for latency and disaster recovery.
- **Related techniques**: **hinted handoff** (temporarily store writes for a down node), **Merkle trees** (efficient replica comparison/anti-entropy), read repair.

**Sharding (partitioning)** (shards are tagged **#HotNode #Hash**)

| Strategy | How | Pros | Cons |
|---|---|---|---|
| **Range-based** | Key ranges map to shards (e.g. A–F, G–M) | Efficient range queries | Hotspots, uneven distribution |
| **Hash-based** | `hash(key) mod N` | Even distribution | Range queries hard; resharding painful |
| **Consistent hashing** | Keys and nodes on a ring; add/remove nodes moves few keys (virtual nodes smooth the load) | Minimal data movement on scaling | More complex |
| **Geographical** | By region/user location | Low latency, data residency | Uneven load across regions |
| **Directory-based** | A lookup service maps key → shard | Flexible | Lookup becomes a dependency |

Challenges: **hot nodes** (celebrity keys), cross-shard joins and transactions, rebalancing, global secondary indexes.

**Bloom filter**: a space-efficient probabilistic structure used to cheaply check "definitely not present" and skip unnecessary disk or shard lookups.

**Database access control**: RBAC (see §9.2), least privilege, encryption in transit and at rest, audit trails.

**[Metrics]** (from the blueprint): query response time, CPU and disk usage, network throughput, active connections, storage used; also replication lag, slow query count, lock waits.

### 9.2 Cold Storage & Archival

**Cold storage** (tagged **#OldRecords**) keeps rarely accessed data cheaply for long periods (e.g. S3 Glacier, Azure Archive, tape).

**Data lifecycle**

```
Hot (DB / cache)  ─►  Warm (cheaper tier)  ─►  Cold / Archive
   fast, expensive          moderate                 slow, cheapest
```

- **Compliance storage**: retain records for legal/regulatory periods (finance, health); support immutability (WORM) and legal holds.
- **Cost-optimized long-term retention**: lifecycle policies automatically move data between tiers by age or access pattern.
- **Archival pricing**: very low per-GB storage cost, but **retrieval fees and delays** (minutes to hours) and minimum storage durations. Model the full cost, not just capacity.
- **Data encryption**: encrypt at rest (KMS-managed keys) and in transit; manage key rotation.
- **RBAC (Role-Based Access Control)**: restrict who can read or restore archives.
- **Audit trail**: log every access and restore for compliance.

**[Security]** (from the blueprint): **RBAC, data encryption, audit trail**.

### 9.3 Media Upload & Processing

The blueprint's **Upload Media** pipeline turns raw uploads into streamable, optimized assets.

```
Client ─► Object Storage (raw chunks) ─► Message Queue ─► Processing Workers
                                                               │
                                       Processed / Encoded Storage ◄─┘ (push processed media files)
                                                   │
                                                   ▼
                                              CDN / Edge
```

**Step by step**

1. **Chunked upload to Object Storage** (`#S3 #Chunk #Raw`): large files are split into chunks (resumable, parallel uploads). Each new chunk object emits an event.
2. **Message queue**: the event is enqueued ("for every new chunk object"). The queue decouples upload from processing, absorbs bursts, and enables retries.
3. **Validate checksum**: workers verify chunk integrity (e.g. MD5/SHA-256) against the expected value to catch corruption.
4. **Processing workers**: transcode video to multiple resolutions and bitrates (for ABR), generate thumbnails, compress images, extract metadata, scan for malware or policy violations.
5. **Push processed media files** to **Processed / Encoded Storage** (`#Lossless #Lossy #Compression`).
6. **Serve via CDN** with ABR manifests (HLS/DASH).

**Streaming chunk metadata table (from the blueprint)**

| Key | Checksum | Timestamp |
|---|---|---|
| `chunk_1` | `asdf23dsf3oj23098asfdf3` | `1532873423` |

This table tracks each chunk's integrity and ordering so the system can verify, reassemble, resume, and deduplicate.

**Message queue semantics**: at-least-once delivery (make workers **idempotent**), visibility timeouts, retry with backoff, **dead-letter queue** for poison messages.

**[Metrics]** (from the blueprint)

- *Queue*: count of messages, consumption rate, in-transit (waiting for ack) count, queue limit
- *Workers*: compute time, failed count, CPU / disk usage
- *Storage*: compression ratio, storage consumed, object count
- *Broker*: Pub/Sub throughput

---

## 10. Common Fan-Out & Microservices Layer

### 10.1 Messaging & Notifications

**Fan-out** means one event triggers multiple independent consumers. The backbone is a **Pub/Sub queue**: producers publish to a topic once, and every subscribed service receives its own copy.

```
                          ┌─► Recommendation Service
                          ├─► ML Processing Service
Event ─► Pub/Sub Queue ───┼─► Search Service (indexing)
                          ├─► Analytics / Log Processing Service
                          ├─► Notification Service ─► push / email / SMS
                          └─► Payment Charge Service ─► third-party banking
```

**Why fan-out**: loose coupling, independent scaling per consumer, failure isolation (one slow consumer doesn't block others), easy addition of new consumers.

**Technology options**: Kafka (log-based, replayable, ordered per partition), RabbitMQ, SNS+SQS, Google Pub/Sub, Pulsar.

**Notification Service**

- **Channels**: mobile push (APNs/FCM), email, SMS, in-app, streaming alerts.
- **Spam and abuse controls (from the blueprint)**: **spamming** protection, **stop words** filtering, **dedup**, and **retry with idempotent key** to prevent duplicate sends.
- **Features**: templates, user preferences and opt-outs, rate limits per user, quiet hours, priority queues, delivery receipts.

**[Metrics]**: delivery rate, open/click rate, bounce rate, queue lag, per-channel latency.

### 10.2 Specialized Microservices

#### Recommendation Service
- Suggests items (videos, products, people) personalised to a user.
- **Filtering approaches (from the blueprint)**: **content-based** (similar item attributes) and **collaborative** (similar users' behaviour). Modern systems combine both with embeddings and ranking models.
- Pipeline: candidate generation → ranking → re-ranking (diversity, business rules).
- **[Metrics]**: click-through rate, conversion, coverage, latency.

#### ML Processing Service
- Runs model inference and (offline) training or feature pipelines triggered by events: moderation, tagging, transcription, ranking features, fraud scoring.
- Considerations: batch vs real-time inference, feature store, model versioning, A/B testing, GPU capacity, fallbacks when a model is unavailable.
- **[Metrics]**: inference latency, throughput, model accuracy/drift, resource utilisation.

#### Search Service
- Powers full-text and faceted search via an **inverted index** (Elasticsearch, OpenSearch, Solr).
- Fed asynchronously from the pub/sub stream so the index stays near real-time.
- Features: tokenization, stemming, stop words, typo tolerance, relevance ranking (BM25 + signals), autocomplete.
- **[Metrics]**: query latency, indexing lag, zero-result rate.

#### Analytics Service (Event Logging, Metrics, Usage Tracking)
- Includes the blueprint's **Log Processing Service**: collects events and logs, parses and enriches them, and stores them for dashboards and reporting.
- Pipeline: collectors → stream processing (Flink/Spark) → data lake/warehouse (OLAP / column store) → BI dashboards.
- Supports usage tracking and metering that feeds billing and quotas.
- **[Metrics]**: ingestion rate, end-to-end data freshness, event loss rate.

#### Payment Charge Service (Third-Party Integrations & Gateway Processing)
- Charges customers through **third-party banking / payment providers**.
- **Idempotency is essential**: every charge request carries an **idempotency key** so retries never double-charge.
- **Typical failure reasons (from the blueprint)**: expired, blocked, or invalid card; service down; insufficient balance.
- Reliability patterns: state machine for payment status (`created → authorized → captured → settled / failed / refunded`), retries with backoff for transient errors, **reconciliation** jobs against provider reports, webhooks for async status updates, saga/compensation for multi-step flows.
- **[Security]**: PCI-DSS scope reduction (use tokenization, never store raw card data), encryption, strict RBAC, full audit trail, fraud checks.
- **[Metrics]**: authorisation success rate, decline reasons, latency to provider, reconciliation mismatches.

---

## Appendix: End-to-End Request Flow & Quick Reference

### A. Request Lifecycle

1. **Client** builds a request (headers, request ID, idempotency key).
2. **DNS** resolves the domain (ISP → Root → TLD → Authoritative → `12.34.56.78`).
3. **CDN / Edge** serves static content or forwards dynamic requests to the origin.
4. **API Gateway** validates, authenticates, rate-limits, deduplicates, meters, and dispatches.
5. **Load Balancer** picks a healthy frontend/backend instance.
6. **Frontend Server** holds the connection (WebSocket/SSE) and dispatches messages via the **Message Dispatcher**.
7. **Backend Server** executes business logic, using **ID generation** and **distributed locks** where required.
8. **Cache** is consulted first; on a miss, the **database** is queried (sharded, replicated) and the cache is populated.
9. **Media** uploads flow through object storage → queue → workers → encoded storage → CDN.
10. **Events** are published to the **Pub/Sub queue**, fanning out to recommendation, ML, search, analytics, notification, and payment services.
11. **Response** returns along the same path with the proper status code, headers, and error format.

### B. Layer Cheat Sheet

| Layer | Key Concepts | Key Metrics |
|---|---|---|
| Client & DNS | Idempotency key, status codes, DNS hierarchy | Latency, error rate, resolution time |
| CDN / Edge | ABR, push vs pull, cache invalidation | Cache hit/miss, origin offload |
| API Gateway | AuthN/AuthZ, rate limiting, OAuth 2.0, TLS termination | Throttled requests, auth failures |
| Load Balancer | Round robin, least connections, hashing, multi-primary | Active connections, request count |
| Frontend | WebSocket, SSE, long polling, in-memory connections | Concurrent connections, bandwidth |
| Coordination | Snowflake, UUID, Redlock, ZooKeeper | Lock wait time, ID rate |
| Backend / Dispatcher | Leader/followers, gossip, topic → server mapping | CPU, memory, latency, dispatch lag |
| Caching | Cache-aside, write-through, TTL, LRU/LFU | Hit ratio, evictions |
| Storage | ACID vs BASE, sharding, consistent hashing, replication, cold storage | Query time, replication lag |
| Media Pipeline | Chunked upload, checksum, queue, workers | Queue depth, compute time |
| Fan-out Services | Pub/Sub, notification, recommendation, search, analytics, payment | Delivery rate, ingestion lag |

### C. Core Trade-offs to Remember

- **Consistency vs availability (CAP)** under network partitions; **latency vs consistency** otherwise (PACELC).
- **Cache freshness vs speed**: longer TTL means faster but staler data.
- **Push vs pull** for CDN and notifications.
- **Sync vs async** replication and processing.
- **Vertical vs horizontal** scaling.
- **Pessimistic vs optimistic** concurrency.
- **Simplicity vs scale**: don't shard, cache, or split into microservices until measurements justify it.

### D. Glossary

| Term | Meaning |
|---|---|
| **ABR** | Adaptive Bitrate streaming |
| **ACID / BASE** | Transactional vs eventual-consistency models |
| **CDN** | Content Delivery Network |
| **DLQ** | Dead-letter queue |
| **Idempotency** | Repeating an operation has the same effect as doing it once |
| **LRU / LFU** | Least Recently / Least Frequently Used |
| **OAuth 2.0** | Delegated authorization framework |
| **Pub/Sub** | Publish/subscribe messaging pattern |
| **RBAC** | Role-Based Access Control |
| **SSE** | Server-Sent Events |
| **TTL** | Time-To-Live |
| **WAF** | Web Application Firewall |
