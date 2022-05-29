---
sort: 6
---

# Storage

Automatic provisioning of a persistent volume is a great feature that makes it significantly easier to build and manage stateful applications in Kubernetes. However, the lifespan of these persistent volumes is dictated by the reclamation policy of the PersistentVolumeClaim and the default is to bind that lifespan to the lifespan of the Pod that creates the volume.
