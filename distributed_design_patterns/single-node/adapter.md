---
sort: 3
---

# Adapter Pattern

**Two containers:**
- application container
- adapter container

![adapter](./images/adapter.jpg)

## The Role of the Adapter:

- **adapts the interface of the application container.**

In other words:
- the adapter container modifies the way the application container interacts with other applications, mostly because other applications (often preexisting applications) need to interact in another way (another protocol for example).

**Containers...**

This is a specialization pattern of the sidecar pattern.
The containers:
- live in the same machine via an atomic container group (example: the pod in Kubernetes).
- share resources (example: parts of the filesystem, hostname and network)

## Advantages:

From the sidecar pattern:
- Modularity
- Reuse of components
  - Reduces code duplication in a microservice architecture (as you can reuse components)
- Reduces the complexity
- Containers can evolve independently
  - they can be independently updated
  - they can be implemented in different languages

Besides:
- easily extends application container to support other interfaces

## Examples:

- monitoring:
  - each application can just implement the adapter to be compliant with a single monitoring system
  - each application can reuse an adapter to be compliant with a single monitoring system
- logging:
  - each application can just implement the adapter to be compliant with a single logging system
  - each application can reuse an adapter to be compliant with a single logging system