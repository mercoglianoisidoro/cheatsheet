---
sort: 4
---

# Transaction Management in a Microservice Environment

## Problem Introduction

In a microservice environment, where each service is coupled with its own database, the system can't rely on ACID transactions as it could be with a legacy system relying on a single relational database.

A multi-database architecture is ACD (lack of Isolation), not ACID.

Two solutions exist:

- Distributed transactions
- SAGA Pattern

## Distributed Transactions

A standard for distributed transaction management is: X/Open Distributed Transaction Processing (DTP), in short X/Open XA

[https://en.wikipedia.org/wiki/X/Open_XA](https://en.wikipedia.org/wiki/X/Open_XA)

### Problems

- It's a form of synchronous IPC, → reduced availability
- Support: even if it's the de-facto standard, many database engines don't support it, like MongoDB and Cassandra, neither some message brokers, like RabbitMQ and Apache Kafka.
- Blocking protocol
- Transaction manager is a single point of failure

### Benefits

- Atomic transaction across multiple heterogeneous technologies

## SAGA Pattern

Also **called Long-running transactions**.

Following this pattern, for each command needing one global transaction: the transaction is divided into a set of distributed transactions with a compensation mechanism that restores previous state in case of rollback.
This lets to have a data consistency mechanism across services, using asynchronous messaging.

They **lack the Isolation** property of ACID transactions.

A rollback mechanism must be implemented (it's not automatic): every step made in each microservice has to have a rollback step. **Coordination is needed**.

[https://microservices.io/patterns/data/saga.html](https://microservices.io/patterns/data/saga.html)

### Coordination

2 ways: Choreography and Orchestration.

#### Some Considerations

- Because coordination is made using messages, **transactional messaging** is needed (database update and message publishing must be atomic)
- Modeling with **state machine** can be useful

#### Choreography

Distributed decision making by exchanging events (they trigger local transactions).

Drawbacks:

- Difficult to understand
- Cyclic dependencies between the services

#### Orchestration

Based on an orchestrator sending commands.

Drawbacks:

- Too much business logic in the orchestrator → orchestrator must be responsible only for sequencing

### Lack of Isolation

The isolation of the transaction is not guaranteed as any local transaction will be visible to other SAGAs.

This carries out to 2 effects:

- Other saga can change data during a global transaction → inconsistent data, rollback problems
- Other saga can read data during a transaction → inconsistent data

#### More In-depth Details

This type of problems are called **anomalies**.

The lack of isolation can cause:

**Dirty reads:** a transaction reads the altered records of another transaction before the other transaction has completed.

**Non-repeatable reads:** 2 different transactions read data and get different results as another transaction is changing that same data

**Lost updates:** a transaction overwrites data without reading changes made by another one

**Phantom records:** a transaction reads temporary data from another transaction, or vice-versa

#### Countermeasures

- **Semantic lock**: application-level lock
- **Commutative updates**: Design update operations to be executable in any order
- **Pessimistic view**: reorder the steps of a saga to minimize business risk
- **Reread value**: Prevent dirty writes by rereading data to verify that it's unchanged before overwriting it
- **Version file**: Record the updates to a record so that they can be reordered
- **By value**: Use each request's business risk to dynamically select the concurrency mechanism.