---
sort: 6
---

# Scaling

## kubectl scale

Set a new size for a deployment, replica set, replication controller, or stateful set.

example:
```bash
# If the deployment named mysql's current size is 2, scale mysql to 3
  kubectl scale --current-replicas=2 --replicas=3 deployment/mysql
  
```
## Horizontal Pod Autoscaling (HPA)
The HPA manage horizontal autoscaling.
You can use the `autoscale deployment` command to create an HPA.

To scale a ReplicaSet (create an HPA):

```bash
$ kubectl autoscale rs NAME --min=2 --max=5 --cpu-percent=80

```

To scale a deployment (create an HPA):

```bash
kubectl autoscale deployment nginx --cpu-percent=80 --min=3 --max=5
```

the equivalent manifest:
```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx
spec:
  maxReplicas: 5
  minReplicas: 3
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx
  targetCPUUtilizationPercentage: 80


```

The autoscaler need resources set on pods and metric server

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

### Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: ubuntu
  name: ubuntu
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu
  strategy: {}
  template:
    metadata:
      labels:
        app: ubuntu
    spec:
      volumes:
      - name: exec
        configMap:
          name: exec
          items:
          - key: "start.sh"
            path: "start.sh"
      containers:
      - image: ubuntu
        name: ubuntu
        command:
        - bash
        - -c
        - bash /mounts/start.sh
        resources:
          requests:
            memory: "128Mi"
            cpu: "2"
          limits:
            memory: "128Mi"
            cpu: "2"
        volumeMounts:
        - name: exec
          mountPath: "/mounts"

---

apiVersion: v1
kind: ConfigMap
metadata:
  name: exec
data:
  start.sh: |
    echo "here I'm"
    apt update && apt install -y stress
    # sleep 3600
    stress -c 1


---


apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: ubuntu
spec:
  maxReplicas: 2
  minReplicas: 1
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ubuntu
  targetCPUUtilizationPercentage: 80


```