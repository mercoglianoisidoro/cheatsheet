---
sort: 2
---

# Kubernetes Theory - General



## Kubernetes
Kubernetes is build on the concept of transient, decoupled objects connected together.

## Labels
Labels provide identifying metadata for objects.

Two parts, separated by a slash:
- optional prefix (must be a DNS subdomain with a 253-character limit)
- a name (start and end with an alphanumeric character and permit the use of dashes (-), underscores (_), and dots (.) between characters)

Apply labels:
```
--labels="ver=1,app=backend
```
See:
```
kubectl get deployments --show-labels
```
change:
```
kubectl label deployments deployment_name "label=value"
```

Label Selectors: are used to filter Kubernetes objects based on a set of labels.
```
kubectl get pods --show-labels
kubectl get pods --selector="ver=2.2,app=backend"
```

## Annotation
Same format as label keys.
However, because they are often used to communicate information between tools, the namespace part of the key is more important
