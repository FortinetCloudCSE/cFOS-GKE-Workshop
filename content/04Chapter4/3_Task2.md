---
title: "Task 2 - Validate Demo Application"
menuTitle: "Validate Demo Application"
weight: 2
---

### Validate Demo Application deployment

Validate Demo Application deployment

```
kubectl rollout status deployment multitool01-deployment
```

> output will be similar as below

![envOutput](v-demo-app-1.png)

```
kubectl get pod -l app=multitool01
```

> output will be similar as below

![envOutput](v-demo-app-2.png)

```
nodeName=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}') && for node in $nodeName; do podName=$(kubectl get pods -l app=multitool01 --field-selector spec.nodeName="$node" -o jsonpath='{.items[*].metadata.name}') ; kubectl exec -it po/$podName -- ip route && kubectl exec -t po/$podName -- ip address ; done
```

> output will be similar as below

![envOutput](v-demo-app-3.png)
