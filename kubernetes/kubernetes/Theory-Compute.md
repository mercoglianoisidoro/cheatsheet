---
sort: 4
---

# Compute




## An interesting point of view



| Object     | represent...                                                 | act as ...                                               |
| ---------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| Pod        | represent the littlest deployment unit (one or more container), where replicas is always = 1 (no scaling) | act as a container manager in a single node              |
| ReplicaSet | represent a single, scalable microservice (Pods homogeneous) | acts as a cluster-wide Pod manager                       |
| Deployment | represent deployed applications in a way that transcends any particular version (manage the release of new versions) | acts as a cluster-wide Replicaset manager                |
| Job        | a single run Pod                                             | acts as a cluster-wide Pod manager for one-shot pod runs |
| DaemonSets | single Pod on every node                                     | acts as a cluster-wide Pod manager for one pod per host  |





## Pods

The smallest deployable artifact in a Kubernetes cluster: a group of one or more containers (shared storage, network resources, and containers specification)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```



Port-forwarding:

```bash
kubectl port-forward POD_NAME 8080:8080
```



Execute sh on a container:

```bash
kubectl exec -it POD_NAME sh
```



### Health Checks

ensures that the main process running, otherwise Kubernetes restarts it.

**Different goals of probe:**

- liveness (livenessProbe)
- readiness (readinessProbe)
- startup (startupProbe)

**Different type of probe:**

- HTTP checks
- tcpSocket
- exec

**Liveness example:**

Liveness health checks run application-specific logic to verify that the application is not just still running, but is functioning properly.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - image: bla
      name: nginx
      livenessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP

```



Three options for the restart policy:

- Always (the default)
- OnFailure (restart only on liveness failure or nonzero process exit code)
- Never

more at: [https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)



### Resource Requests

- Resource requests: minimum amount of a resource required to run the application
- Resource limits: maximum amount of a resource that an application can consume

They are requested per container.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - image: bla
      name: nginx
      resources:
        requests:
          cpu: "500m"
          memory: "128Mi"
        limits:
          cpu: "1000m"
          memory: "256Mi"
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP

```



### Persistent Data

hostPath:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  volumes:
    - name: "app-data"
      hostPath:
        path: "/var/lib/app"
  containers:
    - image: app
      name: app
      volumeMounts:
        - mountPath: "/data"
          name: "app-data"
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP

```



## ReplicaSets

acts as a cluster-wide Pod manager.

With it we define a specification for the Pods and a desired number of replicas.
Designed to represent a single, scalable microservice (Pods homogeneous).

Implicitly we define a way of finding Pods that the ReplicaSets control. (The act of managing the replicated Pods is an example of a reconciliation loop)

ReplicaSets create and manage Pods, but they do not own the Pods they create (loosely coupling).
ReplicaSets use labels to identify the set of Pods they should be manage. => ReplicaSets can "adopt" pods, or pods can be quarantined


### Minimal example:

```yaml
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: example
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: example
        version: "2"
    spec:
      containers:
        - name: NAME
          image: "image address"
```



### Annotation

Automatic annotation are set ad pod level for pods managed by ReplicaSets (kubernetes.io/created-by)



## Deployments

Manage the release of new versions.
Deployments represent deployed applications in a way that transcends any particular version.

Deployments manage ReplicaSets: this relationship is defined by labels and a label selector.

Deployments:
- let to easily move from versions ("rollout" process can be precisely controlled, it also uses health checks and stops the deployment in case of problem)

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

### Strategy
The strategy object prescribes how rollout new versions.
The 2 strategies supported by deployments are: Recreate and RollingUpdate.

**Recommendation**: use annotation to record some information about the update.
```yaml
spec:
  ...
  template:
    metadata:
      annotations:
        kubernetes.io/change-cause: "Update to ... "
```




- kubectl rollout history: let to obtain the history of rollouts associated with a particular deployment
- kubectl rollout status (deployment NAME): let to obtain the current status of a rollout.
- kubectl rollout pause deployments NAME: to pause a rollout process
- kubectl rollout history deployment NAME: to get the history
- kubectl rollout undo deployments NAME



#### Recreate Strategy
Updates the ReplicaSet and terminates all pods: they will be recreated.
It results in **downtime**.



#### RollingUpdate Strategy
Let to roll out a new version while the previous is still receiving traffic, **without any downtime**.

It works by updating a few Pods at a time.
Both the new and the old version of your service receive requests => important implications for how you build your sw (true is, you always had this problem).

Two parameter let to configure this strategy:

- maxUnavailable : maximum number of Pods that can be unavailable during a rolling update (absolute or percentage). This reduce the number of pods receiving traffic during updates

- maxSurge : how many extra pods can be created to achieve a rollout (absolute or percentage). This let to have always, at least, the 100% of pods receiving traffic.



### Observations

- Recreate strategy = RollingUpdate strategy with maxUnavailable set to 100%
- maxSurge = 100% is equivalent to a blue/green deployments
- deployment controller get the Pod’s status thanks to  readiness checks, that are parts of the  health probes: so it's important to specify them.



### More control on the rollout process

Time to wait for a Pod become healthy.
If noticing that a Pod has become ready doesn’t provide enough confidence (example memory leak.. ), you can define a time to wait before moving on rollouts.

```yaml
spec:
  minReadySeconds: 60
```

If a deployment may get stuck trying to deploy its newest ReplicaSet without ever completing (Insufficient quota,Readiness probe failures,
Image pull errors, Insufficient permissions, Limit ranges Application runtime misconfiguration),
you can set a timeout.
```yaml
spec:
  progressDeadlineSeconds: 600
```
This means: if any particular stage in the rollout fails to progress in 10 minutes, then the deployment is marked as failed
Timeout applies on deployment progress, not the overall length of a deployment

To get the status of a deployment: see the status.conditions =Progressing |False




## Jobs


A job creates Pods that run until successful termination (i.e., exit with 0). If the Pod fails before a successful termination, the job controller will create a new Pod based on the Pod template in the job specification.

Jobs are useful for things you only want to do once, such as database migrations or batch jobs.

Note:

- in contrast, a regular Pod will continually restart regardless of its exit code.
- due to the nature of distributed systems there is a small chance, during certain failure scenarios, that duplicate Pods will be created for a specific task


The Job object is responsible for creating and managing Pods. The Job object coordinates running a number of Pods in parallel.



### job patterns

highlights job patterns based on the combination of `completions` and `parallelism` for a job configuration.

from 'Kubernetes: Up and Running, 2nd Edition', By Brendan Burns, Joe Beda and Kelsey Hightower:

| Type                       | Use case                                               | Behavior                                                     | completions | parallelism |
| :------------------------- | :----------------------------------------------------- | :----------------------------------------------------------- | :---------- | :---------- |
| One shot                   | Database migrations                                    | A single Pod running once until successful termination       | 1           | 1           |
| Parallel fixed completions | Multiple Pods processing a set of work in parallel     | One or more Pods running one or more times until reaching a fixed completion count | 1+          | 1+          |
| Work queue: parallel jobs  | Multiple Pods processing from a centralized work queue | One or more Pods running once until successful termination   | 1           | 2+          |



Example:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: oneshot
spec:
  template:
    spec:
      containers:
      - name: bla
        image: bla
        imagePullPolicy: Always
        args:
        - "--bla"
      restartPolicy: OnFailure
```



## CronJobs

Let to schedule Jobs

Example:

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-isidoro
spec:
  schedule: "0 22 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cronjobcontainername
            image: image
          restartPolicy: OnFailure
```



## DaemonSets

Let to deploy a single Pod on every node of the cluster.

Using labels you can target specific nodes. You can use nodeSelector select nodes.

Usage example: to act directly on hosts (for example install same sw on every host)

DaemonSets can be rolled out using the same `RollingUpdate` strategy used by deployment (kubectl rollout status daemonSets NAME)

