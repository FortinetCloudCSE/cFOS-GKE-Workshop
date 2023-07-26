---
title: "Task 4 - Validate GKE Cluster"
menuTitle: "Validate GKE Cluster"
weight: 4
---

### Validate GKE Cluster

```
kubectl get node -o wide
```

> output will be similar as below

```
NAME                                                STATUS   ROLES    AGE   VERSION            INTERNAL-IP   EXTERNAL-IP      OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
gke-my-first-cluster-1-default-pool-c10fbf4f-3d1s   Ready    <none>   33s   v1.26.3-gke.1000   10.0.0.4      35.189.160.192   Ubuntu 22.04.2 LTS   5.15.0-1028-gke   containerd://1.6.18
gke-my-first-cluster-1-default-pool-c10fbf4f-5qxh   Ready    <none>   33s   v1.26.3-gke.1000   10.0.0.3      34.80.212.63     Ubuntu 22.04.2 LTS   5.15.0-1028-gke   containerd://1.6.18
```