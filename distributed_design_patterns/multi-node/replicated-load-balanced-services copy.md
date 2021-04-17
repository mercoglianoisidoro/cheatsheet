---
sort: 1
---

#  Replicated Load-Balanced Services

A service is replicated: every service is identical and they are behind a load balancer.



![replicated-load-balanced-services](./images/replicated-load-balanced-services.jpg)


Strategies for the load balancer:
- round-robin
- session stickiness
- implement a probe (Readiness Probes)
- a more complex approach

A caching layer can be considered between the load balancer and the services

## Stateless Services

Each service replica doesn't need a saved state: each request can be rooted to any service replica (this is also a requirement).

Replication of this type of service provide **redundancy, reliability and scalability**.

Redundancy let to:
- provide a service level agreement for your system


Scalability let to:
- increase the number of requests per time being processes by the system
- decrease the resources used by the system when they are not needed


## Stateful Services

Each service replica handles status information, that's why you need sticky session (stateful services don't scale well).

Sticky session: once users establish a connection with a particular service, they have to stick with it until the transaction completes.


