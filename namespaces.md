# Kubernetes Namespaces

---

```bash
k get namespaces
```

default # to create default resources
kube-node-lease # node heartbeat info
kube-public # public data (configMap with cluster info -> k cluster-info)
kube-system # system processes

## Use

```bash
k create namespace test
k delete namespace test
```

* Use to organize your resources in complex applications or with multiple teams that use the same cluster.

* You cannot communicate between files in different namespaces - each namespace has to have it's own ConfigMap

* You can share services

