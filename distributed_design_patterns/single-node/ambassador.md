---
sort: 2
---

#  Ambassador Pattern

**Two containers:**
- application container
- ambassador container

![sidecar](./images/ambassador.jpg)


## The role of the Ambassador:

- **broker communication coming from the application container to the rest of the world.**

In other words:
- the ambassador container sends network requests on behalf of the application container


**Containers...**

This is a specialization patter of the sidecar pattern.
The containers:
- live in the same machine via an atomic container group (example: the pod in Kubernetes).
- share resources (example: parts of the filesystem, hostname and network)


## Advantages:

From the sidecar pattern:
- Modularity
- Reuse of components
  - Reduces code duplication in a microservice architecture (as you can reuse component)
- Reduces the complexity
- Container can evolve independently
  - they can be independently updated
  - they can be implemented in different languages

Besides:
- separation of concerns


## Examples:

- a proxy for a sharded service
- beta testing (the ambassador carry the logic to redirect requests prod or beta)
- Service Brokering (service broker do services discovering and act as a proxy)



