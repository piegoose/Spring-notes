# Microservices

## Definition of a Microservice

A microservice is a small, autonomous service focused on a single business capability that works together with other services to form a complete application.

### Characteristics

- independently deployable
- independently scalable
- loosely coupled
- highly cohesive
- owns its own data
- communicates through APIs or messaging

---

# Monolith vs Microservices

## Monolith

Single application containing all business modules.

```text
Application
├── Users
├── Orders
├── Products
└── Payments
```

### Advantages

- simple architecture
- easy debugging
- easy deployment
- simple transactions

### Disadvantages

- difficult scaling
- slower development in large teams
- one deployment unit
- one failure can affect the entire system

---

## Microservices

Application divided into independent services.

```text
User Service

Order Service

Product Service

Payment Service
```

### Advantages

- independent deployment
- independent scaling
- fault isolation
- technology flexibility
- team autonomy

### Disadvantages

- distributed system complexity
- network communication overhead
- data consistency challenges
- harder monitoring and debugging

---

# Core Principles

## Single Responsibility

Each service should focus on one business capability.

```text
User Service
    ↓
User Management
```

---

## Bounded Context

Concept from Domain Driven Design.

Each service should represent one business domain.

```text
User Context

Order Context

Payment Context
```

---

## Database per Service

Each service owns its database.

```text
User Service
    ↓
users_db

Order Service
    ↓
orders_db
```

### Benefits

- loose coupling
- independent schema evolution
- easier scaling

### Problem

Cross-service joins are not possible.

---

# Communication

## Synchronous Communication

### Technology

- REST API
- OpenFeign
- WebClient

### Flow

```text
Order Service
      ↓
REST Call
      ↓
User Service
```

### Advantages

- simple
- immediate response

### Problems

- service dependency
- latency
- cascading failures

---

## Asynchronous Communication

### Technology

- RabbitMQ
- Kafka

### Flow

```text
Order Service
      ↓
OrderCreated Event
      ↓
Message Broker
      ↓
Notification Service
```

### Advantages

- loose coupling
- resilience
- scalability

### Problems

- eventual consistency
- harder debugging

---

# Problems Introduced by Microservices

## Distributed System Complexity

In monolith:

```text
Method Call
```

In microservices:

```text
Network Call
```

Additional concerns:

- latency
- timeouts
- network failures
- serialization
- availability

---

## Service Discovery Problem

How does one service find another?

```text
Order Service
needs
User Service
```

### Technology

- Eureka
- Consul
- Kubernetes Service Discovery

---

## Data Consistency Problem

Monolith:

```text
Single Database
Single Transaction
```

Microservices:

```text
Multiple Databases
Multiple Services
```

Consistency becomes difficult.

---

## Distributed Transactions Problem

Example:

```text
Create Order
↓
Reserve Product
↓
Process Payment
```

What happens if payment fails?

Need distributed transaction patterns.

---

# Architectural Patterns

## Saga Pattern

Manages distributed transactions.

### Flow

```text
Create Order
↓
Reserve Product
↓
Process Payment
↓
Send Confirmation
```

If a step fails:

```text
Cancel Order
Release Product
Refund Payment
```

### Solves

- distributed transaction problem

---

## Strangler Fig Pattern

Migration from monolith to microservices.

### Flow

```text
Monolith
↓
Extract User Service
↓
Extract Product Service
↓
Extract Order Service
```

### Solves

- risky big-bang migration

---

## API Gateway Pattern

Single entry point for clients.

```text
Client
↓
Gateway
↓
Services
```

### Responsibilities

- routing
- authentication
- authorization
- rate limiting

### Technology

- Spring Cloud Gateway
- Kong
- NGINX

---

## Circuit Breaker Pattern

Prevents cascading failures.

```text
Service A
↓
Service B Down
↓
Fallback
```

### States

```text
Closed
↓
Open
↓
Half-Open
↓
Closed
```

### Technology

- Resilience4j

---

## CQRS

Command Query Responsibility Segregation.

```text
Command
↓
Write

Query
↓
Read
```

### Benefits

- better scalability
- optimized read models

---

# Event Driven Architecture (EDA)

Architecture based on events.

```text
Order Service
↓
OrderCreated Event
↓
Kafka
↓
Payment Service
↓
Notification Service
```

### Benefits

- loose coupling
- scalability
- resilience

### Technology

- Kafka
- RabbitMQ

---

# Eventual Consistency

Data is not immediately consistent across services.

Example:

```text
Order Created
↓
Payment Processing
↓
Payment Confirmed
```

Temporary inconsistency is normal.

---

# Observability

Understanding what happens in distributed systems.

## Logs

Record events.

```text
Order Created
```

### Technology

- ELK Stack
- Loki

---

## Metrics

Measure system health.

Examples:

```text
Request Count

Error Rate

Response Time
```

### Technology

- Prometheus
- Grafana

---

## Distributed Tracing

Track requests across services.

```text
Gateway
↓
Order Service
↓
Payment Service
↓
Notification Service
```

### Technology

- OpenTelemetry
- Zipkin
- Jaeger

---

# Containerization

## Docker

Packages applications into containers.

### Usage

- deployment
- local development
- environment consistency

---

## Docker Compose

Runs multiple services locally.

Example:

```text
User Service
PostgreSQL
RabbitMQ
```

---

# Container Orchestration

## Kubernetes

Manages containers at scale.

### Responsibilities

- deployment
- scaling
- self-healing
- rolling updates
- service discovery

---

# Typical Spring Microservices Stack

## Communication

- REST API
- OpenFeign
- WebClient

## Messaging

- RabbitMQ
- Kafka

## Gateway

- Spring Cloud Gateway

## Service Discovery

- Eureka

## Resilience

- Resilience4j

## Configuration

- Spring Cloud Config

## Monitoring

- Prometheus
- Grafana

## Tracing

- OpenTelemetry
- Zipkin

## Containers

- Docker

## Orchestration

- Kubernetes

---

# Interview Questions

## Monolith vs Microservices

Single application vs independent services.

---

## Why Database per Service?

Loose coupling and independent evolution.

---

## RabbitMQ vs Kafka

RabbitMQ:
- task queues
- background jobs

Kafka:
- event streaming
- analytics
- high throughput

---

## What is Saga Pattern?

Pattern for managing distributed transactions.

---

## What is API Gateway?

Single entry point for all services.

---

## What is Circuit Breaker?

Protection against cascading failures.

---

## What is Eventual Consistency?

Data becomes consistent over time.

---

# Must Know Topics

- Monolith vs Microservices
- Bounded Context
- Database per Service
- REST Communication
- Messaging
- RabbitMQ
- Kafka
- Saga Pattern
- Strangler Fig Pattern
- API Gateway
- Circuit Breaker
- CQRS
- Event Driven Architecture
- Eventual Consistency
- Observability
- Docker
- Kubernetes