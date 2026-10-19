# System Design Handbook
### A Layer-by-Layer Guide Following the Request Flow

> Based on the *System Design Blueprint: The Ultimate Guide* diagram. The blueprint's sticky notes group extra details by type (yellow = what to measure, orange = security, blue = options and algorithms). In this handbook, each layer follows the same pattern in plain labels: **Design options** (the choices you can make), **Key metrics to monitor** (how you know it is healthy), and **Security considerations** (how you protect it).

---

## Table of Contents

0. [Foundations: Requirements, Estimation & Thinking Framework](#0-foundations-requirements-estimation--thinking-framework)
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
11. [Distributed Systems Fundamentals](#11-distributed-systems-fundamentals)
12. [Scalability](#12-scalability)
13. [Reliability & Availability](#13-reliability--availability)
14. [Observability](#14-observability)
15. [Security (System-Level)](#15-security-system-level)
16. [Infrastructure, Deployment & Cost](#16-infrastructure-deployment--cost)
17. [Worked Example: Movie Website for 1M Users](#17-worked-example-movie-website-for-1m-users)
18. [Appendix: End-to-End Request Flow & Quick Reference](#appendix-end-to-end-request-flow--quick-reference)

---

System Design is the process of designing a system’s architecture, components, and interfaces to meet functional and non-functional requirements while ensuring scalability, reliability, and maintainability.
Importance of System Design
- Scalability and Reliability: Handle growing traffic and prevent failures.
- Performance and Efficiency: Optimize resources and response times.
- Maintainability: Adapt to changing business requirements.
- Architecture: Understand monolithic and microservices architectures.

- 
System Design Fundamentals
- High-Level Design (HLD): Overall architecture, major components, and communication between them.
- Low-Level Design (LLD): Classes, interfaces, methods, and detailed implementation.
- Functional and non-functional requirements.
- Architectural Styles: Monolithic, microservices, event-driven, and serverless architectures.

High-Level Design (HLD)
-----------------------

High-level design (HLD) defines the overall architecture of a system and how the main components interact with each other. It provides a big-picture view of the system structure, services, and data flow.

-   Identifies major modules, services, and their interactions.
-   Focuses on system architecture and high-level decisions.
-   Usually created by architects, stakeholders, and senior developers.

### Topics Covered in HLD

Focuses on system architecture, modules, and their interactions.

-   **System architecture overview**: Defines the major components, modules, and how they interact (e.g., services, queues, databases) .
-   **Data flow and component interaction**: Illustrates how data moves between modules, along with key integrations and interfaces.
-   **Technology stack and infrastructure**: High-level decisions on frameworks, platforms, hardware, databases, and hosting setups.
-   **Module responsibilities**: Describes what each module does and how they relate to one another .
-   **Performance & trade-offs**: Includes design trade-offs, performance considerations, scalability, security, cost and other non-functional factors .
-   **Artifacts**: Commonly includes architecture diagrams, component and deployment diagrams, data flow diagrams, and possibly ER/DB schematic overviews.

Low-Level Design (LLD)
----------------------

Low-Level Design (LLD) focuses on the internal implementation details of each component in the system. It provides developers with a clear and detailed blueprint for how modules, classes, and functions should be built.

-   Describes the internal logic, classes, methods, and data structures of each module.
-   Converts the High-Level Design into detailed implementation plans.
-   Usually created by senior developers or designers before coding begins

Covers how each component is implemented, including classes, methods, and logic.

-   **Component/module breakdown**: Detailed internal logic for each module---with class responsibilities, methods, attributes, interactions
-   **Database schema & structure**: Designing tables, keys, indexes, relationships with SQL/NoSQL refinements
-   **API & interface definitions**: Precise request/response formats, error codes, methods, endpoints, and internal interfacing
-   **Error handling & validation logic**: Define how each module manages invalid inputs, failures, edge cases, and logging
-   **Design patterns & SOLID**: Implement design patterns and solid principles to ensure clean, extensible, maintainable code
-   **UML and pseudocode artifacts**: Class diagrams, sequence diagrams, pseudocode or flowcharts to clarify logic paths and method calls

| HIGH-LEVEL DESIGN (HLD) | LOW-LEVEL DESIGN (LLD) |
| --- | --- |
| Defines the overall system architecture | Focuses on detailed component-level design |
| Describes modules and their interactions | Describes internal logic of each module |
| Also known as macro-level/system design | Also known as micro-level/detailed design |
| Created by solution architects | Created by developers/designers |
| Based on SRS (Software Requirement Specification) | Based on reviewed HLD |

> HLD = What components does the system need and how do they interact?\
> LLD = How will each component be implemented?

Steps for getting started with System Design
--------------------------------------------

Here are some steps to get started with system design:

![system_design](https://media.geeksforgeeks.org/wp-content/uploads/20260410124919776366/system_design.webp "Click to enlarge")

-   **Understand Requirements:** Gather and analyze business needs to clearly define system functionality and avoid issues.\
    **Example:** For a food delivery app, requirements may include user login, restaurant listing, order placement, and online payment.

-   **Define Architecture:** Plan overall system structure, components, and their interactions.\
    **Example:** In an e-commerce system, the architecture may include services like user service, product service, order service, and payment service.

-   **Choose Tech Stack:** Select suitable technologies, frameworks, and databases based on requirements and scalability.\
    **Example:** A web application might use React for frontend, Node.js for backend, and MongoDB as the database.

-   **Design Modules:** Divide the system into smaller components with specific responsibilities.\
    **Example:** In a banking system, modules may include account management, transaction processing, and customer management.

-   **Plan for Scalability:** Design the system to handle growth using scalable solutions and identifying bottlenecks.\
    **Example:** A video streaming platform may use multiple servers and caching to handle millions of users at the same time.

-   **Ensure Security & Privacy:** Apply security measures like authentication, encryption, and secure communication.\
    **Example:** An online banking system uses secure login, encrypted transactions, and multi-factor authentication.

-   **Test & Validate:** Test the system to ensure it meets functional and performance requirements.\
    **Example:** Testing a ride-hailing app to ensure booking, driver matching, and payment processing work correctly.

Important points to consider when designing a software system
-------------------------------------------------------------

Designing a software system requires balancing performance, scalability, and maintainability to meet both current and future business needs.

-   **Scalability:** The system should be designed to handle increased loads and be able to scale horizontally or vertically as needed.
-   **Performance:** The system should be designed to perform efficiently and effectively, with minimal latency and response time.
-   **Reliability:** The system should be reliable and available, with minimal downtime or system failures.
-   **Security:** The system should be designed with security in mind, including measures to prevent unauthorized access and protect sensitive data.
-   **Maintainability:** The system should be designed to be easy to maintain and update, with clear documentation and well-organized code.
-   **Interoperability:** The system should be designed to work seamlessly with other systems and components, with clear and well-defined interfaces.
-   **Usability:** The system should be designed to be user-friendly and intuitive, with a clear and consistent user interface.
-   **Cost-effectiveness:** The system should be designed to be cost-effective, with a focus on minimizing development and operational costs while still meeting the requirements.


Functional Requirements
-----------------------

Functional requirements describe the features, behaviors, and operations that a system must provide.

They specify how the system should respond to user actions, inputs, or events.

-   **User Authentication:** Users can register, log in, and log out.
-   **Search:** Users can search for products or information.
-   **Payment Processing:** Users can complete payments.
-   **Order Management:** Users can create, view, or cancel orders.
-   **Notifications:** The system can notify users about relevant events.
-   **Report Generation:** The system can generate required reports.

Non-Functional Requirements
---------------------------

Non-functional requirements (NFRs) define the quality attributes and constraints of a system.

They should be expressed using measurable targets wherever possible so that they can be validated.

-   **Performance:** Defines response-time, latency, or throughput targets.
-   **Availability:** Defines how often the system should remain accessible.
-   **Reliability:** Defines expected behavior during failures and recovery.
-   **Scalability:** Defines how the system should handle increasing traffic, users, or data.
-   **Security:** Defines requirements for authentication, authorization, encryption, and data protection.
-   **Maintainability:** Defines expectations for updating, testing, debugging, and modifying the system.
-   **Usability:** Defines usability or accessibility expectations.
-   **Portability:** Defines supported environments, platforms, or deployment targets.








Components of System Design
===========================

# 1. Load Balancer 
---------------------
Load balancing is the process of distributing incoming network or application traffic across multiple backend servers or resources. Its main purpose is to prevent a single server from becoming overloaded while improving performance, scalability, and availability. 
**Example:** A company may use NGINX, HAProxy, or AWS Elastic Load Balancing to distribute incoming requests across multiple backend servers.

## Types of Load Balancers

### 1. Based on Deployment

- Hardware Load Balancer: A dedicated physical device for distributing traffic. Example: F5.
- Software Load Balancer: Software that distributes traffic across servers. Examples: NGINX, HAProxy.
- Cloud Load Balancer: A managed service provided by cloud platforms. Example: AWS Elastic Load Balancing.

### 2. Based on OSI Model

- Layer 4 Load Balancer: Routes traffic using IP addresses, TCP/UDP protocols, and port numbers.
- Layer 7 Load Balancer: Routes HTTP/HTTPS traffic using URLs, headers, hostnames, cookies, and request methods.


<br>

  

# 2. Caching
-----------

A technique for temporarily storing frequently requested data that speeds up its retrieval when needed again called [caching](https://www.geeksforgeeks.org/system-design/caching-system-design-concept-for-beginners/). The main database or data source is less burdened when caching is included in system architecture, which enhances performance and efficiency.

Here's how caching works and why it's beneficial:

-   **Quick Access**: When data is cached, it's stored in a faster, temporary storage (like memory) instead of being fetched repeatedly from a slower storage source. This makes retrieving information faster.
-   **Reduces Database Load**: The system can process more requests without experiencing any lag by keeping frequently used or popular data in the cache instead of constantly querying the main database.
-   **Improves User Experience**: Faster data access means faster response times for users, which improves the overall experience on websites or apps.


## Types of Cache

### 1. Application Server Cache

Stores frequently accessed data within an application server to reduce database queries and improve response times.

Drawback: Each server maintains its own cache, causing cache misses and inconsistent data across multiple servers.

### 2. Distributed Cache

Distributes cached data across multiple nodes. Consistent hashing helps locate the node containing the required data.

Example: Redis Cluster.

### 3. Global Cache

Provides a shared cache accessible to all application servers. If data is unavailable, it can be fetched from the database or underlying data source.

Example: A shared Redis instance.

### 4. CDN / Edge Cache

Stores static content on geographically distributed servers to deliver it faster to users.

Examples: Images, CSS, JavaScript, and videos.

## Applications of Caching

- Web Page Caching: Stores web resources to speed up page loading.
- Database Caching: Reduces repeated database queries.
- CDN Caching: Delivers content from nearby servers.
- Session Caching: Stores user session information.
- API Response Caching: Reuses responses to reduce processing time.

## Cache Invalidation Strategies

Cache invalidation ensures cached data remains up to date when the original data changes.

- Time-Based (TTL): Expires data after a specified duration.
- Event-Based: Updates or removes cached data when the source changes.

## Cache Eviction Policies

Eviction policies determine which items to remove when the cache is full.

- LRU (Least Recently Used): Removes the item not accessed for the longest time.
- LFU (Least Frequently Used): Removes the least frequently accessed item.
- FIFO (First In, First Out): Removes the oldest cached item.

## Pros

- Improved Performance: Reduces data access time and response latency.
- Reduced Server Load: Minimizes repeated database queries.
- Cost Savings: Improves resource utilization and reduces infrastructure costs.
- Better Scalability: Helps handle more requests efficiently.

## Cons

- Data Inconsistency: Cached data may become outdated.
- Eviction Overhead: Poor policies can reduce cache hit rates.
- Additional Complexity: Requires managing expiration, invalidation, and consistency.



# 3. Content Delivery Network (CDN)
----------------------------------
## CDN (Content Delivery Network)

A CDN is a geographically distributed network of servers that caches and delivers website content from locations closer to users, reducing latency and improving performance.

## Without CDN vs With CDN
**Without CDN:** Requests go directly to the origin server, increasing latency for distant users and potentially overloading the server during traffic spikes.
<img width="738" height="350" alt="image" src="https://github.com/user-attachments/assets/da7a3080-cdc3-4798-9240-9bac2c2db923" />


**With CDN:** Requests are served from nearby edge servers, reducing latency, improving load times, and lowering the origin server's workload.
<img width="758" height="370" alt="image" src="https://github.com/user-attachments/assets/57d34409-86b4-4645-bd4b-ba1633a29c55" />


## Real-World Applications

- Streaming: Netflix, YouTube — smoother video playback.
- E-commerce: Amazon — faster product pages and images.
- Software Distribution: Faster downloads of updates and applications.

## Types of CDN

- Public CDN: Shared CDN services available to many customers. Example: Cloudflare.
- Private CDN: Dedicated content delivery infrastructure for an organization.
- Peer-to-Peer (P2P) CDN: Distributes content between users. Example: WebTorrent.
- Hybrid CDN: Combines public and private delivery infrastructure.
- Push CDN: Content is uploaded to CDN servers in advance.
- Pull CDN: Content is fetched from the origin when requested and cached.

## Components of a CDN

- Edge Servers: Cache and deliver content near users.
- Origin Server: Stores the original content.
- Distribution Nodes: Help route traffic and distribute content.
- Control Plane: Manages routing, caching, and traffic distribution.
<img width="828" height="340" alt="image" src="https://github.com/user-attachments/assets/eeaf2863-1f10-4e47-8c6c-a7bc28c63c7e" />




## Types of Content Delivered

- Static Content: Images, CSS, JavaScript, logos, and videos. Easily cached.
- Dynamic Content: Personalized pages, feeds, and live data. Usually generated on demand and cached selectively.

## Importance of CDN

- Faster Delivery: Reduces latency and page load times.
- Scalability: Handles traffic spikes efficiently.
- Reliability: Provides redundancy across multiple servers.
- Cost Savings: Reduces origin bandwidth and server load.
- Security: Can provide DDoS protection, SSL/TLS, and web application firewall (WAF) features.

## Challenges

- Cost: Adds service and bandwidth expenses.
- Complexity: Requires cache and routing configuration.
- Security: Requires proper access controls and cache rules.





4\. API Gateways
----------------

An [API Gateway](https://www.geeksforgeeks.org/system-design/what-is-api-gateway-system-design/) is like a central doorway or "traffic controller" for requests coming into a system. In system design, it acts as a single entry point for clients (such as apps or websites) to access multiple backend services in an organized and secure way.

Let's see how it works:

-   **Request Routing**: A client submits a request to the API Gateway when it wants information or actions from several services. After forwarding the request to the appropriate services, the gateway compiles their answers and provides the client with a single, unified response.
-   **Simplifies Client Interaction**: Without an API Gateway, clients might need to connect directly to each service individually, which can be complex. The API Gateway simplifies this by hiding the backend details from clients.
-   **Security and Monitoring**: The gateway can handle security features like authentication (checking user identities) and authorization (checking what they're allowed to do). It also helps monitor traffic, so you can track and log usage, spot issues, or prevent attacks.
-   **Load Management**: In busy systems, the gateway can help balance and control the flow of requests to prevent overload, ensuring smoother performance.

**5\. Key-value stores**
------------------------

One kind of NoSQL database that is meant for storing data as a collection of key-value pairs is called a key-value store. Every piece of data is kept in a key-value store under a distinct key, and the data itself serves as the value. Since key-value stores allow for quick access to data by key, they are typically used to store data that is accessed frequently.

-   Key-value stores come in a variety of forms, such as persistent key-value stores, which store data on disk or in a distributed file system for durability, and in-memory key-value stores, which store data in memory for quick access.
-   Key-value stores are generally simpler to use and more scalable than other types of databases, such as RDBMS. However, they are not as well-suited for storing complex structured data that requires advanced querying capabilities.

**6\. Blob storage & Databases**
--------------------------------

Blob storage and database systems are two different types of storage systems that can be used to store and manage data.

Large volumes of unstructured data, including documents, photos, videos, and audio files, can be stored in blob storage, sometimes referred to as object storage. In general, blob storage systems are very scalable and capable of managing several requests at once. They are widely used to store frequently accessible material, such user-generated content or media files.

On the other hand, database systems are made to hold structured data that has been arranged in a particular manner. RDBMSs, NoSQL databases, and in-memory databases are among the several kinds of database systems. Database systems are typically used to store data that needs to be queried and accessed in a structured way, such as customer records or financial transactions.

**7\. Rate limiters**
---------------------

System design components known as [rate limiters](https://www.geeksforgeeks.org/system-design/rate-limiting-in-system-design/) are used to restrict the rate at which a system or application responds to requests or carries out specific tasks. This can be helpful in a variety of situations, such as when a system has to guard against receiving too many requests or when a company want to stop a particular user or group of users from submitting excessive requests that can affect the system's performance.

Some common types of rate limiters include:

-   **Request rate limiters** are used to limit the number of requests that a system or application processes within a given time period.
-   **User rate limiters** are used to limit the rate at which a specific user or group of users can make requests to a system or application.
-   **Token bucket rate limiters** are used to limit the rate at which requests are processed by a system by allowing a certain number of requests to be processed in each time period, with any excess requests being held in a "bucket" until the next time period.

**8\. Monitoring System**
-------------------------

A [monitoring system](https://www.geeksforgeeks.org/system-design/types-of-monitoring-in-system-design/) is a system design component that is used to collect, analyze, and report on various metrics and performance data related to a system or application. This can be useful in a number of different scenarios, such as when a system needs to track its own performance and availability, or when an organization needs to monitor the performance of its systems and applications to ensure that they are meeting their desired service levels.

Some common types of monitoring systems include:

-   **Network monitoring systems,** are used to monitor the performance of a network and its various components, such as routers, switches, and servers.
-   **System monitoring systems,** are used to monitor the performance of a computer system and its various components, such as the CPU, memory, and disk usage.
-   **Application monitoring systems**, are used to monitor the performance of specific applications or services, such as web servers or databases.

**9\. Distributes system messaging queue**
------------------------------------------

A distributed system [messaging queue](https://www.geeksforgeeks.org/system-design/message-queues-system-design/) is a system that enables the exchange of messages between different nodes in a distributed system. Messaging queues allow nodes to communicate asynchronously, decoupling the sender and receiver of a message and enabling each node to operate independently.

There are several different types of messaging queues, including:

-   **Point-to-point queues:** In this type of queue, messages are delivered to a specific recipient.
-   **Publish-subscribe queues:** In this type of queue, messages are published to a topic and are delivered to all subscribers to that topic.
-   **Hybrid queues:** Hybrid queues combine elements of both point-to-point and publish-subscribe queues, allowing messages to be delivered to specific recipients or to all subscribers to a topic.

Distributed system messaging queues can be used to enable communication between different components of a distributed system, such as microservices or distributed applications. They can also be used to decouple different parts of the system, allowing each component to operate independently and improving the system's resilience and scalability.

**10\. Distributed unique id generator**
----------------------------------------

A distributed unique ID generator is a system that generates unique identifiers (IDs) that can be used to identify objects or entities in a distributed system. These IDs are typically used to uniquely identify items in a database or to provide a stable identifier for a resource that is accessed over the network.

There are several approaches to generating distributed unique IDs:

-   Using a centralized service
-   Using a [distributed consensus algorithm](https://www.geeksforgeeks.org/computer-organization-architecture/distributed-consensus-in-distributed-systems/)
-   Using timestamps

**11\. Distributes search**
---------------------------

Distributed search refers to the practice of using multiple nodes or servers to index and search large datasets in a distributed system. Distributed search can be used to improve the performance and [scalability](https://www.geeksforgeeks.org/system-design/what-is-scalability/) of search operations, as it allows for parallel processing of search queries and the distribution of data across multiple nodes.

There are several approaches to implementing distributed search, including:

-   **Using a distributed search engine:** A distributed search engine is a search platform that is designed to scale horizontally across multiple nodes. These systems typically use a distributed index to store the data being searched, allowing for parallel processing of search queries.
-   **Using a database with search capabilities:** Some databases, such as MongoDB and Cassandra, have built-in search capabilities that allow for the indexing and searching of data stored in the database.
-   **Using a cloud-based search service:** Cloud-based search services, such as Amazon Elastic search Service and Google Cloud Search, can be used to implement distributed search in a distributed system. These services are typically highly scalable and fault-tolerant.

**12\. Distributed logging services**
-------------------------------------

[Distributed logging](https://www.geeksforgeeks.org/system-design/distributed-logging-for-microservices/) refers to the practice of collecting, storing, and analyzing log data from multiple sources in a [distributed system](https://www.geeksforgeeks.org/computer-networks/what-is-a-distributed-system/). This can be useful for tracking the health and performance of a distributed system, as well as for debugging issues that may arise.

There are several approaches to implementing distributed logging, including:

-   Using a centralized logging service
-   Using a distributed logging system
-   Using a cloud-based logging service

**13\. Distributes task scheduler**
-----------------------------------

A distributed task scheduler is a system that is responsible for scheduling and executing tasks in a distributed system. A task scheduler can be used to automate the execution of tasks at regular intervals, on a specific schedule, or in response to certain events.

There are several approaches to implementing a distributed task scheduler, including:

-   **Using a standalone task scheduler:** A standalone task scheduler is a separate system that is responsible for scheduling and executing tasks in a distributed system. This approach can be simple to implement and allows for flexibility in terms of the types of tasks that can be scheduled.
-   **Using a built-in task scheduler:** Some distributed systems, such as container orchestration platforms or cloud-based serverless platforms, have built-in task schedulers that can be used to schedule tasks within the system.
-   **Using a cloud-based task scheduler:** Cloud-based task schedulers, such as Amazon Simple Notification Service (SNS) or Google Cloud Scheduler, can be used to schedule tasks in a distributed system.






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

## 0. Foundations: Requirements, Estimation & Thinking Framework

### 0.1 What Is System Design?

> Given a business problem and an expected scale, how do you design the complete software system so that it is **scalable, reliable, secure, maintainable, and cost-effective**?

**Use case: "Build a movie website that can handle 1 million users."**

A junior view of the solution:

```
React  ->  Spring Boot  ->  PostgreSQL
```

A system designer's view:

```
                    ┌── CDN
                    │
Users ──► Load Balancer ──► API Gateway
                                 │
                       ┌─────────┴─────────┐
                       ▼                   ▼
                 Movie Service        User Service
                       │                   │
                       ▼                   ▼
                     Redis             PostgreSQL
                       │
                       ▼
                  PostgreSQL
                       │
                       ▼
                     Kafka ──► Background Workers ──► Notifications / Analytics / Search
```

…and then asks the hard questions (answered in [Section 17](#17-worked-example-movie-website-for-1m-users)):

- What happens if PostgreSQL goes down?
- What happens if 100,000 users request the same movie?
- Should we cache movie data, and where should the cache live?
- What happens if Kafka is unavailable?
- How do we prevent duplicate processing?
- How do we scale the API?
- How do we authenticate users?
- How do we monitor the system?
- How do we deploy without downtime?
- How much will it cost?

### 0.2 Requirements

Always clarify requirements **before** drawing boxes.

| Type | Question | Movie website example |
|---|---|---|
| **Functional** | What should the system *do*? | Search movies; open a movie; read reviews; submit a review; admin approves reviews |
| **Non-functional** | How should it *behave*? | 1M users; 99.9% availability; < 200 ms response time; secure; scalable; low cost |

Other non-functional dimensions to ask about: consistency needs (can a review appear 5 s late?), durability, data retention, compliance (GDPR, PCI), geographic spread, read/write ratio, and budget.

**Why it matters:** "99.9% availability" and "< 200 ms" directly drive decisions such as caching, replicas, multi-AZ deployment, and CDN usage. Without them you cannot justify any component.

### 0.3 Back-of-the-Envelope Estimation

**Use case: 1M daily active users on the movie site.**

| Quantity | Assumption | Result |
|---|---|---|
| Requests per day | 1M users × 20 requests | 20M / day |
| Average RPS | 20,000,000 / 86,400 s | ~230 RPS |
| Peak RPS (×5) | Evening spike | ~1,150 RPS |
| Metadata storage | 100K movies × 5 KB | ~500 MB (fits in memory!) |
| Review storage | 50K reviews/day × 1 KB | ~50 MB/day, ~18 GB/year |
| Image bandwidth | 10M poster views × 200 KB | ~2 TB/day, so a **CDN is mandatory** |

**Takeaways:** the working set is tiny (cache everything hot), the read:write ratio is heavily read-biased (cache + read replicas), and images dominate bandwidth (CDN + object storage). A single well-tuned PostgreSQL could handle the writes. The architecture should be *justified by numbers*, not by buzzwords.

### 0.4 The Mental Model: A Repeatable Checklist

Every system design problem can be attacked in this order:

```
Requirements ─► APIs ─► Data ─► Scale ─► Cache ─► Async ─► Reliability ─► Security ─► Observability ─► Cost
```

| Step | Key question | Covered in |
|---|---|---|
| Requirements | What and how well? | §0.2 |
| APIs | What is the contract? | §1.4 |
| Data | Which store, which schema, which keys? | §9 |
| Scale | How do we handle 10× load? | §12 |
| Cache | What can we avoid recomputing? | §8 |
| Async | What doesn't need to block the user? | §10 |
| Reliability | What happens when parts fail? | §11, §13 |
| Security | Who can do what? | §15 |
| Observability | How do we know it's working? | §14 |
| Cost | Is it affordable? | §16 |

**Practice habit:** after drawing any design, pick each component and ask, *"What happens when this fails?"*

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

**Design options:** choice of REST vs gRPC vs GraphQL; retry with exponential backoff and jitter; client-side caching; connection reuse (HTTP/2, keep-alive).

**Security considerations:** TLS everywhere, token-based auth (`Bearer` JWT / OAuth access token), CSRF protection for cookie-based sessions, CORS policy, no secrets in query strings, certificate pinning on mobile.

**Key metrics to monitor:** client-observed latency, request success rate, retry count, payload size.

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

**Key metrics to monitor:** response time (p50 / p95 / p99), failed status code counts, error-code distribution, error budget burn.

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

**Design options:** DNS-based load balancing (round robin, weighted, latency-based, geo-routing), Anycast, low TTL for fast failover vs high TTL for fewer lookups.

**Security considerations:** DNSSEC, DNS over HTTPS/TLS, protection from cache poisoning and DDoS on DNS.

**Key metrics to monitor:** resolution time, cache hit ratio at the resolver, query volume, SERVFAIL rate.

### 1.4 API Design Deep Dive

#### REST and HTTP method semantics

| Method | Purpose | Safe | Idempotent |
|---|---|---|---|
| `GET` | Read | Yes | Yes |
| `POST` | Create / trigger action | No | **No** (unless you add an idempotency key) |
| `PUT` | Replace a resource | No | Yes |
| `PATCH` | Partial update | No | Depends on the operation |
| `DELETE` | Remove | No | Yes |

#### Filtering, sorting, pagination

```
GET /v1/movies?genre=thriller&year=2024&sort=-rating&page[size]=20&page[cursor]=eyJpZCI6MTIzfQ
```

| Pagination | Pros | Cons |
|---|---|---|
| **Offset/limit** (`?page=5&size=20`) | Simple, random page access | Slow on deep pages (`OFFSET 1000000`), inconsistent if data changes |
| **Cursor / keyset** (`WHERE id > :last ORDER BY id LIMIT 20`) | Fast at any depth, stable | No random page jumps |

#### Versioning

| Style | Example | Notes |
|---|---|---|
| URI | `/v1/movies` | Most common, easy to route |
| Header | `Accept: application/vnd.app.v2+json` | Cleaner URLs, harder to test |
| Query | `?version=2` | Rarely recommended |

Rules: only break compatibility in a new major version, support N-1 for a deprecation window, and publish a sunset date (`Sunset` header).

#### Use case: duplicate payments, solved with idempotency

```
POST /payments
Idempotency-Key: abc123
{ "amount": 10000, "currency": "INR" }
```

**Problem:** the network drops the response, so the client retries:

```
Without idempotency:   ₹10,000 charged, ₹10,000 charged, ₹10,000 charged
With idempotency:      ₹10,000 charged once; retries return the stored result
```

**Solution flow**

1. Client generates a unique key (UUID) per *logical* operation and reuses it on retries.
2. Server atomically **claims** the key (unique constraint), executes the operation, stores the response against the key.
3. A retry with the same key returns the stored response instead of re-executing.
4. A concurrent duplicate sees the key as `IN_PROGRESS` and gets `409 Conflict` (or waits).
5. Keys expire after a window (e.g. 24 h).

```java
@PostMapping("/payments")
public ResponseEntity<PaymentResponse> create(
        @RequestHeader("Idempotency-Key") String key,
        @RequestBody @Valid PaymentRequest req) {

    // Atomic claim: INSERT ... with UNIQUE(idempotency_key). Loser of a race gets a duplicate-key exception.
    IdempotencyRecord rec = idempotencyService.claimOrGet(key, hash(req));

    if (rec.isCompleted()) {                       // retry after success
        return ResponseEntity.status(rec.getStatus()).body(rec.getResponse());
    }
    if (rec.isInProgress() && !rec.isOwnedByThisCall()) {   // concurrent duplicate
        return ResponseEntity.status(HttpStatus.CONFLICT).build();
    }
    if (!rec.requestHashMatches(hash(req))) {      // same key, different payload: client bug
        return ResponseEntity.unprocessableEntity().build();
    }

    PaymentResponse response = paymentService.charge(req);     // do the real work once
    idempotencyService.complete(key, 201, response);
    return ResponseEntity.status(HttpStatus.CREATED).body(response);
}
```

Idempotency matters equally for **message consumers** (§10.3) and **webhooks**.

#### Rate limiting and security at the API level

Rate limiting algorithms are in §3.1. Remember to return `429 Too Many Requests` with `Retry-After`, and use per-user, per-IP, and per-API-key buckets. Authentication/authorization specifics are in §15.


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

**Key metrics to monitor** (from the blueprint):

- **Cache miss count** and cache hit ratio
- **Active resources**
- **Total resources** tracked
- Edge bandwidth, origin offload percentage, time-to-first-byte

**Security considerations:** signed URLs/cookies for private content, WAF, DDoS absorption, hotlink protection.

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

**Rate limiting algorithms**

| Algorithm | Idea | Trade-off |
|---|---|---|
| **Token bucket** | Tokens refill at a fixed rate; each request consumes one | Allows bursts; most popular |
| **Leaky bucket** | Requests drain at a constant rate | Smooth output, no bursts |
| **Fixed window counter** | Count per time window | Simple; boundary spikes |
| **Sliding window log** | Store timestamps of requests | Accurate; memory heavy |
| **Sliding window counter** | Weighted blend of current and previous windows | Good accuracy/efficiency balance |

Rate-limit state is typically kept in a shared store such as Redis so every gateway node sees the same counters. Respond with `429` plus `Retry-After`.

**Security considerations:** the gateway is the main trust boundary. Combine WAF rules, bot detection, schema validation, mTLS to upstreams, and secrets kept out of logs.

**Key metrics to monitor:** requests per second per route/client, auth failure rate, throttled request count, gateway latency overhead.

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

**Balancing algorithms**

- **Round robin**: rotate through servers in order.
- **Weighted round robin**: more traffic to higher-capacity servers.
- **Least connections**: pick the server with the fewest active connections.
- **Hash / server stickiness**: consistent hash on client IP, user ID, or session so a client keeps landing on the same server.
- **Random** (and "power of two choices": sample two, pick the less loaded).

**Health checking**: active (probe `/health`) and passive (observe errors/timeouts). Unhealthy nodes are removed and slowly reintroduced (slow start, connection draining).

**Key metrics to monitor** (from the blueprint):

- **Access logs**
- **Status codes**
- **Number of requests**
- **Active connections**
- Also useful: backend response time, unhealthy host count, connection errors.

**Security considerations:** DDoS protection, connection limits, TLS policy, hiding backend topology.

---

## 5. Frontend Servers & Connection Management Layer

### 5.1 Frontend Infrastructure

The **frontend servers** terminate client connections and manage session state for real-time features. The blueprint tags them **#InMemoryConnections** and highlights use for **live streaming and chat**.

**Why in-memory connection state?** Long-lived connections (WebSocket, SSE) are bound to a specific server. That server keeps a map of *which user is connected* in memory, so it can push messages immediately.

**Connection mapping table (from the blueprint)**

| User | Connection |
|---|---|
| `user_1` | `ada_inst_obj` |

**Real-time and delivery protocols**

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

**Key metrics to monitor** (from the blueprint): **bandwidth** and **topics** (subscriptions) per server; also concurrent connections, message latency, reconnect rate.

**Security considerations:** authenticate at handshake, validate origin, per-message authorization, limit message size.

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

**Key metrics to monitor:** ID generation rate, collisions (should be zero), clock drift.

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

**Key metrics to monitor:** lock wait time, lock hold time, contention rate, timeouts/lease expirations.

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

**Key metrics to monitor** (from the blueprint): **disk I/O (if buffering)**, **CPU**, **memory**, **latency**, **bandwidth**.

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

**Key metrics to monitor:** dispatch latency, fan-out factor, undeliverable messages, subscription count.

---

## 8. Caching & Memory Management Layer

### 8.1 In-Memory & Distributed Caches

An **in-memory cache** (tagged **#Fast**) holds hot data in RAM (e.g. Redis, Memcached) to cut latency and database load. A **distributed cache** spreads data across nodes for capacity and availability.

**Caching patterns**

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

**Key metrics to monitor** (from the blueprint): number of items, **cache hit and miss** ratio, **disk and memory usage**; also eviction count, latency, replication lag.

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

### 8.3 Caching Use Cases & Where the Cache Lives

#### Use case: 100,000 users request the same movie

```
Without cache:   100,000 requests ──► PostgreSQL   (connection pool exhausted, latency spikes, possible outage)

With cache:      100,000 requests ──► Redis ──► PostgreSQL (1 query on the first miss)
```

At a 99.9% hit ratio, the database sees ~100 queries instead of 100,000. For a viral movie, also add a **CDN** for the poster/trailer and a **local in-process cache** (Caffeine) for the hottest keys.

#### Cache layers: where should the cache live?

| Layer | Example | Latency | Best for |
|---|---|---|---|
| **Browser / client** | `Cache-Control`, `ETag` | 0 ms | Static assets, rarely changing responses |
| **CDN** | CloudFront, Akamai | ~10-50 ms | Images, video segments, public GET responses |
| **Application (local) cache** | Caffeine, Guava | ~µs | Tiny, hot, read-mostly data; per-instance copies |
| **Distributed cache** | Redis, Memcached | ~1 ms | Shared hot data across all instances |
| **Database cache** | PostgreSQL shared buffers, query cache | n/a | Automatic; tune memory sizing |

Rule of thumb: put the cache **as close to the user as the data's freshness allows**. Local caches are fastest but can become inconsistent between instances; use short TTLs or pub/sub invalidation.

#### Cache-aside in Spring Boot

```java
@Cacheable(cacheNames = "movies", key = "#id", sync = true)   // sync=true => single loader on a stampede
public MovieDto getMovie(long id) {
    return movieRepository.findById(id).map(mapper::toDto).orElseThrow();
}

@CacheEvict(cacheNames = "movies", key = "#movie.id")
public void updateMovie(Movie movie) { movieRepository.save(movie); }
```

```yaml
spring:
  cache:
    type: redis
    redis:
      time-to-live: 10m        # always set a TTL
```

#### Use case: cache stampede on a popular key

**Problem:** the hot key `movie:42` expires and 10,000 concurrent requests all miss and hit PostgreSQL at once.

**Fixes (combine as needed)**

1. **Single-flight / request coalescing**: only one request loads, the others wait (`sync = true`, or a per-key mutex).
2. **Distributed lock** (Redis `SET key value NX PX 5000`) for cross-instance coalescing.
3. **Refresh-ahead**: refresh before expiry.
4. **TTL jitter**: `ttl = base + random(0..20%)`.
5. **Serve stale while revalidating** for non-critical data.

#### Distributed locking with Redis (basic form)

```
SET lock:movie:42 <unique-owner-token> NX PX 5000     # acquire, auto-expires in 5 s
... do the work ...
DEL lock:movie:42 only if value == <unique-owner-token>   # release safely (Lua script)
```

Use a unique token (so you never release someone else's lock), a TTL (so a crashed holder doesn't deadlock), and fencing tokens when correctness is critical (§6.2).

#### Write strategies: summary of when to pick each

- **Cache-aside**: default for read-heavy data like movie catalogue.
- **Write-through**: when reads must always see fresh data (user profile).
- **Write-behind**: counters, view counts, analytics where small loss is acceptable.


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

**Key metrics to monitor** (from the blueprint): query response time, CPU and disk usage, network throughput, active connections, storage used; also replication lag, slow query count, lock waits.

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

**Security considerations** (from the blueprint): **RBAC, data encryption, audit trail**.

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

**Key metrics to monitor** (from the blueprint):

- *Queue*: count of messages, consumption rate, in-transit (waiting for ack) count, queue limit
- *Workers*: compute time, failed count, CPU / disk usage
- *Storage*: compression ratio, storage consumed, object count
- *Broker*: Pub/Sub throughput

### 9.4 Database Deep Dive (Architecture-Level)

#### Indexes and query plans

| Index type | Use |
|---|---|
| **B-tree** (default) | Equality and range queries, sorting |
| **Composite** `(genre, year)` | Queries filtering on a left-prefix of the columns |
| **Covering** (`INCLUDE`) | Index-only scans, no table lookup |
| **Partial** | Index only a subset (`WHERE status='PENDING'`) |
| **Hash / GIN / GiST** | Equality / JSONB & full-text / geospatial |

```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT id, title FROM movies WHERE genre = 'thriller' AND year = 2024 ORDER BY rating DESC LIMIT 20;
-- Seq Scan on movies  => add an index
CREATE INDEX idx_movies_genre_year_rating ON movies (genre, year, rating DESC);
-- Index Scan using idx_movies_genre_year_rating  => fast
```

Things to check: sequential scan vs index scan, estimated vs actual rows (stale statistics), sort spills to disk, and the N+1 query problem (JPA lazy loading; fix with `JOIN FETCH` or batch fetching). Every index speeds reads but slows writes and uses storage.

#### Transactions and isolation levels

| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| **Read Committed** (PostgreSQL default) | No | Possible | Possible |
| Repeatable Read | No | No | Possible in the SQL standard (PostgreSQL's snapshot isolation prevents it, but allows *write skew*) |
| Serializable | No | No | No (may abort with serialization failure; retry) |

**Use case: overselling the last ticket.** Two users buy the last seat simultaneously. Fix with one of:

```sql
-- Pessimistic: lock the row
SELECT * FROM seats WHERE id = 7 FOR UPDATE;

-- Optimistic: version check (JPA @Version)
UPDATE seats SET status='SOLD', version=version+1 WHERE id=7 AND version=3;   -- 0 rows updated => retry/fail

-- Atomic conditional update
UPDATE seats SET status='SOLD' WHERE id=7 AND status='AVAILABLE';
```

Also know about deadlocks (always acquire locks in a consistent order), lock timeouts, and long transactions that bloat MVCC and block vacuum.

#### Connection pooling

Each DB connection is expensive (a process + memory in PostgreSQL). Use a pool (HikariCP), and never size it by "number of threads."

```
pool size ≈ (cpu_cores × 2) + effective_spindle_count     # rule of thumb starting point
```

**Use case: 50 API pods × 50 connections = 2,500 connections** against a database that supports ~200. Fix: smaller pools (e.g. 10 per pod), **PgBouncer** in transaction mode, and shorter transactions.

#### Read replicas and replication lag

```
Writes ──► Primary ══(async replication)══► Replica 1, Replica 2 ◄── Reads
```

**Problem (read-your-writes):** a user posts a review, then immediately reloads and doesn't see it because the replica lags.
**Fixes:** route that user's reads to the primary for a few seconds after a write; use session/sticky routing; track the write's LSN and read from a replica that has caught up; or return the created object from the write response.

#### Partitioning vs sharding

| | Partitioning | Sharding |
|---|---|---|
| Scope | Split a table inside **one** database server | Split data across **multiple** servers |
| Purpose | Query pruning, easier archival/deletion (e.g. monthly partitions of `events`) | Scale writes and storage beyond one machine |
| Complexity | Low | High (cross-shard joins, transactions, rebalancing) |

Exhaust vertical scaling, caching, replicas, and partitioning **before** sharding.

#### Choosing a database: "Why PostgreSQL here instead of Cassandra?"

| Database | Choose when | Avoid when |
|---|---|---|
| **PostgreSQL / MySQL** | Strong consistency, joins, transactions, moderate scale, complex queries (users, orders, payments, reviews) | Multi-million writes/sec across regions |
| **MongoDB** | Flexible/nested documents, rapidly evolving schema (movie metadata, catalogs, CMS) | Heavy multi-document transactions and relational integrity |
| **DynamoDB** | Predictable low-latency key-value access at massive scale, serverless ops, known access patterns | Ad-hoc queries and joins; access patterns unknown |
| **Cassandra** | Huge write throughput, multi-region active-active, time-series/event data, query-by-partition-key | Strong consistency, joins, ad-hoc queries, small teams |
| **Redis** | Caching, sessions, counters, leaderboards, rate limits | Primary store for critical data (unless persistence is carefully designed) |

**Example reasoning:** for the movie site, *reviews and users* → PostgreSQL (relational, transactional, moderate volume); *movie metadata* → PostgreSQL + Redis (or MongoDB); *view-event firehose* → Kafka → Cassandra or a column store; *search* → Elasticsearch.

#### Use case: what happens if PostgreSQL goes down?

1. **Detect:** health checks and monitoring fail; alert fires.
2. **Fail over:** with a managed HA setup (Multi-AZ, Patroni, RDS), a synchronous/async standby is promoted (typically 30-120 s). Applications reconnect via a stable endpoint/DNS or proxy.
3. **Degrade gracefully:** serve reads from cache and replicas; queue writes (e.g. reviews) to Kafka for later processing; show a friendly "temporarily read-only" mode.
4. **Protect:** circuit breakers prevent connection storms; pool timeouts fail fast.
5. **Recover:** restore from backup/PITR if data is corrupted; validate RPO (see §13).


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

**Key metrics to monitor:** delivery rate, open/click rate, bounce rate, queue lag, per-channel latency.

### 10.2 Specialized Microservices

#### Recommendation Service
- Suggests items (videos, products, people) personalised to a user.
- **Filtering approaches (from the blueprint)**: **content-based** (similar item attributes) and **collaborative** (similar users' behaviour). Modern systems combine both with embeddings and ranking models.
- Pipeline: candidate generation → ranking → re-ranking (diversity, business rules).
- **Key metrics:** click-through rate, conversion, coverage, latency.

#### ML Processing Service
- Runs model inference and (offline) training or feature pipelines triggered by events: moderation, tagging, transcription, ranking features, fraud scoring.
- Considerations: batch vs real-time inference, feature store, model versioning, A/B testing, GPU capacity, fallbacks when a model is unavailable.
- **Key metrics:** inference latency, throughput, model accuracy/drift, resource utilisation.

#### Search Service
- Powers full-text and faceted search via an **inverted index** (Elasticsearch, OpenSearch, Solr).
- Fed asynchronously from the pub/sub stream so the index stays near real-time.
- Features: tokenization, stemming, stop words, typo tolerance, relevance ranking (BM25 + signals), autocomplete.
- **Key metrics:** query latency, indexing lag, zero-result rate.

#### Analytics Service (Event Logging, Metrics, Usage Tracking)
- Includes the blueprint's **Log Processing Service**: collects events and logs, parses and enriches them, and stores them for dashboards and reporting.
- Pipeline: collectors → stream processing (Flink/Spark) → data lake/warehouse (OLAP / column store) → BI dashboards.
- Supports usage tracking and metering that feeds billing and quotas.
- **Key metrics:** ingestion rate, end-to-end data freshness, event loss rate.

#### Payment Charge Service (Third-Party Integrations & Gateway Processing)
- Charges customers through **third-party banking / payment providers**.
- **Idempotency is essential**: every charge request carries an **idempotency key** so retries never double-charge.
- **Typical failure reasons (from the blueprint)**: expired, blocked, or invalid card; service down; insufficient balance.
- Reliability patterns: state machine for payment status (`created → authorized → captured → settled / failed / refunded`), retries with backoff for transient errors, **reconciliation** jobs against provider reports, webhooks for async status updates, saga/compensation for multi-step flows.
- **Security:** PCI-DSS scope reduction (use tokenization, never store raw card data), encryption, strict RBAC, full audit trail, fraud checks.
- **Key metrics:** authorisation success rate, decline reasons, latency to provider, reconciliation mismatches.


### 10.3 Messaging & Event-Driven Architecture Deep Dive

#### Why asynchronous messaging

```
Order Service ──► Kafka ──┬──► Email Service
                          ├──► Inventory Service
                          └──► Analytics Service
```

The Order Service publishes `OrderPlaced` once. Consumers react independently, so services no longer call each other synchronously for everything. A slow email provider no longer slows checkout.

#### Core Kafka concepts

| Concept | Meaning |
|---|---|
| **Producer** | Publishes records to a topic |
| **Topic** | Named, append-only log of records |
| **Partition** | Unit of parallelism and ordering; a topic is split into N partitions |
| **Offset** | Position of a record within a partition; consumers track it |
| **Consumer group** | Set of consumers sharing a topic; each partition is read by exactly one consumer in the group |
| **Broker / replication** | Servers storing partitions; each partition has a leader and follower replicas |

```
Topic "orders" (3 partitions)
P0: [0][1][2][3]...  ──┐
P1: [0][1][2]...     ──┼── Consumer Group A: C1←P0, C2←P1, C3←P2  (scale up to #partitions)
P2: [0][1][2][3]...  ──┘── Consumer Group B (analytics): reads all independently
```

**Ordering:** guaranteed only **within a partition**. Use the entity ID as the key (`key = orderId`) so all events for one order go to the same partition. Maximum parallelism equals the partition count.

#### Delivery semantics

| Semantics | Meaning | How |
|---|---|---|
| **At-most-once** | May lose, never duplicate | Commit offset before processing |
| **At-least-once** | Never lose, may duplicate | Process, then commit offset (default choice) |
| **Exactly-once (effect)** | Processed effect happens once | Idempotent producer + transactions in Kafka, **plus idempotent consumers/DB constraints** for external side effects |

Exactly-once delivery across systems is not truly achievable; you get **exactly-once *processing effect*** through idempotency.

#### Use case: preventing duplicate processing

**Problem:** the consumer processes `OrderPlaced` (sends email), crashes before committing the offset, and receives the message again, so the customer gets two emails or is charged twice.

**Solution: idempotent consumer**

```java
@KafkaListener(topics = "orders", groupId = "email-service")
@Transactional
public void onOrderPlaced(OrderPlaced event) {
    // INSERT into processed_events(event_id) with a UNIQUE constraint
    if (!processedEvents.markProcessedIfNew(event.eventId())) {
        return;                                  // duplicate: skip safely
    }
    emailService.sendConfirmation(event);
}
```

Other options: natural idempotent operations (`SET status='SHIPPED'`), version checks, and upserts.

#### Retry and dead-letter queue (DLQ)

```
orders ──► Consumer ──fails──► orders.retry.5s ──► orders.retry.1m ──► orders.DLQ
```

- **Transient failures** (timeout, deadlock): retry with exponential backoff.
- **Poison messages** (malformed, bug): after N attempts, route to the DLQ with error metadata, alert, and replay after the fix.
- Never block the whole partition on one bad message; use non-blocking retry topics.

#### Reliable publishing: the Transactional Outbox pattern

**Problem (dual write):** save the order in PostgreSQL *and* publish to Kafka. If the app crashes between the two, the DB says "placed" but no event is ever sent (or the reverse).

**Solution:**

```
BEGIN;
  INSERT INTO orders ...;
  INSERT INTO outbox (event_id, topic, payload) ...;   -- same transaction
COMMIT;

Relay / CDC (Debezium) reads the outbox ──► publishes to Kafka ──► marks sent
```

Atomicity comes from the DB transaction; delivery is at-least-once, so consumers must be idempotent.

#### Use case: what if Kafka is unavailable?

- **Producer side:** buffer in the outbox table (events are safe in the DB and are published when Kafka returns); use producer retries, `acks=all`, `enable.idempotence=true`, and timeouts.
- **Core user flow stays up:** checkout succeeds because publishing is decoupled via the outbox; non-critical features (email, analytics) are delayed, not lost.
- **Broker side:** replication factor 3, `min.insync.replicas=2`, multi-AZ brokers.
- **Consumer side:** consumers pause and retry; lag alerts fire.
- **Back-pressure:** monitor outbox size; shed non-critical events if it grows without bound.

#### Choosing a broker

| Broker | Model | Strengths | Typical use |
|---|---|---|---|
| **Kafka** | Distributed commit log, pull-based | Huge throughput, replay, retention, partition ordering, stream processing | Event streaming, analytics, event sourcing, CDC |
| **RabbitMQ** | Smart broker, push-based, exchanges/queues | Flexible routing, per-message ack, priorities, low latency | Task queues, RPC-style workflows |
| **ActiveMQ** (Classic / Artemis) | JMS broker | JMS compatibility, enterprise Java integration, durable subscriptions | Legacy / JMS-based enterprise integration |
| **Azure Service Bus** | Managed queues & topics | Sessions (ordered per key), dead-lettering, scheduled messages, transactions, managed ops | Azure-native enterprise workflows |

Rule of thumb: **Kafka** when you need a replayable event log at scale; **queue-style brokers** (RabbitMQ, ActiveMQ, Service Bus) for work distribution with per-message handling and rich routing.

---

## 11. Distributed Systems Fundamentals

### 11.1 CAP Theorem and Consistency Models

During a **network partition**, a system must choose between:

- **Consistency (C):** every read sees the latest write.
- **Availability (A):** every request gets a (non-error) response.
- **Partition tolerance (P):** the system keeps working despite lost messages between nodes (unavoidable in real networks).

So the real choice is **CP vs AP** when a partition occurs.

| Type | Behaviour in a partition | Examples | Use for |
|---|---|---|---|
| **CP** | Rejects some requests to stay consistent | ZooKeeper, etcd, HBase, PostgreSQL primary-only writes | Payments, inventory, locks, config |
| **AP** | Keeps serving, may return stale data, reconciles later | Cassandra, DynamoDB (eventual), DNS | Feeds, likes, catalogues, shopping carts |

**PACELC extension:** even without partitions there is a trade-off between **L**atency and **C**onsistency.

**Consistency spectrum:** strong (linearizable) → sequential → causal → read-your-writes → **eventual**.

**Use case: "like" counter vs bank balance.** A like count can be eventually consistent (AP; a few seconds stale is fine). A bank balance needs strong consistency (CP; a stale balance allows overdraft).

### 11.2 Eventual Consistency in Practice

- Replicas converge via anti-entropy, read repair, and hinted handoff.
- Conflict resolution: last-write-wins (LWW), vector clocks, CRDTs, or application-level merge.
- Design UX around it: optimistic UI updates, "processing" states, idempotent operations.

### 11.3 Distributed Transactions

A single DB transaction cannot span multiple services. Options:

| Approach | How | Trade-off |
|---|---|---|
| **Two-Phase Commit (2PC / XA)** | Coordinator asks all to *prepare*, then *commit* | Strong atomicity, but blocking, slow, coordinator is a risk; rarely used across microservices |
| **Saga (choreography)** | Each service does a local transaction and emits an event; compensating actions undo on failure | Eventually consistent, no locks; harder to reason about |
| **Saga (orchestration)** | A central orchestrator drives the steps and compensations | Clear flow, single place to monitor; orchestrator logic to maintain |
| **Outbox + idempotent consumers** | Reliable event publication | Foundation for sagas (§10.3) |

**Use case: order placement saga**

```
1. Order Service      : create order (PENDING)
2. Payment Service    : charge card                 ── fails ──► 5. Order: cancel (compensate)
3. Inventory Service  : reserve stock               ── fails ──► refund payment, then cancel order
4. Shipping Service   : schedule shipment
5. Order Service      : mark CONFIRMED
```

Each step needs an **idempotent** action and a **compensating action** (refund, release stock).

### 11.4 Leader Election, Consensus & Replication

- **Leader election** picks a single coordinator (to avoid split-brain). Implemented with ZooKeeper/etcd/Consul leases or built-in Raft.
- **Consensus (Raft, Paxos, ZAB)**: nodes agree on a value/log order as long as a **majority (quorum)** is alive. A 3-node cluster tolerates 1 failure; 5 nodes tolerate 2.
- **Split-brain** (two leaders) is prevented with quorums, fencing tokens, and lease expiry.
- **Replication:** single-leader, multi-leader, leaderless (quorum `R + W > N`). See §9.1.

### 11.5 Resilience Patterns

| Pattern | Purpose | Notes |
|---|---|---|
| **Timeout** | Never wait forever | Set on every network call; budget timeouts down the call chain |
| **Retry with exponential backoff + jitter** | Survive transient errors | Only retry idempotent operations; cap attempts |
| **Circuit breaker** | Stop calling a failing dependency | States: Closed → Open → Half-Open |
| **Bulkhead** | Isolate resources per dependency | Separate thread pools/semaphores so one slow service can't exhaust all threads |
| **Fallback** | Provide a degraded result | Cached/default data |
| **Rate limiter / load shedding** | Protect capacity | Reject early rather than collapse |
| **Backpressure** | Slow producers when consumers can't keep up | Bounded queues, reactive streams, consumer lag alerts |

#### Use case: Service A → B → C, and C takes 30 seconds

```
Client ─► A ─► B ─► C (slow: 30 s)
```

**What goes wrong:** B's threads wait on C, B's thread pool fills, A's threads wait on B, the whole chain stalls (a **cascading failure**), retries then amplify the load (**retry storm**).

**Defence in depth**

1. **Timeout** on A→B and B→C (e.g. 2 s for C, 3 s for B, so the caller times out *after* the callee).
2. **Retry** at most once or twice with backoff and jitter, for idempotent calls only.
3. **Circuit breaker** around C: after 50% failures in a window, open and fail fast.
4. **Bulkhead** so C's slowness only consumes C's allotted threads.
5. **Fallback** (cached data, default value, partial response).
6. **Async processing** if the operation is inherently slow: return `202 Accepted` with a job ID; process via queue; client polls or receives a webhook/SSE.

```java
@Service
public class CClient {

    @CircuitBreaker(name = "serviceC", fallbackMethod = "fallback")
    @Bulkhead(name = "serviceC", type = Bulkhead.Type.THREADPOOL)
    @TimeLimiter(name = "serviceC")
    @Retry(name = "serviceC")
    public CompletableFuture<Result> call(Request r) {
        return CompletableFuture.supplyAsync(() -> restClient.post(r));
    }

    private CompletableFuture<Result> fallback(Request r, Throwable t) {
        return CompletableFuture.completedFuture(Result.degraded());
    }
}
```

```yaml
resilience4j:
  timelimiter.instances.serviceC.timeoutDuration: 2s
  retry.instances.serviceC: { maxAttempts: 2, waitDuration: 200ms, enableExponentialBackoff: true }
  circuitbreaker.instances.serviceC: { slidingWindowSize: 20, failureRateThreshold: 50, waitDurationInOpenState: 30s }
```

### 11.6 Race Conditions in Distributed Systems

| Problem | Example | Fix |
|---|---|---|
| **Lost update** | Two admins edit the same review; the last write silently wins | Optimistic locking (`@Version`), ETag + `If-Match` |
| **Double processing** | Two workers pick the same job | Row lock `SELECT ... FOR UPDATE SKIP LOCKED`, lease with TTL, idempotency |
| **Check-then-act** | `if (stock>0) stock--` run concurrently | Atomic conditional update, `UPDATE ... WHERE stock > 0` |
| **Duplicate request** | Retry from the client | Idempotency key |
| **Stale lock holder** | A paused JVM (GC) resumes after the lock expired | Fencing tokens |
| **Clock skew** | Ordering by wall-clock timestamps | Logical clocks, DB sequences, Snowflake with safeguards |

---

## 12. Scalability

### 12.1 Vertical vs Horizontal Scaling

| | Vertical (scale up) | Horizontal (scale out) |
|---|---|---|
| What | 4 CPU → 16 CPU, more RAM | More instances behind a load balancer |
| Pros | Simple, no code change | Near-linear capacity, fault tolerance |
| Cons | Hardware ceiling, downtime, single point of failure, expensive | Needs statelessness, distributed-system complexity |

```
              Load Balancer
             /      |       \
          API       API       API      (identical, stateless)
```

### 12.2 Stateless Services

A service is **stateless** when any instance can serve any request. To get there:

- Keep sessions in Redis or in signed tokens (JWT), **not** in server memory.
- Store uploads in object storage, not local disk.
- Avoid in-memory-only state that matters (or treat it as cache).
- Externalise configuration.

**Use case:** the shopping cart lives in the JVM session. A deploy or autoscale event kills it. Move it to Redis (key = `cart:{userId}`) and any instance can serve the user.

### 12.3 Autoscaling

- **Horizontal Pod Autoscaler (Kubernetes)** or cloud autoscaling groups scale on CPU, memory, requests per pod, queue depth, or custom metrics.
- Scale **out fast, in slowly**; set min/max bounds; pre-warm for known peaks (premieres, sales).
- Pitfalls: slow startup (JVM warm-up), downstream DB limits (more pods = more connections), thundering herds on scale-out.
- Queue-driven workloads scale on **consumer lag**.

### 12.4 Scaling the Data Tier

Typical progression: **tune queries and indexes → cache → read replicas → partitioning → vertical upsizing → sharding / purpose-built stores.** (Details in §8, §9.)

### 12.5 Scaling Summary Checklist

| Tier | Techniques |
|---|---|
| Static content | CDN, long TTLs, versioned URLs |
| API | Stateless instances, load balancer, autoscaling |
| Reads | Cache, read replicas, denormalization |
| Writes | Queue + batch, partitioning, sharding, CQRS |
| Heavy work | Async workers, message queues |

**Use case: scale the API for the movie site** from 230 RPS to 1,150 RPS peak. Run 4-6 stateless Spring Boot pods behind an LB (each ~300 RPS with headroom), cache hot movies in Redis, and put posters on a CDN. Autoscale on CPU 60% and p95 latency.

---

## 13. Reliability & Availability

### 13.1 Core Concepts

| Term | Meaning |
|---|---|
| **High availability (HA)** | Minimal downtime via redundancy and automatic failover |
| **Fault tolerance** | The system continues operating correctly despite component failure |
| **Disaster recovery (DR)** | Restoring service after a major loss (region outage, data corruption) |
| **Backup / restore** | Periodic copies + point-in-time recovery (PITR); **test restores regularly** |
| **Multi-AZ** | Redundancy across data centres in one region (protects from a data-centre failure) |
| **Multi-region** | Redundancy across regions (protects from a regional outage; adds cost and data-consistency complexity) |
| **Health checks** | Liveness (restart if dead), readiness (remove from LB if not ready), startup |
| **Failover** | Automatically switching traffic to a standby |
| **Graceful degradation** | Reduced functionality instead of total failure |

### 13.2 SLA, SLO, SLI

| Term | Definition | Example |
|---|---|---|
| **SLI** (indicator) | The measured metric | % of requests with status < 500 and latency < 200 ms |
| **SLO** (objective) | Internal target for the SLI | 99.9% over 30 days |
| **SLA** (agreement) | Contractual promise, with penalties | 99.5% or service credits |
| **Error budget** | `1 − SLO` allowed failure | 0.1% ≈ 43 minutes/month; spend it on releases and experiments |

**Availability "nines"**

| Availability | Downtime / year | Downtime / month |
|---|---|---|
| 99% | 3.65 days | ~7.3 h |
| 99.9% | 8.76 h | ~43.8 min |
| 99.99% | 52.6 min | ~4.4 min |
| 99.999% | 5.26 min | ~26 s |

**Composite availability:** serial dependencies multiply. Three components at 99.9% each give ~99.7%. Redundant parallel components improve it: two 99.9% replicas ≈ 99.9999% (if failures are independent).

### 13.3 RTO and RPO

```
 last good backup        disaster          service restored
        │◄──── RPO ────────►│◄───── RTO ───────►│
```

- **RPO (Recovery Point Objective):** maximum acceptable **data loss**. *RPO = 5 minutes* means you may lose at most the last 5 minutes of data, so you need continuous replication or WAL archiving at least that frequent.
- **RTO (Recovery Time Objective):** maximum acceptable **downtime**. *RTO = 1 hour* means after a disaster, service must be back within an hour.

| DR strategy | RTO | RPO | Cost |
|---|---|---|---|
| Backup & restore | Hours | Hours | $ |
| Pilot light | Tens of minutes | Minutes | $$ |
| Warm standby | Minutes | Seconds-minutes | $$$ |
| Active-active multi-region | ~Zero | ~Zero | $$$$ |

### 13.4 Reliability Practices

- Eliminate single points of failure (SPOF): multiple LBs, instances, AZs, DB replicas.
- Automated failover, regularly **tested** (game days, chaos engineering).
- Timeouts, retries, circuit breakers (§11.5), rate limiting and load shedding.
- Safe deployments: canary, blue-green, feature flags, quick rollback (§16).
- Runbooks, on-call, blameless post-mortems.

---

## 14. Observability

### 14.1 Three Pillars

```
              Observability
            /       |        \
         Logs     Metrics    Traces
```

| Pillar | Answers | Tools |
|---|---|---|
| **Logs** | *What happened?* (discrete events) | ELK/EFK (Elasticsearch, Logstash/Fluentd, Kibana), Loki, Splunk |
| **Metrics** | *How is it behaving over time?* (numeric time series) | Prometheus + Grafana, Datadog, New Relic |
| **Traces** | *Where did time go across services?* (request path) | OpenTelemetry, Jaeger, Zipkin, Tempo, Datadog APM |
| **Alerts** | *Who should act now?* | Alertmanager, PagerDuty, Opsgenie |

**OpenTelemetry (OTel)** is the vendor-neutral standard for generating and exporting all three signals; instrument once, send to any backend.

### 14.2 What to Measure

- **RED** (for services): **R**ate, **E**rrors, **D**uration.
- **USE** (for resources): **U**tilisation, **S**aturation, **E**rrors.
- **Four golden signals:** latency, traffic, errors, saturation.
- Business metrics: signups, orders/min, payment success rate.

### 14.3 Spring Boot Setup

```yaml
management:
  endpoints.web.exposure.include: health,prometheus,info
  endpoint.health.probes.enabled: true      # /actuator/health/liveness & readiness
  tracing.sampling.probability: 0.1          # sample 10% of traces
logging.pattern.level: "%5p [${spring.application.name},%X{traceId},%X{spanId}]"
```

Use **structured JSON logs** with `traceId`, `userId` (not PII), `requestId`; Micrometer for metrics; propagate trace context (W3C `traceparent`) across HTTP and Kafka.

### 14.4 Use Case: Diagnosing "The movie page is slow"

1. **Alert** fires: p95 latency of `GET /movies/{id}` > 500 ms (SLO burn).
2. **Metrics dashboard:** latency spike started at 14:05 and DB connection pool usage is at 100%.
3. **Trace:** most of the time is spent in `SELECT ... FROM reviews` (800 ms) inside Movie Service.
4. **Logs** (filter by `traceId`): a new release started loading all reviews without pagination.
5. **Fix:** add pagination and an index, roll back or hot-fix; verify recovery via the same dashboard.

### 14.5 Alerting Principles

Alert on **symptoms** (user impact, SLO burn rate), not every cause; make each alert actionable with a runbook link; avoid alert fatigue; use multi-window burn-rate alerts; page for urgent, ticket for non-urgent.

---

## 15. Security (System-Level)

### 15.1 Authentication ≠ Authorization

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| Question | **Who are you?** | **What are you allowed to do?** |
| Mechanisms | Passwords + MFA, OIDC, API keys, mTLS | RBAC, ABAC, scopes, policies |
| Failure code | `401 Unauthorized` | `403 Forbidden` |

**Use case:** a logged-in user (authenticated) requests `GET /orders/999`, which belongs to someone else. Authentication passes; authorization must still reject (this class of bug is **BOLA/IDOR**, the most common API vulnerability). Always check ownership in the service layer.

### 15.2 OAuth 2.0, OIDC, JWT

| Standard | Purpose |
|---|---|
| **OAuth 2.0** | *Delegated authorization*: issues **access tokens** with scopes |
| **OpenID Connect (OIDC)** | Identity layer on OAuth 2.0: adds the **ID token** and `/userinfo` (authentication) |
| **JWT** | Compact signed token format (`header.payload.signature`) used for access/ID tokens |

**Recommended flows:** Authorization Code + **PKCE** for web/mobile apps; Client Credentials for service-to-service; avoid the Implicit and Password flows.

```
User ─► App ─► Authorization Server (Keycloak/Auth0/Entra ID) ─► code ─► App exchanges code (+PKCE) ─► access token + refresh token
App ─► API Gateway (validates JWT signature, expiry, audience, scopes) ─► services
```

**JWT best practices:** short-lived access tokens (5-15 min), rotating refresh tokens, verify `iss`, `aud`, `exp`, and signature with the JWKS; never put secrets/PII in the payload (it's only encoded, not encrypted); have a revocation strategy (short TTL, deny-list, token introspection).

```java
@Bean
SecurityFilterChain api(HttpSecurity http) throws Exception {
    return http
        .authorizeHttpRequests(a -> a
            .requestMatchers("/actuator/health/**").permitAll()
            .requestMatchers(HttpMethod.POST, "/reviews/*/approve").hasRole("ADMIN")
            .anyRequest().authenticated())
        .oauth2ResourceServer(o -> o.jwt(Customizer.withDefaults()))
        .build();
}
```

### 15.3 RBAC vs ABAC

| | RBAC | ABAC |
|---|---|---|
| Decision based on | User's **role** (`ADMIN`, `EDITOR`) | **Attributes** of user, resource, action, environment |
| Example | Admins can approve reviews | An editor can approve reviews **only** for their region, during business hours, if the review is `PENDING` |
| Pros / cons | Simple, can cause role explosion | Fine-grained, flexible; more complex (use a policy engine like OPA/Cedar) |

### 15.4 Transport, Data & Secrets

- **TLS** everywhere (1.2+/1.3); **mTLS** between services in a mesh; HSTS; managed certificates with auto-renewal.
- **Encryption at rest** (disk/DB/object store, KMS-managed keys) and **field-level encryption** for sensitive columns; hash passwords with **bcrypt / Argon2** (never reversible encryption).
- **Secrets management:** Vault, AWS Secrets Manager, Azure Key Vault, Kubernetes secrets with external-secrets; never in Git or images; rotate regularly; least-privilege IAM.

### 15.5 API & Application Security

- **OWASP Top 10 / API Top 10:** broken access control (BOLA), injection (use parameterised queries), cryptographic failures, insecure design, misconfiguration, vulnerable dependencies (SCA scanning), SSRF, mass assignment, excessive data exposure.
- Input validation and output encoding; CORS allow-lists; CSRF protection for cookie sessions; security headers (CSP, X-Content-Type-Options).
- **Rate limiting** and bot protection (§3.1) to stop brute force and scraping.
- Audit logging, anomaly detection, dependency/container image scanning in CI.

### 15.6 Zero Trust

"Never trust, always verify": no implicit trust from network location. Authenticate and authorize **every** request (user and service identity), enforce least privilege, segment networks, use short-lived credentials, and continuously monitor.

---

## 16. Infrastructure, Deployment & Cost

### 16.1 Containers and Orchestration

- **Docker:** package the app and dependencies as an immutable image (use multi-stage builds, a slim JRE base, non-root user, and Spring Boot layered jars).
- **Kubernetes:** schedules containers, self-heals, scales, and rolls out. Key objects: **Deployment**, **Service**, **Ingress**, **ConfigMap/Secret**, **HPA**, **PodDisruptionBudget**; configure resource requests/limits and readiness/liveness probes.

```yaml
readinessProbe: { httpGet: { path: /actuator/health/readiness, port: 8080 }, periodSeconds: 5 }
livenessProbe:  { httpGet: { path: /actuator/health/liveness,  port: 8080 }, periodSeconds: 10 }
resources: { requests: { cpu: "500m", memory: "768Mi" }, limits: { memory: "1Gi" } }
```

### 16.2 Cloud Building Blocks

| Need | AWS | Azure | GCP |
|---|---|---|---|
| Compute | EC2, ECS/EKS, Lambda | VMs, AKS, Functions | GCE, GKE, Cloud Run |
| Managed DB | RDS/Aurora, DynamoDB | Azure SQL/PostgreSQL, Cosmos DB | Cloud SQL, Spanner |
| Cache | ElastiCache | Azure Cache for Redis | Memorystore |
| Messaging | MSK, SQS/SNS | Service Bus, Event Hubs | Pub/Sub |
| Object storage / CDN | S3 / CloudFront | Blob / Front Door | GCS / Cloud CDN |

Infrastructure as Code (**Terraform**, CloudFormation, Bicep) keeps environments reproducible and reviewable.

### 16.3 CI/CD

```
Commit ─► Build & unit test ─► Static analysis / SCA / secret scan ─► Container image ─► Integration tests
      ─► Deploy to staging ─► Smoke/E2E ─► Progressive production rollout ─► Monitor ─► Auto-rollback
```

GitOps (Argo CD/Flux) makes Git the source of truth for deployments.

### 16.4 Use Case: Deploying Without Downtime

| Strategy | How | Pros | Cons |
|---|---|---|---|
| **Rolling update** | Replace pods gradually | Default, cheap | Old and new versions coexist; slow rollback |
| **Blue-green** | Run full new stack (green), switch traffic from blue | Instant rollback | 2× capacity during cutover |
| **Canary** | Send 1% → 10% → 100% of traffic to the new version, watching metrics | Limits blast radius | Needs good metrics and routing |
| **Feature flags** | Deploy dark, enable per cohort | Decouples deploy from release | Flag hygiene |

**Zero-downtime requirements:** readiness probes, graceful shutdown (`server.shutdown=graceful`, drain connections, `preStop` hook), backward-compatible APIs and events, and **expand/contract database migrations**:

1. *Expand:* add the new nullable column (old code still works).
2. Deploy code that writes both and reads the new one.
3. Backfill data.
4. *Contract:* drop the old column in a later release.

### 16.5 Cost Optimization

| Lever | Example |
|---|---|
| Right-size | Match instance size to real CPU/memory usage (most are over-provisioned) |
| Commitments | Reserved Instances / Savings Plans for steady load; Spot instances for stateless/batch work |
| Autoscale | Scale in at night; scale to zero for dev environments |
| Data tiering | Lifecycle policies to cold storage (§9.2); compress and expire logs |
| CDN & caching | Offload origin bandwidth and DB reads |
| Egress and cross-AZ traffic | Keep chatty services in the same zone; egress is often the hidden cost |
| Observability cost | Sample traces, set log retention, drop high-cardinality metrics |
| Managed vs self-hosted | Count engineering time, not only the cloud bill |

**Cost estimate habit:** compute `unit cost × quantity` per component (compute, DB, cache, CDN egress, storage, messaging, observability) and calculate **cost per 1,000 requests / per active user**. Treat cost as a non-functional requirement.

---

## 17. Worked Example: Movie Website for 1M Users

### 17.1 Requirements Recap

- **Functional:** search, view movie, read/submit reviews, admin approval, notifications.
- **Non-functional:** 1M users, 99.9% availability, p95 < 200 ms, secure, cost-aware.
- **Estimates:** ~1,150 RPS peak, ~2 TB/day image bandwidth (§0.3).

### 17.2 Architecture

```
Users ─► DNS ─► CDN (posters, JS/CSS, trailers)
                  │ (dynamic)
                  ▼
        WAF / Load Balancer (multi-AZ) ─► API Gateway (AuthN, rate limit, routing)
                  │
     ┌────────────┼──────────────┬───────────────┐
     ▼            ▼              ▼               ▼
 Movie Svc    Review Svc     User Svc       Search Svc ─► Elasticsearch
     │            │              │
   Redis      PostgreSQL      PostgreSQL
     │         (primary + 2 replicas, Multi-AZ)
     ▼
 PostgreSQL            Review Svc ─► Outbox ─► Kafka ─► Notification / Analytics / Search-indexer workers
```

### 17.3 Failure Analysis: "What Happens When Each Component Fails?"

| Component fails | Impact | Mitigation |
|---|---|---|
| **CDN edge node** | Slower image loads | Multi-edge/multi-CDN; origin fallback |
| **Load balancer node** | None if multi-node | Active-active LBs, health checks |
| **An API instance** | Some in-flight requests fail | Stateless pods, readiness probes, retries on idempotent calls, autoscaling |
| **Redis** | Cache misses; DB load jumps | Redis replicas + auto-failover; **circuit breaker + request coalescing** so the DB is protected; local Caffeine fallback |
| **PostgreSQL primary** | Writes fail during failover | Multi-AZ auto-failover (<2 min), read-only degraded mode, queue writes via Kafka, PITR for corruption |
| **PostgreSQL replica** | Reduced read capacity | Route to other replicas/primary, rebuild replica |
| **Kafka** | Async features delayed | Outbox keeps events; 3-broker multi-AZ cluster; lag alerts |
| **A consumer (worker)** | Lag grows | Consumer group rebalances; autoscale on lag; idempotent processing |
| **Search (Elasticsearch)** | Search unavailable | Fall back to basic DB `LIKE`/trigram search or cached popular results; replicas and shards |
| **Auth server** | New logins fail | Already-issued JWTs still validate (JWKS cached); HA deployment |
| **Entire AZ** | One third of capacity lost | Spread across 3 AZs; N+1 capacity |
| **Entire region** | Total outage | DR plan matching RTO/RPO; warm standby or multi-region for stricter SLOs |
| **Third-party (email, payment)** | Degraded feature | Timeouts, circuit breakers, retries via queue, idempotent charge calls |

### 17.4 Use Case: "Design Netflix" Practice Walk-Through

Apply the checklist from §0.4:

1. **Requirements:** browse catalogue, play video with adaptive quality, personalised home page, resume playback, multi-device; 99.99% availability; global.
2. **APIs:** `GET /v1/titles`, `GET /v1/titles/{id}`, `POST /v1/playback/sessions`, `PUT /v1/profiles/{id}/progress` (idempotent).
3. **Data:** catalogue (document/relational + cache), user profiles/billing (relational), viewing history (Cassandra, write-heavy and partitioned by `userId`), video files (object storage).
4. **Scale:** video served from **CDN edge appliances inside ISPs** (Open Connect style); encode each title into many bitrates/codecs, chunk it, and use ABR (HLS/DASH).
5. **Cache:** metadata in Redis/EVCache; CDN for video chunks and images.
6. **Async:** upload → object storage → queue → transcoding workers (see §9.3); playback events → Kafka → analytics, recommendations.
7. **Reliability:** multi-region active-active, graceful degradation (fall back to a non-personalised list when the recommender fails), chaos testing.
8. **Security:** DRM (Widevine/FairPlay), signed URLs, token-based auth, device limits.
9. **Observability:** distributed tracing, QoE metrics (rebuffer rate, startup time), SLO dashboards.
10. **Cost:** CDN egress dominates, so optimise encoding efficiency, edge caching, and storage tiering.

Then, for every box in your diagram, ask: **"What happens when this fails?"**

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

### E. Study Roadmap (Non-AI System Design)

| Phase | Duration | Focus |
|---|---|---|
| **1. Fundamentals** | ~4 weeks | Scalability, availability, load balancing, caching, database scaling, CAP, consistency, distributed-systems basics (§0, §3-§9, §11-§13) |
| **2. Advanced backend architecture** | ~4 weeks | Kafka, Redis, API gateway, resilience patterns, distributed transactions, event-driven architecture, observability, security (§10.3, §11, §14, §15) |
| **3. Infrastructure** | Ongoing | Docker, Kubernetes, cloud, CI/CD, cost (§16) |
| **4. Practice** | Ongoing | Design real systems end to end (movie site, Netflix, URL shortener, chat, payments), then for every component ask *"what happens when it fails?"* (§17) |

**Tip:** don't just watch videos; draw the design, justify each component with numbers, and walk through failure scenarios.

### F. Problem → Section Index

| Problem / Question | Where answered |
|---|---|
| Junior vs system designer view of a movie site | §0.1, §17 |
| Capacity estimate for 1M users | §0.3 |
| Duplicate payments on retry (idempotency) | §1.4, §10.3 |
| 100,000 users request the same movie | §8.3 |
| Cache stampede / hot key | §8.1, §8.3 |
| Where should the cache live? | §8.3 |
| Why PostgreSQL instead of Cassandra? | §9.4 |
| Last ticket sold twice (race condition) | §9.4, §11.6 |
| Too many DB connections | §9.4 |
| Replica lag / read-your-writes | §9.4 |
| What if PostgreSQL goes down? | §9.4, §17.3 |
| What if Kafka is unavailable? | §10.3, §17.3 |
| Duplicate message processing | §10.3 |
| Dual-write problem (DB + Kafka) | §10.3 (Outbox) |
| Distributed transaction across services | §11.3 |
| Service A → B → C with C taking 30 s | §11.5 |
| Scaling the API | §12, §17.2 |
| RTO / RPO / SLA / SLO | §13 |
| Slow page debugging | §14.4 |
| AuthN vs AuthZ, OAuth2/OIDC/JWT, RBAC/ABAC | §15 |
| Deploy without downtime | §16.4 |
| Cost estimation | §16.5 |
| Design Netflix | §17.4 |

### G. Extended Layer Cheat Sheet

| Area | Key Concepts | Key Metrics |
|---|---|---|
| Requirements | Functional vs non-functional, estimation | n/a |
| API design | REST, idempotency, pagination, versioning | Error rate, p95 latency |
| Databases | Indexes, isolation levels, pooling, replicas, sharding | Slow queries, replication lag, connection usage |
| Messaging | Partitions, offsets, consumer groups, DLQ, outbox | Consumer lag, DLQ size |
| Distributed systems | CAP, sagas, consensus, circuit breaker, bulkhead | Retry rate, breaker state |
| Scalability | Stateless, autoscaling, horizontal scaling | RPS per instance, saturation |
| Reliability | HA, DR, RTO/RPO, SLO | Availability %, error budget |
| Observability | Logs, metrics, traces, alerts | MTTD, MTTR |
| Security | OAuth2/OIDC, JWT, RBAC/ABAC, TLS, zero trust | Auth failures, vulnerabilities open |
| Infra & cost | Docker, K8s, CI/CD, canary, FinOps | Deployment frequency, cost per 1K requests |
