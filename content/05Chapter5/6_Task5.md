---
title: "Task 5 - Validate cFOS license"
menuTitle: "Validate cFOS license"
weight: 5
---

### Validate cFOS license

```
nodeName=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}') && for node in $nodeName; do podName=$(kubectl get pods -l app=fos --field-selector spec.nodeName="$node" -o jsonpath='{.items[*].metadata.name}') ; kubectl exec -it po/$podName -- ip route && kubectl exec -t po/$podName -- ip address ; done
```

> output will be similar as below

```
System is starting...

Firmware version is 7.2.0.0231
Preparing environment...
INFO: 2023/05/18 07:42:15 importing license...
INFO: 2023/05/18 07:42:15 license is imported successfuly!
WARNING: System is running in restricted mode due to lack of valid license!
Starting services...

System is starting...

Firmware version is 7.2.0.0231
Preparing environment...
INFO: 2023/05/18 07:42:14 importing license...
INFO: 2023/05/18 07:42:14 license is imported successfuly!
WARNING: System is running in restricted mode due to lack of valid license!
Starting services...
System is ready.

2023-05-18_07:42:15.02058 ok: run: /run/fcn_service/certd: (pid 272) 1s, normally down
```
