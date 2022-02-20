---
sort: 2
---
# AWS Elastic Kubernetes Service

Here, just some notes.



## To use fargate, you need to use a different profile:

*Schedule:* Fargate Profile   --> *Data Plane* = Fargate VM

*Schedule:* Normale Profile --> *Data Plane* = EC2

The scheduler can use both and chose the right profile using affinity or taints.



## Limitations:

- Fargate add some resources for every task (DNS, etcd, ...)

- NO NLB or CLB

- It can't run privileged containers

- slower startup

- no dataset of statefulset

  

 
