---
sort: 3
---
# fargate notes

Here, just some notes.



## To use fargate, you need to use a different profile:

*Schedule:* Fargate Profile   --> *Data Plane* = Fargate VM

*Schedule:* Normal Profile --> *Data Plane* = EC2

The scheduler can use both and chose the right profile using affinity or taints.



## Limitations:

- Fargate add some resources for every task (DNS, etcd, ...)
- NO NLB or CLB
- It can't run privileged containers
- slower startup
- no daemonset or statefulset




