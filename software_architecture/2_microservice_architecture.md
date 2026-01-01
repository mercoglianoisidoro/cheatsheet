---
sort: 2
---

# Microservice Architecture

## Service

By Chris Richardson in his book "Microservices Patterns":
> A Service is an application that implements narrowly focused functionality, such as order management, customer management, and so on.

## Definition: Microservice Architecture

By Chris Richardson in his book "Microservices Patterns":
> The high-level definition of microservice architecture (microservices) is an architectural style that functionally decomposes an application into a set of services. Note that this definition doesn't say anything about size. Instead, what matters is that each service has a focused, cohesive set of responsibilities.

## Characteristics

- **Modularity**: The microservice architecture uses services as the unit of modularity. This introduces other benefits like the ability to deploy and scale them independently.

- **Loosely coupled and database**: Microservices are loosely coupled and communicate only via APIs (no DB). One way to achieve it is: each service having its own datastore

- **Shared Library**: Be sure to not introduce coupling.

- **Size**: Size is not a useful metric. A better target: developed by a small team with minimal lead time.

## Benefits

- Easy continuous delivery and deployment
- Smaller services: easier to maintain (test, update, ...)
- Services are independently deployable (updatable), scalable, monitored
- Simplicity and autonomy for teams and business
- Easy experimenting and adoption of new technologies
- Better fault isolation

## Drawbacks

- Hard to decouple the macrosystem into microservices
- Distributed systems become more complex: it can be hard to test and deployment
- More complex coordination
- More complex macro debugging

## Delivery Organization

### About Team Size

The communication overhead of a team of size N is O(N²) → if team gets too large, it will become inefficient (communication overhead).

### Conway's Law

*Organizations which design systems ... are constrained to produce designs which are copies of the communication structures of these organizations.*

### Conway's Law in Reverse

It's important, therefore, to apply Conway's law in reverse:

From: https://www.thoughtworks.com/radar/techniques/inverse-conway-maneuver

Conway's Law asserts that organizations are constrained to produce application designs which are copies of their communication structures. This often leads to unintended friction points. The 'Inverse Conway Maneuver' recommends evolving your team and organizational structure to promote your desired architecture. Ideally your technology architecture will display isomorphism with your business architecture.

Besides, by doing so, you ensure that your development teams are as loosely coupled as the services.

## A Three-Step Process for Defining an Application's Architecture

1. Identify system operations
2. Identify services
3. Define service APIs and collaborations

This process is proposed by Chris Richardson in his book "Microservices Patterns".

### 1. Identify System Operations

A **system operation** is an abstraction of a request that the application must handle.

Starting point: application requirements.

2 steps: create a domain model and then describe behaviors. The domain model is derived primarily from the nouns of the user stories, and the system operations are derived mostly from the verbs.

### 2. Identify Services

It's a decomposition problem. Many strategies can be used.

Example strategies:

- Define services corresponding to business capabilities
- Use Domain-Driven Design (DDD)

#### Define Services Corresponding to Business Capabilities

Business capability = is something that a business does in order to generate value.

[https://microservices.io/patterns/decomposition/decompose-by-business-capability.html](https://microservices.io/patterns/decomposition/decompose-by-business-capability.html)

#### Use Domain-Driven Design (DDD)

[https://microservices.io/patterns/decomposition/decompose-by-subdomain.html](https://microservices.io/patterns/decomposition/decompose-by-subdomain.html)

Don't use a single model for the entire enterprise, instead define multiple domain models, separated by a different scope.

Here a strategy: a domain is decomposed in subdomain at which the DDD is applied.

**Domain** = application's problem space, the subject area, is divided in

- **subdomain** 1 ---define--> **Domain Model** 1 --has its own--> **Bounded Context**
- **...**
- subdomain N ---define--> Domain Model N --has its own--> Bounded Context

where the subdomain is part of the domain, they are similar to business capabilities.

##### Domain-Driven Design

**Bounded context**: is the scope of a domain model, it includes implementation artifacts. It corresponds to a microservice or a set of microservices.

**Ubiquitous language**: common language for the project. The domain model is its backbone. It's the common language used by both domain experts and developers.
The more it is used, the more it is effective.

**Model**: tools letting to simplify a body of knowledge; it is a selectively simplified and consciously structured form of knowledge. A good model makes sense of information and focuses on a problem.

> FROM: Domain-Driven Design: Tackling Complexity in the Heart of Software, By Eric Evans
>
> The Utility of a Model in Domain-Driven Design:
>
> - The model and the heart of the design shape each other
> - The model is the backbone of a language used by all team members
> - The model is distilled knowledge
>
> Ingredients of Effective Modeling:
>
> - Binding the model and the implementation
> - Cultivating a language based on the model
> - Developing a knowledge-rich model
> - Distilling the model
> - Brainstorming and experimenting
>
> To tie the implementation slavishly to a model usually requires software development tools and languages that support a modeling paradigm, such as object-oriented programming.

A **domain model**: idea that the diagram is intended to convey. Organized and selected knowledge. Must be understood by both domain experts and developers.

DDD discards the dichotomy of analysis model and design to search out a single model that serves both purposes.

**Diagrams, Model Paradigms and Tool Support:**

Diagrams have to be essential, not pointing at the global object model, to avoid overwhelming the readers.

Different diagrams can focus on different points. Natural language can add details.

To make a correspondence between model and design, you need a modeling paradigm supported by software tools able to create a direct correspondence between concepts and implementation.

Modeling paradigms:

- Object-oriented programming
- Prolog language
- ...

> FROM: Domain-Driven Design: Tackling Complexity in the Heart of Software, By Eric Evans
>
> A MODEL-DRIVEN DESIGN intimately connects the model and the implementation. The UBIQUITOUS LANGUAGE is the channel for all that information to flow between developers, domain experts, and the software.

**Layered architecture and DDD**

Eric Evans proposes to use layered architecture and apply DDD to the business layer.

### 3. Define Service APIs and Collaborations

Assign operations to services.

And then check if services can cooperate with other services.

### Decomposition

#### Guidelines

Here some guidelines from OOP.

##### Single Responsibility Principle

Each microservice has a single responsibility. → reduce the size and increase stability

##### Common Closure Principle

Reminder:

> The classes in a package should be closed together against the same kinds of changes. A change that affects a package affects all the classes in that package.
>
> Robert C. Martin

Components (classes) that change for the same reason, should stay in the same service → requirement changes will affect few microservices

#### Obstacles and Some Strategies

**Network latency** →

- Use batch API
- Combine services
- At infrastructure level: use container groups (ex pods)
- Use another IPC

**Synchronous communication** between services reduces availability (if a service is not available, the other depending on it will be impacted) →

- Use asynchronous messaging

**Data consistency** across services (some data need to be transitionally updated on multiple microservices) →

- Use a two-phase, commit-based, distributed transaction management mechanism
- Use SAGA (Saga distributed transactions pattern): https://microservices.io/patterns/data/saga.html

**God classes**, which are used throughout an application → identify and eliminate them