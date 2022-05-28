---
sort: 1
---

# Some Useful Commands

## status

```bash
kubectl version --short
kubectl get componentstatus
kubectl get nodes
kubectl cluster-info

```

## general info
- kubectl api-resources : to show api
- kubectl api-versions :great to find information for manifests
- kubectlm explain

## explain

```bash
kubectl explain deployment.spec.template.spec.containers.envFrom.configMapRef
```

## execute a pod in iterative way

```bash
kubectl run -it  --image ubuntu bash
```
```bash
kubectl run -i --tty ubuntu --image=ubuntu -- sh
```

```bash
kubectl run -i -t busybox --image=busybox --restart=Never
```

```bash
kubectl run -t -i sshd --image=mercisi/ubuntu18-supervisord:sshd --restart=Never --env="ROOT_PASSWORD=tre" --env="POD_NAMESPACE=default" --command -- bash
```

## execute a bash in a pod

```bash
k exec  --stdin --tty  dumb-server-9d7b889f9-6z284 -- bash
```


## set default namespace

```bash
kubectl config set-context --current --namespace=NAMESPACE
```

## get event ordered by time

```bash
kubectl get events --sort-by=.metadata.creationTimestamp -w
```


## create manifest from kubectl

```bash
kubectl create deploy .... --dry-run -o yaml
```


## proxy

```bash
kubectl port-forward --namespace redis service/my-redis-master 6379:6379 > /dev/null
```


## events
get odered events
```bash
kubectl get events --sort-by=.metadata.creationTimestamp -w
```




<!--
## Apply Manifest with annotation

This adds an annotation so that, when applying changes in the future, kubectl will know what the last applied configuration was for smarter merging of configs.

```bash
kubectl replace --save-config -f MANIFEST.yaml
``` -->
