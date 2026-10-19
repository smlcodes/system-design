
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
- **Scalability and Reliability:** Handle growing traffic and prevent failures.
- **Performance and Efficiency:** Optimize resources and response times.
- **Maintainability:** Adapt to changing business requirements.
- **Architecture:** Understand monolithic and microservices architectures.


System Design Fundamentals
- **High-Level Design (HLD):** Overall architecture, major components, and communication between them.
- **Low-Level Design (LLD):** Classes, interfaces, methods, and detailed implementation.
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

## 1. Load Balancer 
---------------------
Load balancing is the process of distributing incoming network or application traffic across multiple backend servers or resources. Its main purpose is to prevent a single server from becoming overloaded while improving performance, scalability, and availability. 
**Example:** A company may use NGINX, HAProxy, or AWS Elastic Load Balancing to distribute incoming requests across multiple backend servers.

### Types of Load Balancers

#### 1. Based on Deployment

- Hardware Load Balancer: A dedicated physical device for distributing traffic. Example: F5.
- Software Load Balancer: Software that distributes traffic across servers. Examples: NGINX, HAProxy.
- Cloud Load Balancer: A managed service provided by cloud platforms. Example: AWS Elastic Load Balancing.

#### 2. Based on OSI Model

- Layer 4 Load Balancer: Routes traffic using IP addresses, TCP/UDP protocols, and port numbers.
- Layer 7 Load Balancer: Routes HTTP/HTTPS traffic using URLs, headers, hostnames, cookies, and request methods.


<br>

  

## 2.Caching
-----------

A technique for temporarily storing frequently requested data that speeds up its retrieval when needed again called [caching](https://www.geeksforgeeks.org/system-design/caching-system-design-concept-for-beginners/). The main database or data source is less burdened when caching is included in system architecture, which enhances performance and efficiency.

Here's how caching works and why it's beneficial:

-   **Quick Access**: When data is cached, it's stored in a faster, temporary storage (like memory) instead of being fetched repeatedly from a slower storage source. This makes retrieving information faster.
-   **Reduces Database Load**: The system can process more requests without experiencing any lag by keeping frequently used or popular data in the cache instead of constantly querying the main database.
-   **Improves User Experience**: Faster data access means faster response times for users, which improves the overall experience on websites or apps.


### Types of Cache

#### 1. Application Server Cache

Stores frequently accessed data within an application server to reduce database queries and improve response times.

Drawback: Each server maintains its own cache, causing cache misses and inconsistent data across multiple servers.

#### 2. Distributed Cache

Distributes cached data across multiple nodes. Consistent hashing helps locate the node containing the required data.

Example: Redis Cluster.

#### 3. Global Cache

Provides a shared cache accessible to all application servers. If data is unavailable, it can be fetched from the database or underlying data source.

Example: A shared Redis instance.

#### 4. CDN / Edge Cache

Stores static content on geographically distributed servers to deliver it faster to users.

Examples: Images, CSS, JavaScript, and videos.

### Applications of Caching

- Web Page Caching: Stores web resources to speed up page loading.
- Database Caching: Reduces repeated database queries.
- CDN Caching: Delivers content from nearby servers.
- Session Caching: Stores user session information.
- API Response Caching: Reuses responses to reduce processing time.

### Cache Invalidation Strategies

Cache invalidation ensures cached data remains up to date when the original data changes.

- Time-Based (TTL): Expires data after a specified duration.
- Event-Based: Updates or removes cached data when the source changes.

### Cache Eviction Policies

Eviction policies determine which items to remove when the cache is full.

- LRU (Least Recently Used): Removes the item not accessed for the longest time.
- LFU (Least Frequently Used): Removes the least frequently accessed item.
- FIFO (First In, First Out): Removes the oldest cached item.

### Pros

- Improved Performance: Reduces data access time and response latency.
- Reduced Server Load: Minimizes repeated database queries.
- Cost Savings: Improves resource utilization and reduces infrastructure costs.
- Better Scalability: Helps handle more requests efficiently.

### Cons

- Data Inconsistency: Cached data may become outdated.
- Eviction Overhead: Poor policies can reduce cache hit rates.
- Additional Complexity: Requires managing expiration, invalidation, and consistency.



## 3. Content Delivery Network (CDN)
----------------------------------
### CDN (Content Delivery Network)

A CDN is a geographically distributed network of servers that caches and delivers website content from locations closer to users, reducing latency and improving performance.
when a user requests content (like a video or an image), instead of retrieving it from the main server, the request is handled by a nearby CDN server, which has a cached copy of the content. This reduces the distance the data has to travel, making it load faster for the user.

### Without CDN vs With CDN
**Without CDN:** Requests go directly to the origin server, increasing latency for distant users and potentially overloading the server during traffic spikes.  

<img width="738" height="350" alt="image" src="https://github.com/user-attachments/assets/da7a3080-cdc3-4798-9240-9bac2c2db923" />


**With CDN:** Requests are served from nearby edge servers, reducing latency, improving load times, and lowering the origin server's workload.  
<img width="758" height="370" alt="image" src="https://github.com/user-attachments/assets/57d34409-86b4-4645-bd4b-ba1633a29c55" />


### Real-World Applications

- Streaming: Netflix, YouTube — smoother video playback.
- E-commerce: Amazon — faster product pages and images.
- Software Distribution: Faster downloads of updates and applications.

### Types of CDN

- Public CDN: Shared CDN services available to many customers. Example: Cloudflare.
- Private CDN: Dedicated content delivery infrastructure for an organization.
- Peer-to-Peer (P2P) CDN: Distributes content between users. Example: WebTorrent.
- Hybrid CDN: Combines public and private delivery infrastructure.
- Push CDN: Content is uploaded to CDN servers in advance.
- Pull CDN: Content is fetched from the origin when requested and cached.

### Components of a CDN

- Edge Servers: Cache and deliver content near users.
- Origin Server: Stores the original content.
- Distribution Nodes: Help route traffic and distribute content.
- Control Plane: Manages routing, caching, and traffic distribution.
<img width="828" height="340" alt="image" src="https://github.com/user-attachments/assets/eeaf2863-1f10-4e47-8c6c-a7bc28c63c7e" />




### Types of Content Delivered

- Static Content: Images, CSS, JavaScript, logos, and videos. Easily cached.
- Dynamic Content: Personalized pages, feeds, and live data. Usually generated on demand and cached selectively.

### Importance of CDN

- Faster Delivery: Reduces latency and page load times.
- Scalability: Handles traffic spikes efficiently.
- Reliability: Provides redundancy across multiple servers.
- Cost Savings: Reduces origin bandwidth and server load.
- Security: Can provide DDoS protection, SSL/TLS, and web application firewall (WAF) features.

### Challenges

- Cost: Adds service and bandwidth expenses.
- Complexity: Requires cache and routing configuration.
- Security: Requires proper access controls and cache rules.





## 4. API Gateways
----------------
<img width="726" height="559" alt="image" src="https://github.com/user-attachments/assets/8aaa94b3-7744-4955-b307-64c014f473f9" />

 An API Gateway is a centralized entry point that receives client requests and routes them to the appropriate backend services. It simplifies client communication and handles common tasks such as authentication, rate limiting, and monitoring.

Example: In an e-commerce application, the API Gateway routes product, order, and payment requests to their respective services.
<img width="726" height="280" alt="image" src="https://github.com/user-attachments/assets/15cb61b0-b9ad-4ebc-a5b2-360823b56128" />


### Key Responsibilities

- **Request Routing:** Routes requests based on URLs, HTTP methods, or headers.
- **Authentication & Authorization:** Verifies identity and permissions.
- **Rate Limiting:** Controls request frequency to prevent abuse.
- **Protocol Translation:** Converts between supported protocols, such as HTTP and gRPC.
- **Request Aggregation:** Combines responses from multiple services.
- **Load Balancing:** Distributes traffic across service instances when supported or integrated.
- **Caching:** Stores frequently requested responses.
- **Monitoring & Logging:** Tracks requests, errors, and performance.
 
An API Gateway is optional for a monolith but especially useful in microservices architectures.
 

### Challenges

- Can become a bottleneck or single point of failure if poorly designed.
- Adds latency through routing and security checks.
- Requires careful configuration, security, and scaling.

### Popular API Gateway Solutions

- **Amazon API Gateway** — Managed API service on AWS.
- **Google Apigee** — API management and analytics.
- **Kong** — Extensible API gateway with plugins.
- **Azure API Management** — API publishing, security, and management.
- **Apache APISIX** — High-performance, cloud-native API gateway.




## 5. Key-value stores
------------------------

One kind of NoSQL database that is meant for storing data as a collection of key-value pairs is called a key-value store. Every piece of data is kept in a key-value store under a distinct key, and the data itself serves as the value. Since key-value stores allow for quick access to data by key, they are typically used to store data that is accessed frequently.

-   Key-value stores come in a variety of forms, such as persistent key-value stores, which store data on disk or in a distributed file system for durability, and in-memory key-value stores, which store data in memory for quick access.
-   Key-value stores are generally simpler to use and more scalable than other types of databases, such as RDBMS. However, they are not as well-suited for storing complex structured data that requires advanced querying capabilities.

## 6.Blob storage & Databases 
--------------------------------

Blob storage and database systems are two different types of storage systems that can be used to store and manage data.

Large volumes of unstructured data, including documents, photos, videos, and audio files, can be stored in blob storage, sometimes referred to as object storage. In general, blob storage systems are very scalable and capable of managing several requests at once. They are widely used to store frequently accessible material, such user-generated content or media files.

On the other hand, database systems are made to hold structured data that has been arranged in a particular manner. RDBMSs, NoSQL databases, and in-memory databases are among the several kinds of database systems. Database systems are typically used to store data that needs to be queried and accessed in a structured way, such as customer records or financial transactions.

## 7.Rate limiters
---------------------
Rate Limiting is a technique used to control the number of requests a client can make within a specified time. It prevents system overload, reduces abuse, and ensures fair resource usage.

Example: An API allows 100 requests per minute per user. Additional requests may be rejected with HTTP `429 Too Many Requests`.

### Use Cases

- API Rate Limiting: Controls requests to APIs.
- Web Server: Prevents excessive traffic and overload.
- Database: Limits queries to protect database resources.
- Login: Prevents brute-force password attacks.

### Common Rate Limiting Strategies

- IP-Based: Limits requests from a specific IP address.
- Server-Based: Limits the total requests a server processes.
- Geography-Based: Applies different limits based on the client's estimated location.

### How Rate Limiting Works
 <img width="712" height="255" alt="image" src="https://github.com/user-attachments/assets/849df95c-d746-4d98-ac29-7b27bddbc23b" />


### Rate Limiting Algorithms

- **Token Bucket:** Requests consume tokens added at a fixed rate, allowing bursts. Example: 5 available tokens allow 5 immediate requests.
- **Leaky Bucket:** Processes queued requests at a constant rate. Example: Processes 1 request per second with a queue capacity of 5.
- **Fixed Window Counter:** Limits requests within a fixed time interval and resets the counter afterward. Example: 5 requests per minute.
- **Sliding Window Log:** Tracks request timestamps over a rolling time window. Example: Allows 5 requests in any 60-second period.
Example: Allows 5 requests in any 60-second period.


### Challenges

- Latency: Rate-limit checks can add processing overhead.
- False Positives: Strict limits may block legitimate users.
- Configuration: Different APIs need appropriate limits.
- Scalability: Distributed systems need efficient, coordinated rate limiting.

  

## 9. Monitoring System
------------------------

Monitoring continuously collects and analyzes system data to ensure application health, performance, security, and reliability. It helps detect issues, troubleshoot failures, and prevent downtime.

### Three Pillars of Monitoring

- **Metrics:** Numerical measurements that show system health and performance. Example: CPU usage, request latency, error rate. Tools: Prometheus, Grafana.
- **Logs:** Timestamped records of events used to investigate errors and troubleshoot issues. Example: Payment failed due to a database timeout. Tools: ELK Stack, Splunk.
- **Traces:** Track a request across multiple services to identify bottlenecks. Example: An order request takes 500 ms, with 300 ms spent in the Order Service. Tools: Jaeger, Zipkin, OpenTelemetry.

### Monitoring Philosophies

- **Reactive Monitoring:** Detects and alerts teams after an issue occurs. Example: CPU reaches 100%, triggering an alert.
- **Proactive Monitoring:** Identifies warning signs before a failure occurs. Example: Predicting disk space exhaustion and alerting the team in advance.


### Key Monitoring Domains

- **Infrastructure Monitoring:** Tracks CPU, memory, disk, and network health. Tools: Prometheus, Nagios.
- **Application Performance Monitoring (APM):** Monitors latency, throughput, errors, and database queries. Tools: New Relic, Dynatrace.
- **Log Monitoring:** Collects and analyzes application and system logs. Tools: ELK Stack, Loki.
- **Security Monitoring:** Detects suspicious activities, attacks, and unauthorized access. Tools: Wazuh, SIEM platforms.
- **User Experience Monitoring:** Measures real-user performance (RUM) and simulates user journeys (synthetic monitoring).
- **Business Monitoring:** Tracks business KPIs such as signups, sales, and cart abandonment.

### Effective Monitoring Practices

- SLI (Service Level Indicator): What you measure. **Example**: Login success rate.
- SLO (Service Level Objective): Your target. **Example**: 99.9% login success rate.
- SLA (Service Level Agreement): A formal commitment to customers, potentially including penalties.
- Actionable Alerts: Alert on user-impacting symptoms, such as high API latency.
- Avoid Alert Fatigue: Configure meaningful alerts that require action.
- Role-Based Dashboards: Provide business KPIs for executives, service metrics for engineers, and detailed diagnostics for debugging.






 ## 9. Distributes system messaging queue 
------------------------------------------

A distributed system [messaging queue](https://www.geeksforgeeks.org/system-design/message-queues-system-design/) is a system that enables the exchange of messages between different nodes in a distributed system. Messaging queues allow nodes to communicate asynchronously, decoupling the sender and receiver of a message and enabling each node to operate independently.

There are several different types of messaging queues, including:

-   **Point-to-point queues:** In this type of queue, messages are delivered to a specific recipient.
-   **Publish-subscribe queues:** In this type of queue, messages are published to a topic and are delivered to all subscribers to that topic.
-   **Hybrid queues:** Hybrid queues combine elements of both point-to-point and publish-subscribe queues, allowing messages to be delivered to specific recipients or to all subscribers to a topic.

Distributed system messaging queues can be used to enable communication between different components of a distributed system, such as microservices or distributed applications. They can also be used to decouple different parts of the system, allowing each component to operate independently and improving the system's resilience and scalability.

 ## 10. Distributed unique id generator 
----------------------------------------

A distributed unique ID generator is a system that generates unique identifiers (IDs) that can be used to identify objects or entities in a distributed system. These IDs are typically used to uniquely identify items in a database or to provide a stable identifier for a resource that is accessed over the network.

There are several approaches to generating distributed unique IDs:

-   Using a centralized service
-   Using a [distributed consensus algorithm](https://www.geeksforgeeks.org/computer-organization-architecture/distributed-consensus-in-distributed-systems/)
-   Using timestamps

 ## 11. Distributes search 
---------------------------

Distributed search refers to the practice of using multiple nodes or servers to index and search large datasets in a distributed system. Distributed search can be used to improve the performance and [scalability](https://www.geeksforgeeks.org/system-design/what-is-scalability/) of search operations, as it allows for parallel processing of search queries and the distribution of data across multiple nodes.

There are several approaches to implementing distributed search, including:

-   **Using a distributed search engine:** A distributed search engine is a search platform that is designed to scale horizontally across multiple nodes. These systems typically use a distributed index to store the data being searched, allowing for parallel processing of search queries.
-   **Using a database with search capabilities:** Some databases, such as MongoDB and Cassandra, have built-in search capabilities that allow for the indexing and searching of data stored in the database.
-   **Using a cloud-based search service:** Cloud-based search services, such as Amazon Elastic search Service and Google Cloud Search, can be used to implement distributed search in a distributed system. These services are typically highly scalable and fault-tolerant.

 ## 12. Distributed logging services
-------------------------------------

[Distributed logging](https://www.geeksforgeeks.org/system-design/distributed-logging-for-microservices/) refers to the practice of collecting, storing, and analyzing log data from multiple sources in a [distributed system](https://www.geeksforgeeks.org/computer-networks/what-is-a-distributed-system/). This can be useful for tracking the health and performance of a distributed system, as well as for debugging issues that may arise.

There are several approaches to implementing distributed logging, including:

-   Using a centralized logging service
-   Using a distributed logging system
-   Using a cloud-based logging service

  

 ## 13. Distributes task scheduler
-----------------------------------

A distributed task scheduler is a system that is responsible for scheduling and executing tasks in a distributed system. A task scheduler can be used to automate the execution of tasks at regular intervals, on a specific schedule, or in response to certain events.

There are several approaches to implementing a distributed task scheduler, including:

-   **Using a standalone task scheduler:** A standalone task scheduler is a separate system that is responsible for scheduling and executing tasks in a distributed system. This approach can be simple to implement and allows for flexibility in terms of the types of tasks that can be scheduled.
-   **Using a built-in task scheduler:** Some distributed systems, such as container orchestration platforms or cloud-based serverless platforms, have built-in task schedulers that can be used to schedule tasks within the system.
-   **Using a cloud-based task scheduler:** Cloud-based task schedulers, such as Amazon Simple Notification Service (SNS) or Google Cloud Scheduler, can be used to schedule tasks in a distributed system.
