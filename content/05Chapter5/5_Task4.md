---
title: "Task 4 - Check Routing Table and IP Address"
menuTitle: "Check Routing Table and IP Address"
weight: 4
---

### Check Routing Table and IP Address

```
nodeName=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}') && for node in $nodeName; do podName=$(kubectl get pods -l app=fos --field-selector spec.nodeName="$node" -o jsonpath='{.items[*].metadata.name}') ; kubectl exec -it po/$podName -- ip route && kubectl exec -t po/$podName -- ip address ; done
```

> output will be similar as below

![envOutput](routing-table.png)
