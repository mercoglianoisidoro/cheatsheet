---
sort: 4
---

# Transaction management



## Problem Introduction

In an microservice environment, where each service is coupled with its own database, the system can't rely on ACID transactions as it could be with a legacy system relying on a single relational database.

A multi-database architecture is ACD (lack of Isolation), not ACID.

Two solution exist:

- distributed transactions
- SAGA Pattern



## Distributed transactions

A standard for distributed transaction management is: X/Open Distributed Transaction Processing (DTP), in short X/Open XA

[https://en.wikipedia.org/wiki/X/Open_XA](https://en.wikipedia.org/wiki/X/Open_XA)



### Problems

- it's a form of synchronous IPC, => reduced availability
- Support: even if it's the de-facto standard, many database engine don't support it, like MongoDB and Cassandra, neither some message broker, like RabbitMQ and Apache Kafka.
- blocking protocol
- transaction manager is a single point of failure

### Benefits

- atomic transaction across multiple heterogeneous technologies





## Saga Pattern

Also **called Long-running transactions**.

Following this patter, for each command needing of one global transaction: the transaction is divided into a set of distributed transaction with a compensation mechanism that restore previous state in case of rollback.
This let to have a data consistency mechanism across services, using asynchronous messaging.

They **lack the Isolation** property of ACID transaction.

A rollback mechanism must be implemented (it's not automatic): every step made in each microservice have to have a rollback step. **Coordination is need**.

[https://microservices.io/patterns/data/saga.html](https://microservices.io/patterns/data/saga.html)



### Coordination

2 ways: Choreography and Orchestration.

#### Some consideration

- because of coordination is made using messages, **transactional messaging** is needed (database update and message publishing must be atomic)

- modeling with **state machine** can be useful



#### Choreography

Distributed decision making by exchanging events (they trigger local transaction).

Drawbacks:

- difficult to understand
- cyclic dependencies between the services



#### Orchestration

Based  on an orchestrator sending command.

Drawbacks:

-  too much business logic in the orchestrator => orchestrator must be responsible only of sequencing



### Lack of isolation

The isolation of the transaction is not guaranteed as any local transaction will be visible to other SAGAs.

This carries out to 2 effects:

- other saga can change data during a global transaction => inconstant data, rollback problems
- other saga can read data during a transaction => inconstant data



#### More in-depth details

This type of problems are called **anomalies**.

The lack of isolation can cause:

**Dirty reads:** a transaction reads the altered records of another transaction before the other transaction has completed.

**Non-repeatable reads:** 2 different transactions reads data and get different result as another transaction is changing that same data

**Lost updates:** a transaction overwrite a data without reading change made by another one

**Phantom records:** a transaction read temporary data from another transaction, or vice-versa



#### Countermeasures

- **Semantic lock**: application-level lock
- **Commutative updates**: Design update operations to be executable in any order
- **Pessimistic view**: reorder the steps of a saga to minimize business risk
- **Reread value**: Prevent dirty writes by rereading data to verify that it’s unchanged before overwriting it
- **Version file**: Record the updates to a record so that they can be reordered
- **By value**: Use each request’s business risk to dynamically select the concurrency mechanism.



