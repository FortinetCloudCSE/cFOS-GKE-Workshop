---
title: "Task 2 - Validate Multus CNI"
menuTitle: "Validate Multus CNI installation"
weight: 2
---

### Validate Multus CNI installation

```
kubectl rollout status ds/kube-multus-ds -n kube-system
kubectl logs ds/kube-multus-ds -c kube-multus -n kube-system
```


> output will be similar as below

![envOutput](validate-multus.png)
