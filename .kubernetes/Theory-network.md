---
sort: 2
---

# Kubernetes Theory - Networks


## Services

With services you can reach any node in the cluster.

Cluster IPs are stable virtual IPs that load-balance traffic across all of the endpoints in a service. Behind the scene, this is managed by kube-proxy.

You can use selector-less services to manually assigned **IPs outside** the cluster.

## Ingress
Ingress is a Kubernetes-native way to implement the “virtual hosting” pattern.

There is no “standard” Ingress controller that is built into Kubernetes, so the user must install one of many optional implementations.

The intersection of Ingress and Service Meshes is not clear.

Example:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple-ingress
spec:
  backend:
    serviceName: test
    servicePort: 8080
```

Example with hostname:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: host-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - backend:
          serviceName: test
          servicePort: 8080

```

Example with path (the longest prefix wins):
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: path-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: "/"
        backend:
          serviceName: service1
          servicePort: 8080
      - path: "/a/"
        backend:
          serviceName: service2
          servicePort: 8080

```

The path remains unmodified to the upstream service => the upstream service needs to be ready to serve traffic on that subpath.

Path Rewriting is supported only by some Ingress controller implementations.

### Ingress and Namespaces

An Ingress object can only refer to an upstream service in the same namespace.
But multiple Ingress objects in different namespaces can specify subpaths for the same host. These Ingress objects are then merged together to a unique final config for the Ingress controller.

### Serving TLS
Users need to specify a secret with their TLS certificate and keys.
Example:
```yaml
apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: null
  name: tls-secret-name
type: kubernetes.io/tls
data:
  tls.crt: <base64 encoded certificate>
  tls.key: <base64 encoded private key>

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: tls-ingress
spec:
  tls:
  - hosts:
    - example.com
    secretName: tls-secret-name
  rules:
  - host: example.com
    http:
      paths:
      - backend:
          serviceName: test
          servicePort: 8080

```

To reduce complexity: https://letsencrypt.org/
(you can set up Kubernetes to automatically fetches and installs TLS certificates)

https://github.com/jetstack/cert-manager

### Ingress Controllers

- Contour: a controller built to configure the open source (and CNCF project) load balancer called Envoy
- ingress-nginx: https://github.com/kubernetes/ingress-nginx/
- Emissary-Ingress: an open-source Kubernetes-native API Gateway + Layer 7 load balancer + Kubernetes Ingress built on Envoy Proxy (https://github.com/emissary-ingress/emissary)
- Gloo Edge: a feature-rich, Kubernetes-native ingress controller, and next-generation API gateway
- Traefik: a reverse proxy that can function as an Ingress controller
