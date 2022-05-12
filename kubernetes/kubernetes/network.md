---
sort: 5
---

# Network


## Services

With services you can reach any node in the cluster.

Cluster IPs are stable virtual IPs that load-balance traffic across all of the endpoints in a service. Behind the scene, this is managed by kube-proxy.

You can use selector-less services to manually assigned **IPs outside** the cluster.

### Example

```yaml


apiVersion: v1
kind: Service
metadata:
  name: servicename
spec:
  ports:
  - port: <externalport>
    protocol: TCP
    targetPort: <internalport>
  selector:
    app: <selector-for-pods>
  type: LoadBalancer|ClusterIP|NodePort|ExternalName

```

Note: selector-for-pods need to link the service with pods.

### Types

- ClusterIP: service is exposed on an cluster internal IP
- NodePort: service is exposed on a  port on each node
- LoadBalancer: use the load balancer provided by the cloud provider
- ExternalName: return a CNAME

### External Services

#### Point to external DNS

the following service, of type ExternalName, let to create a service (so an IP in the cluster is created) populated with an CNAME record (instead of a A record as for the other types) that point to the external name spacified.

```yam
kind: Service
apiVersion: v1
metadata:
  name: external-service
spec:
  type: ExternalName
  externalName: address-external-service.com
```

Such a strategy let to create a service to map a internal/external resource that can change to a be external/internal without changing the other resource using it.

#### Point to external IP

In the case you have only a IP for an external service, you need a A record:

1. create a service without selector and without the external name: kubernetes will allocate a virtual IP and an A record, but no endpoint will be populated.
```yaml
kind: Service
apiVersion: v1
metadata:
  name: external-ip-service

```
2. create manually the endpoint

```yaml
kind: Endpoints
apiVersion: v1
metadata:
  name: external-ip-service
subsets:
  - addresses:
    - ip: 192.168.0.180
    ports:
    - port: 3306
```

The addresses can be multiple (load balancing)

#### Note

external services don't use health checks



## Services vs Ingresses

Service operate at Layer 4 of the OSI model: they forward TCP and UDP without analyzing the traffic.

Ingresses operate at Layer 7 of the OSI model: they let to implement other feature like virtual hosting.



## Ingress
Ingress is a Kubernetes-native way to implement the “virtual hosting” pattern.

The ingresses are merged in a unique configuration managed by the ingress controller but there is no “standard” Ingress controller built into Kubernetes: a controller must be installed from one of many optional implementations.

if you specify duplicate or conflicting configurations, the behavior is undefined.

The intersection of Ingress and Service Meshes is not clear.

Kubectl imperative command for ingresses is very limitated.

### Ingress and Namespaces

An Ingress object can only refer to an upstream service in the same namespace.
But multiple Ingress objects in different namespaces can specify subpaths for the same host. These Ingress objects are then merged together to a unique final config for the Ingress controller.

A global coordina

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

To reduce complexity:

- https://letsencrypt.org/
  (you can set up Kubernetes to automatically fetches and installs TLS certificates)
- https://github.com/jetstack/cert-manager



### Examples

Base ingress forwarding everything to a service:

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
      - path: "/a"
        backend:
          serviceName: service1
          servicePort: 8080
      - path: "/b/"
        backend:
          serviceName: service2
          servicePort: 8080

```

The path remains unmodified to the upstream service => the upstream service needs to be ready to serve traffic on that subpath.



### Annotation and advanced features

Path Rewriting is supported only by some Ingress controller implementations.

Many feature are activated by annotation. To scope annotation, you can used different ingresses

The annotation `kubernetes.io/ingress.class` is used to specify the ingress controller used by the ingress: this is particularly useful in the case of multiple ingress controllers are installed. if it is missing, behavior is undefined.



### default-http-backend

Fallback for requests not matching the specified rules.

Convention not universally supported.

### Ingress Controllers

- Contour: a controller built to configure the open source (and CNCF project) load balancer called Envoy
- ingress-nginx: https://github.com/kubernetes/ingress-nginx/
- Emissary-Ingress: an open-source Kubernetes-native API Gateway + Layer 7 load balancer + Kubernetes Ingress built on Envoy Proxy (https://github.com/emissary-ingress/emissary)
- Gloo Edge: a feature-rich, Kubernetes-native ingress controller, and next-generation API gateway
- Traefik: a reverse proxy that can function as an Ingress controller
