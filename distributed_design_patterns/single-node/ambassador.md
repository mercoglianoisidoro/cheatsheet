---
sort: 2
---

# Ambassador Pattern

**Two containers:**
- application container
- ambassador container

![ambassador](./images/ambassador.jpg)

## The Role of the Ambassador:

- **brokers communication coming from the application container to the rest of the world.**

In other words:
- the ambassador container sends network requests on behalf of the application container

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
- separation of concerns

## Examples:

- a proxy for a sharded service
- beta testing (the ambassador carries the logic to redirect requests to prod or beta)
- Service Brokering (service broker does service discovery and acts as a proxy)