---
sort: 2

---

# Container Runtime

## Definition

The container runtime is a piece of software that creates and manages containers. In Linux, the container runtime uses a set of kernel primitives such as control groups (cgroups) and namespaces to spawn a process from a container image.



## The Open Container Initiative

The Open Container Initiative (OCI) is an open source project established in 2015 to collaborate on specifications around containers. 

[https://github.com/opencontainers](https://github.com/opencontainers) => [https://github.com/opencontainers/runtime-spec/blob/main/spec.md](https://github.com/opencontainers/runtime-spec/blob/main/spec.md)

3 specifications:

- OCI runtime specification
- OCI image specification
- OCI distribution specification



## OCI runtime specification

**It determines how to instantiate and run containers in an OCI-compatible fashion.**

It describes: 

- the container’s configuration (includes container’s root filesystem, command to run, environment variables, the user and group to use, resource limits, ...)
-  operations that a container runtime must support (create, start, kill, delete, and state)
- life cycle of a container and how it progresses through different stages ( 1. creating, 2. created, 3. running, 4. stopped)



The OCI project also hosts **runc**, a low-level container runtime that implements the OCI runtime specification. 



## OCI Image Specification

**It focuses on the container image.**

It defines:

- a manifest (describes the image, it list all the others)
- an optional image index
- a set of filesystem layers
- a configuration (image’s entry point, command, working directory, environment variables, and more)
- how to create and manage container image layers

Layers = TAR archives that include files and directories. The specification defines different media types for layers, including uncompressed layers, gzipped layers, and nondistributable layers.



## OCI distribution specification

It defines an API protocol to facilitate and standardize the distribution of content.
