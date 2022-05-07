---
sort: 2
---

# Microservice architecture

## Service:
by Chris Richardson in his book "Microservices Patterns":
> A Service is a application that implements narrowly focused functionality, such as order management, customer management, and so on.




## Definition: Microservice architecture
by Chris Richardson in his book "Microservices Patterns":
> The high-level definition of microservice architecture (microservices) is an architectural style that functionally decomposes an application into a set of services. Note that this definition doesn’t say anything about size. Instead, what matters is that each service has a focused, cohesive set of responsibilities.

## Characteristics

- **Modularity**: The microservice architecture uses services as the unit of modularity. This introduces other benefits like the ability to deploy and scale them independently.

- **Loosely coupled and database**: microservice are loosely coupled and communicate only via APIs (no DB). One way to achieve it is: each service having its own datastore

- **Shared Library**: Be sure to not introduce coupling.

- **Size**: Size in not a useful metrics. A better target: developed by a small team with minimal lead time.


## Benefits

- easy continuous delivery and deployment
- smaller services: easier to maintain (test, update, ...)
- services are independently deployable (updatable), scalable, monitored
- simplicity and autonomy for teams and business
- easy experimenting and adoption of new technologies
- better fault isolation

## Drawbacks

- hard to decouple the macrosystem into microservices
- distributed systems become more complex: it can be hard to  test and deployment
- more complex coordination
- more complex macro debugging

## Delivery organization

### About team size
The communication overhead of a team of size N is O(N2) => if team gets too large, it will become inefficient (communication overhead).

### Conway’s law
*Organizations which design systems ... are constrained to produce designs which are copies of the communication structures of these organizations.*

### Apply Conway’s law
It’s important, therefore, to apply Conway’s law in reverse:

(From: https://www.thoughtworks.com/radar/techniques/inverse-conway-maneuver)
*Conway's Law asserts that organizations are constrained to produce application designs which are copies of their communication structures. This often leads to unintended friction points. The 'Inverse Conway Maneuver' recommends evolving your team and organizational structure to promote your desired architecture. Ideally your technology architecture will display isomorphism with your business architecture.*


Besides, by doing so, you ensure that your development teams are as loosely coupled as the services.



## A three-step process for defining an application’s architecture

1. identify system operations
2. identify services
3. define service APIs and collaborations

This process is proposed by Chris Richardson in his book "Microservices Patterns".

### 1. Identify system operations

A **system operation** is an abstraction of a request that the application must handle.

Starting point: application requirements.

2 speps: create a domain model and then describe behaviors. The domain model is derived primarily from the nouns of the user stories, and the system operations are derived mostly from the verbs.

### 2. Identify services

It's a decomposition problem. Many strategies can be used.

Example strategies:

- define services corresponding to business capabilities
- use Domain-Driver Design  (DDD)



#### Define services corresponding to business capabilities

Business capability = is something that a business does in order to generate value.

[https://microservices.io/patterns/decomposition/decompose-by-business-capability.html](https://microservices.io/patterns/decomposition/decompose-by-business-capability.html)



#### Use Domain-Driver Design  (DDD)

[https://microservices.io/patterns/decomposition/decompose-by-subdomain.html](https://microservices.io/patterns/decomposition/decompose-by-subdomain.html)

Don't use a single model for the entire enterprise, instead define multiple domain models, separated by a different scope.

Here a strategy: a domain is decomposed in subdomain at which the DDD is applied.



**Domain** = application’s problem space, the subject area, is divided in

- **subdomain** 1 ---define--> **Domain Model** 1 --has its own--> **Bounded Context**
- **...**
- subdomain N ---define--> Domain Model N --has its own--> Bounded Context

where the subdomain is part of the domain, they are similar to business capabilities.



##### Domain-Driver Design

**Bounded context**: is the scope of a domain model, it include implementation artifacts. It corresponds to a microservice or a set of microservices.

**Ubiquitous language**:  common language for the project. The domain model it its backbone. It's the commons language used by both domain experts and developers.
The most is used, the most if effective.

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

Diagrams have to be essential, not pointing at the global object model, to avoid to overwhelm the readers.

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

Eriv Evans propose to use layered architecture and apply DDD to the business layer.



### 3. Define service APIs and collaborations

Assign operations to services.

And then check if services can cooperate with other services.



### Decomposition

#### Guidelines

Here some guidelines from OOP.

##### Single Responsibility Principle

each microservice has a single responsibilit. => reduce the size and increase stability

##### Common Closure Principle:

Reminder:

> The classes in a package should be closed together against the same kinds of changes. A change that affects a package affects all the classes in that package.
>
> Robert C. Martin



Components (classes) that change for the same reason, should stay in the same service => requirement changes will affect few microservices



#### Obstacles and some strategies

**Network latency** =>

- use batch API
- combine services
- ad infrastructure level: use container groups (ex pods)
- use a another IPC



**Synchronous communication** between services reduces availability (is a service is not available, the other depending on it will be impacted) =>

- use asynchronous messaging



**Data consistency** across services (some data need to be transitionally updated on multiple microservices) =>

- use a two-phase, commit-based, distributed transaction management mechanism
- use SAGA (Saga distributed transactions pattern): https://microservices.io/patterns/data/saga.html



**God classes**, which are used throughout an application => identify and eliminate them



