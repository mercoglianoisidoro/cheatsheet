---
sort: 5
---

# Kubernetes Theory - Scaling

## Horizontal Pod Autoscaling (HPA)
To manage horizontal scaling.

It requires the heapster Pod on your cluster that keeps track of metrics and provides an API for consuming metrics used by HPA.
To check:
```bash
$ kubectl get pods --namespace=kube-system
```

### Set autoscaling

To scale a ReplicaSet:

```bash
$ kubectl autoscale rs NAME --min=2 --max=5 --cpu-percent=80

```



### Note
Vertical scaling is not currently implemented in Kubernetes, but planned.
Cluster autoscaling scale the numbers of host, often offered by a cloud provider.


