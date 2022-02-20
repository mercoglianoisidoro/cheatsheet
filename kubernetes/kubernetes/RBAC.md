---
sort: 2
---

# Role Base Access Control

## Intro

To perform each action, 3 steps:
1. Authentication: provides the identity of the caller
2. Authorization (ABAC or RBAC): check if the user has permission to run that action
3. Admission Control: check the actual content of the object being created and validate them before admitting the request.

The 3 step use TLS: they need a certificate.

### Authentication

In short: made by certificates, token or basis auth (user/pass).
Interestingly enough, Kubernetes does not have a built-in identity store: it integrates other identity sources.
So users are not created by API.

The form of Authentication are set by init script (option command like --basic-auth-file)

Here some provider supported:
- HTTP Basic Authentication (deprecated)
- x509 client certificates
- Static token files on the host
- Cloud authentication providers
- Authentication webhooks

### Identity

Every request is associated with an identity (Requests with no identity are associated with the system:unauthenticated).
2 types:

- Normal User: cannot be added via an API call, any user that presents a valid certificate signed by the cluster's certificate authority (CA) is considered authenticated.
- Service accounts: are users managed by the Kubernetes API and are generally associated with components running inside the cluster. Service accounts are tied to a set of credentials stored as Secrets, which are mounted into pods allowing in-cluster processes to talk to the Kubernetes API

### Authorization

3 modes:
- ABAC (Attribute Based Access Control, it needs the autothentication policy file, a json file)
- RBAC (Role Based Access Control, the default one)
- Webhook

(passed to API server startup options:
 --authorization-mode = ABAC, RBAC, Webhook, Alway Deny, Always Allow)

## RBAC

### Role and RoleBinding

**Roles** = a group of rules. They affect **a single namespace**.
Rules = operation which can act upon API group
By a binding they apply to:
- User Accounts (which doesn't exist as API object)
- Service Account
- Groups (known as ClusterRoleBinding)

In other words:
A **role** (set of abstract capabilities) can be assigned to one or more identity through a **role binding**.
Role binding applies to 2 pairs of related resources:
- Role and RoleBinding
- ClusterRole and ClusterRoleBinding


**Role example** (from ubernetes.io)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

**RoleBinding example** (from ubernetes.io)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "jane" to read pods in the "default" namespace.
# You need to already have a Role named "pod-reader" in that namespace.
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
# You can specify more than one "subject"
- kind: User
  name: jane # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io

```

**Verbs for Kubernetes roles**: roughly = HTTP methods

| Verb     | HTTP method | Description                                          |
| :------- | :---------- | :--------------------------------------------------- |
| `create` | `POST`      | Create a new resource.                               |
| `get`    | `GET`       | Get a resource.                                      |
| `list`   | `GET`       | List a collection of resources.                      |
| `patch`  | `PATCH`     | Modify an existing resource via a partial change.    |
| `update` | `PUT`       | Modify an existing resource via a complete object.   |
| `watch`  | `GET`       | Watch for streaming updates to a resource.           |
| `proxy`  | `GET`       | Connect to resource via a streaming WebSocket proxy. |
| `delete` | `DELETE`    | Delete an existing resource.                         |



### ClusterRole

**clusterroles**: a set of well-known system identities that require a known set of capabilities.

4 are designed for generic end users:
- 'cluster-admin': complete access

- 'admin': access to a complete namespaceThe

- 'edit': allows an end user to modify things in a namespace

- 'view': allows for read-only access to a namespace.


ClusterRole is a non-namespaced resource.

ClusterRoles have several uses. You can use a ClusterRole to define permissions on:

- namespaced resources and be granted within individual namespace(s)
- namespaced resources and be granted across all namespaces
- cluster-scoped resources


**To get them:**

```bash
kubectl get clusterroles
```
or
```bash
kubectl get clusterrolebindings
```



**Attention points:**

1. A certain number of ClusterRoles come directly from the code of the servcer:  modification to these built-in roles are transient.
   To avoid this problem: you need to add the rbac.authorization.kubernetes.io/autoupdate annotation with a value of false to the built-in ClusterRole resource.

2. By default, a cluster role system:unauthenticated is intalled: it allows users to access to the API server’s API discovery endpoint.This could be a bad idea: for cluster exposed on the public internet or an other hostile environment, you should ensure that the --anonymous-auth=false flag is set on your API server.


### ClusterRole VS Roles

If you want to define a role within a namespace, use a Role; if you want to define a role cluster-wide, use a ClusterRole.

### Testing tools

```bash
kubectl auth can-i create pods
```

### Aggregation

Kubernetes RBAC supports aggregation rule to combine multiple roles.

This is achieved by using label selectors.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: edit
  ...
aggregationRule:
  clusterRoleSelectors:
  - matchLabels:
      rbac.authorization.k8s.io/aggregate-to-edit: "true"
...
```



### Groups for Bindings

It’s generally a best practice to use groups to manage the roles that define access to the cluster.

Groups are supplied by authentication providers.

To bind a group to a ClusterRole:

```yaml
...
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: my-great-groups-name
...
```




## Admission Control
These controllers have been added into  the binary of the kube-apiserver.
They can be enabled or disabled.

## Security Contexts
Security limitations on containers and pods (ex. UID of a process, linux capabilities, etc).
They are defined on additional section in resource manifests. Ex: spec.securityContext.runAsNonRoot: true
To automate the enforcement of that requirement,upi can definePodSecurityPolicies at cluster level.
