---
sort: 2
---

# Kubernetes Theory - Labels and Annotations


## Labels
key/value pairs attached to Kubernetes objects: they  provide identifying metadata for objects.

Provide the foundation for grouping objects: Label selectors are used to filter Kubernetes objects.

Decoupled object need to relate on another: such relationship are set by labels



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



### Attention point

When applying labels to deployments (ex kubectl label deployments nginx "prod=true"), only the label for the deployment object are changed, not the ReplicaSets and Pods it creates.



### Selectors

Label Selectors: are used to filter Kubernetes objects based on a set of labels.

```
kubectl get pods --show-labels
kubectl get pods --selector="ver=2.2,app=backend"
```



Selector operators:

| Operator                     | Description                              |
| :--------------------------- | :--------------------------------------- |
| `key=value`                  | `key` is set to `value`                  |
| `key!=value`                 | `key` is not set to `value`              |
| `key in (value1, value2)`    | `key` is one of `value1` or `value2`     |
| `key notin (value1, value2)` | `key` is not one of `value1` or `value2` |
| `key`                        | `key` is set                             |
| `!key`                       | `key` is not set                         |



Two forms for defining selectors:

- Compact YAML syntax:

```yaml
selector:
matchLabels:
    app: nameapp
matchExpressions:
    - {key: ver, operator: In, values: [v1, v2]}
```

- old way:

```yaml
selector:
app: alpaca
ver: 1
```



## Annotation

Same format as label keys, but with less restrictions on the keys (no validation exists),
they provide a way to store metadata related to tools and libraries (they are not identifying information).

However, because they are often used to communicate information between tools, the namespace part of the key is more important.

Here some common usages:
- keep track of updates and rollouts
- communicate specialized scheduling policies
- prototype alpha functionality in Kubernetes


They are defined in the metadata section, here un example:

```yaml
...
metadata:
  annotations:
    doamin.com/key: "val"
...
```
