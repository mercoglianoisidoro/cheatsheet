---
sort: 7
---

# Kubernetes Theory - Scheduling


## Kubernetes scheduler (kube-scheduler)
its responsibility is to schedule container workloads (where to instantiate pods) and assign them to worker nodes.
This pass through a set of steps needed to guarantee the selected node is healthy and the policy demanded by the pods is respected.

### Some more technical information

Normally, for managed Kubernetes clusters you haven't access to the Master: you can't access to the kube-scheduler itself, and usually, you can't control its configuration, such as scheduling policies.

You can inspect the logs for the kube-scheduler pod using the kubectl logs command, or even directly at the master nodes in /var/log/kube-scheduler.log. https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/.

## Scheduling process

Kube-scheduler queries the Kubernetes API Server (kube-apiserver) at a regular interval in order to list the pods that have not been scheduled.

Once that kind of pods are found, the scheduling process (elect a worker node) is composed by 2 phases: 

1. **Filtering**: determines the nodes that are capable of running a given pod
2. **Scoring**: assigns scores for each node based on a set of scheduling policies: the pod is then assigned by the scheduler to the node with the highest score

Kube-scheduler will update the nodeName property in the pod object (by HTTP request to the kube-apiserver). This will trigger the kubelet on the selected worker node to run the pod.



### After scheduling

When the pod is assigned to a node and is running, kube-scheduler will not reschedule it (while it is running).

In that state, even if this pod no longer matches the node, no rescheduling it done.

Rescheduling will only happen if the pod is terminated and a new pod needs to be scheduled.

An incubating  Kubernetes project named Descheduler wants solve this type of issue: https://github.com/kubernetes-sigs/descheduler.



## pod node name

it the property (nodeName) of the pod defining the node on which the pods is executed.

The property is identified by: **pod.spec.nodeName**

It's possible to set it directly in the YAML manifest: this is a form of statical scheduling (not recommended! => not flexible and not scalable): this way we actually bypasse kube-scheduler.



## pod node selector

The field .spec.nodeSelector provide the ability to schedule your the pod only on nodes that have certain label values.

```yaml
...
	spec:
      nodeSelector:
        key: value
...
```

This is very useful to select some type of machine (check label with kubectl describe node NAME).

## node affinity

docs: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/

It improves the previous: it provides a richer approach (prefer or avoid).

The property is identified by: **pod.spec.affinity.nodeAffinity**

It's possible to scheduling using:

-  **pods affinity** (**podAffinity**) (hard rule)

  - inter-pod affinity

  - anti-affinity (podAffinity) (repel pods): `requiredDuringSchedulingIgnoredDuringExecution`

    

- **soft affinity**: preferred (soft rule)

  - affinity
  - anti-affinity: `preferredDuringSchedulingIgnoredDuringExecution` 



TODO:

If you specify multiple `nodeSelectorTerms` associated with `nodeAffinity` types, then the Pod can be scheduled onto a node if one of the specified `nodeSelectorTerms` can be satisfied.

If you specify multiple `matchExpressions` associated with a single `nodeSelectorTerms`, then the Pod can be scheduled onto a node only if all the `matchExpressions` are satisfied.





**Example:**

```yaml
apiVersion: v1
kind: pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: topology.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: topology.kubernetes.io/zone
  containers:
  - name: with-pod-affinity
    image: k8s.gcr.io/pause:2.0
```



**TODO**: https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/

### topology domain

The topologyKey allows a general grouping of Pod deployments. Affinity (or the inverse anti-affinity) will try to run on nodes with the declared topology key and running Pods with a particular label. 

from [https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

> Inter-pod affinity and anti-affinity rules take the form "this pod should (or, in the case of anti-affinity, should not) run in an X if that X is already running one or more pods that meet rule Y", where X is a topology domain like node, rack, cloud provider zone or region, or similar and Y is the rule Kubernetes tries to satisfy.
>
> You express the topology domain (X) using a `topologyKey`, which is the key for the node label that the system uses to denote the domain. For examples, see [Well-Known Labels, Annotations and Taints](https://kubernetes.io/docs/reference/labels-annotations-taints/).





## Node taints and tolerations

It's a mechanism to specify which node pods should repel.

A **taint** is applied to a node => a pod can be assigned to that node only it has a specific **toleration** to that taint.

In other words: all pods will avoid the node it there is a taint on it but we can instruct the pods to tolerare it.

Definition: <key>=<value>:<effect>.

key / value pair let to identify the the taint:  they are similar to labels, but they are separate properties.



### Effects

- **NoSchedule** – kube-scheduler *will not schedule* pods to this node (same as hard node affinity rule)

- **PreferNoSchedule** – kube-scheduler *will try to not schedule* pods to this node (same soft node affinity rule)

- **NoExecute** – kube-scheduler *will not schedule* pods to this node and *evict* (terminate and reschedule) running pods from this node. (**impossible to do with affinities**).

  In this case you can also define a **tolerationSeconds** (how long the pod will tolerate it)

**NoExecute** already managed by kubernetes:

- node.kubernetes.io/not-ready ( NodeCondition 'Ready' = false )
- node.kubernetes.io/unreachable ( NodeCondition 'Ready' = Unknown status. Example node is unreachable)
- node.kubernetes.io/out-of-disk
- node.kubernetes.io/memory-pressure (node is experiencing memory pressure)
- node.kubernetes.io/disk-pressure (node  is experiencing disk pressure)
- node.kubernetes.io/network-unavailable (node network down)
- node.kubernetes.io/unschedulable (node is currently in an unschedulable state)
- node.cloudprovider.kubernetes.io/uninitialized (temporary taint attached by a cloud provider during prepararion of a node)





## Examples