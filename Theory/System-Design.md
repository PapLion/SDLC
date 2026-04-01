# Roadmap.sh

## System Design

### Introduction
- What is System Design?
- How to approach System Design?

### Core Concepts
- Performance vs Scalability
- Latency vs Throughput
- Availability vs Consistency
  - **CAP Theorem:** AP - Availability + Partition Tolerance, CP - Consistency + Partition Tolerance

### Consistency Patterns
- Weak Consistency
- Eventual Consistency
- Strong Consistency

### Availability Patterns
- **Fail-Over:** Active - Active, Active - Passive
- **Replication:** Master - Slave, Master - Master
- **Availability in Numbers:** 99.9% Availability - three 9s, 99.99% Availability - four 9s, Availability in Parallel vs Sequence

### Architecture Components
- **Background Jobs:** Event-Driven, Schedule Driven, Returning Results
- **Domain Name System**
- **Content Delivery Networks:** Pull CDNs, Push CDNs
- **Application Layer:** Microservices, Service Discovery
- **Load Balancers:** LB vs Reverse Proxy, Load Balancing Algorithms, Layer 7 Load Balancing, Layer 4 Load Balancing, Horizontal Scaling

### Databases
- SQL vs NoSQL
- **NoSQL:** Key-Value Store, Document Store, Wide Column Store, Graph Databases
- **Concepts & RDBMS:** Replication, Sharding, Federation, Denormalization, SQL Tuning, RDBMS

### Communication & Operations
- **Communication:** HTTP, TCP, UDP, RPC, gRPC, REST, GraphQL
- **Idempotent Operations**
- **Asynchronism:** Back Pressure, Task Queues, Message Queues

### Caching
- **Strategies:** Refresh Ahead, Write-behind, Write-through, Cache Aside
- **Types of Caching:** Client Caching, CDN Caching, Web Server Caching, Database Caching, Application Caching

### Performance Antipatterns
- Improper Instantiation
- Monolithic Persistence
- Noisy Neighbor
- Synchronous I/O
- Extraneous Fetching
- Busy Database
- Busy Frontend
- Chatty I/O
- Retry Storm
- No Caching

### Monitoring
- Health Monitoring
- Availability Monitoring
- Performance Monitoring
- Security Monitoring
- Usage Monitoring
- Instrumentation
- Visualization & Alerts

> *The design patterns given in this section are of varying importance. You don't need to master all of them. Simply get an overview of each and this will give you some insight into designing scalable systems.*

### Design & Implementation Patterns
- Strangler Fig
- Sidecar
- Static Content Hosting
- Leader Election
- CQRS
- Pipes & Filters
- Ambassador
- Gateway Routing
- Gateway Offloading
- Gateway Aggregation
- External Config Store
- Compute Resource Consolidation
- Backends for Frontend
- Anti-Corruption Layer

### Cloud Design Patterns
- **Data Management:** Valet Key, Static Content Hosting, Sharding, Materialized View, Index Table, Event Sourcing, CQRS, Cache-Aside
- **Messaging:** Sequential Convoy, Scheduling Agent Supervisor, Queue-Based Load Leveling, Publisher/Subscriber, Priority Queue, Pipes and Filters, Competing Consumers, Choreography, Claim Check, Async Request Reply

### Reliability Patterns
- **Availability:** Deployment Stamps, Geodes, Throttling, Health Endpoint Monitoring, Queue-Based Load Leveling
- **High Availability:** Deployment Stamps, Geodes, Bulkhead, Health Endpoint Monitoring, Circuit Breaker
- **Security:** Federated Identity, Gatekeeper, Valet Key
- **Resiliency:** Bulkhead, Circuit Breaker, Compensating Transaction, Health Endpoint Monitoring, Leader Election, Queue-Based Load Leveling, Retry, Scheduler Agent Supervisor