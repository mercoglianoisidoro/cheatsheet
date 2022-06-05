---
sort: 6
---

# Scaling

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



### Create HorizontalPodAutoscaler

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: NAME
spec:
  maxReplicas: 10
  minReplicas: 1
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: DEPLOYM
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: AverageValue
        averageValue: 700Mi
  behavior:
    scaleDown:
      policies:
      - type: Percent
        value: 10
        periodSeconds: 10
    scaleUp:
      stabilizationWindowSeconds: 10
```



### Note
Vertical scaling is not currently implemented in Kubernetes, but planned.
Cluster autoscaling scale the numbers of host, often offered by a cloud provider.

