---
sort: 1
---


# Container and Microservices

## Container

**Container:**

[from docker.com](https://www.docker.com/resources/what-container) a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.

Containers can be used as building blocks for cloud architectural patterns.

### Techonologies:

#### Docker

- **Docker**: [from kubernetes.io](https://www.kubernetes.io) a software technology providing operating-system-level virtualization also known as containers.
- **Docker container image:** [from docker.com](https://www.docker.com) a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
- **Docker Engine:** [from docker.com](https://www.docker.com) an open source containerization technology for building and containerizing your applications.

**Link:** [docker.com](https://docs.docker.com/engine/)
{: style="text-align: right" }
#### Containerd

- **Containerd**: [from kubernetes.io](https://www.kubernetes.io) container runtime with an emphasis on simplicity, robustness and portability.

**Link:** [containerd.io](https://containerd.io/docs/)
{: style="text-align: right" }
#### CRI-O

- **CRI-O**: [from kubernetes.io](https://www.kubernetes.io) a lightweight container runtime specifically for Kubernetes.

**Link:** [cri-o.io](https://cri-o.io/##what-is-cri-o)
{: style="text-align: right" }
#### Dyno

- **Dynos**: [form heroku.com](https://www.heroku.com/dynos) isolated, virtualized Linux containers that are designed to execute code based on a user-specified command.

**Link:** [heroku.com](https://www.heroku.com)
{: style="text-align: right" }

### Containerization

- Goals:
  - establish boundaries around specific resources
  - separation of concerns

### Best practices for building containers

[from cloud.google.com](https://cloud.google.com/solutions/best-practices-for-building-containers):

- Package a single app per container
- Properly handle PID 1, signal handling, and zombie processes (the first process launched in a container gets PID 1. Both Docker and Kubernetes can only send signals to the process that has PID 1 inside a container)
- Remove unnecessary tools
- Build the smallest image possible
- Use vulnerability scanning in Container Registry
- Properly tag your images
- Carefully consider whether to use a public image


#### Specifically for docker containers

- Optimize for the Docker build cache



## Microservices


[from wikipedia ](https://en.wikipedia.org/wiki/Microservices):
A microservice is a self-contained piece of business functionality with clear interfaces.
From a strategy perspective, microservices architecture essentially follows the Unix philosophy of "Do one thing and do it well".

### Some advantages over monolithic system

- reliability
- agility
- simplicity
- better scaling (each service can scale with the approach that suit it the best)
- easy to monitor


The introduction of formal API provide a reliable contract among microservices.

### Some disadvantages

- more complicate
  - debugging
  - communication between services
- design and architecture can be more complex


## Container versus Microservices

In short:
- a **microservice** is a self-contained piece of business: they contain everything as one unit of execution (from operating system to libraries implementing business logic).
- a **container** a standard unit of software that encapsulate discrete components of application logic.

Some additional details:
- a microservice may run in a container, or in a VM
- container engines provide a kind of OS virtualization: a container can implement a microservice
- rule of thumb: container are building block for services and microservices

