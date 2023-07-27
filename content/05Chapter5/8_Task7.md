---
title: "Task 7 - Validate cFOS log for retriving config from configmap"
menuTitle: "Validate cFOS log for retriving config from configmap"
weight: 7
---

### Validate cFOS log for retriving config from configmap

```
nodeName=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}') && for node in $nodeName; do podName=$(kubectl get pods -l app=fos --field-selector spec.nodeName="$node" -o jsonpath='{.items[*].metadata.name}') ; kubectl logs po/$podName ; done
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
System is ready.

2023-05-18_07:42:16.77441 ok: run: /run/fcn_service/certd: (pid 282) 1s, normally down

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