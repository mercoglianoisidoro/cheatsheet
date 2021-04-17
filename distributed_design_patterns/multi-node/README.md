---
sort: 2
---

# Multi-node design pattern

Multi-node patters describe solutions using services (container) spread on multiple machines.

Examples of addressed problems: scalability, reliability, redundancy, separation of concerns.

The containers:
- are loosely coupled
- don't share local resources
- can't communicate directly, but on the network


## Goals
The following patterns have different goals. Here a summary

| name of the pattern  | main/direct goal                                                | benefits                               |
| -------------------- | --------------------------------------------------------------- | -------------------------------------- |
| Replicated services  | Increase the number of request per second the system can manage | reliability, redundancy, and scaling   |
| Sharded Services     | Increase the amount of data the system can manage               | scale in response to the data size     |
| Scatter/Gather       | Reduce the response time of the system                          | reduce response time of the system     |

