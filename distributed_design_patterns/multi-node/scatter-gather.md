---
sort: 3
---

#  Scatter/Gather

The services work in parallel to respond to the same request.

Each request is split in multiple requests to be served in parallel: complex request can be served by multiple services working in parallel.

A load balancing node, also called *Scatter/Gather*, must be able to split the request and aggregate results.




![scatter-gather.jpg](./images/scatter-gather.jpg)

