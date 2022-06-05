---
sort: 11
---

# Random Notes

## On nodes

### Reboot node

1. kubectl cordon node: to avoid new pods scheduling
2. kubectl drain node: to remove pods
3. reboot

After the reboot there is a self registration.

Conf is in:

- /etc/k../kubelet/config
- /var/lib/kubelet/config.yaml

### Remove node

1. cordon
2. drain
3. kubectlm delete node

### Add node

1. on master: k token create ...
2. on node: k join ...  (edit the /etc/hosts)

### Kubelet static pods

kubelet starts pods from /etc/kubelet/manifest)

## Random

- Application Platform: viable place to run workloads.
- Cluster Federation: broadly refers to how to centrally manage all the cluster under you control
- Docker Pause: it's needed by networking
- every pod can communicate with other: if you need to change this behavior => network policies (this must be supported by network plugin-in)
- taints: are applied to a node to mark that the node should not accept pod that doesn't tolerate the taint. Where affinities are used on pod to attract them to a specific node, taints allow a node to repel a set of pods.
- 3 types of taints:
  1. NoSchedule: doesn't schedule new pods
  2. PreferNoSchedule: doesn't schedule nnew pods unless there is other option
  3. NoExecute: migrate all pods away from this node

- Taints and toleration have no effect on daemonset

- if CNI is used => /etc/cni/net.d

