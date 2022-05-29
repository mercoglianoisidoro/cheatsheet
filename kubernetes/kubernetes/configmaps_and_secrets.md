---
sort: 8
---

# ConfigMap and Secrets

## ConfigMap

object used to store non-confidential data in key-value pairs



### Creation

with kubectl:

```bash
kubectl create configmap my-configmap \
  --from-file=file.txt \
  --from-literal=param1=value1 \
  --from-literal=param2=value2
```

with manifest (equivalent):

```yaml
apiVersion: v1
data:
  param1: param1
  param2: -value2
  my-config.txt: |
    parameter1 = value1
    parameter2 = value2
kind: ConfigMap
metadata:
  name: my-configmap
  namespace: default
```



Kubectl usage:

--from-file=<filename>
--from-file=<key>=<filename>
--from-file=<directory>
--from-literal=<key>=<value>



### Usage

- Filesystem

  mount a ConfigMap into a Pod: each entry a file with the key as name, and content with the value.

- Environment variable
- Command-line argument
  command line for a container based on ConfigMap values

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: namebla
spec:
  containers:
    - name: test
      image: image
      imagePullPolicy: Always
      command:
        - "/execute"
        - "$(EXTRA_PARAM)"
      env:
        - name: PARAM1
          valueFrom:
            configMapKeyRef:
              name: my-configmap
              key: param1
        - name: PARAM2
          valueFrom:
            configMapKeyRef:
              name: my-configmap
              key: param2
      volumeMounts:
        - name: config-volume
          mountPath: /config
  volumes:
    - name: config-volume
      configMap:
        name: my-configmap
  restartPolicy: Never
```


## Secrets

object that contains a small amount of sensitive data

### Warning

By default, Kubernetes secrets are stored in plain text in the etcd storage for the cluster: any cluster admin can access to them. In recent versions of Kubernetes there is the support for encrypting the secrets with a user-supplied key. Anyway most cloud providers propose a key store: secret can rely on exclusively on the cloud provider service.

### Creation

Create from file:

```bash
kubectl create secret generic db-user-pass \
  --from-file=username=./username.txt \
  --from-file=password=./password.txt
```

note: key is optional (--from-file=[key=]source); the default key name is the filename:

```bash
kubectl create secret generic db-user-pass \
  --from-file=./username.txt \
  --from-file=./password.txt
```

Create from string:

```bash
kubectl create secret generic db-user-pass \
  --from-literal=username=devuser \
  --from-literal=password='S!B\*d$zDsb='
```



### Read secret

```bash
kubectl get secret db-user-pass -o jsonpath='{.data}' | base64 --decode
```

### Usage

- Filesystem

  as previus

- Environment variable

- By the kubelet when pulling images for the Pod
  The `imagePullSecrets` field is a list of references to secrets in the same namespace. You can use an `imagePullSecrets` to pass a secret that contains a Docker (or other) image registry password to the kubelet.

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: NAME
spec:
  containers:
    - name: name
      image: image_name
      imagePullPolicy: Always
      volumeMounts:
      - name: tls-certs
        mountPath: "/tls"
        readOnly: true
  imagePullSecrets:
  - name:  my-image-pull-secret
  volumes:
    - name: tls-certs
      secret:
        secretName: secretname
```

where previously a secret is created:

```bash
kubectl create secret docker-registry my-image-pull-secret \
  --docker-username=<username> \
  --docker-password=<password> \
  --docker-email=<email-address>
```

