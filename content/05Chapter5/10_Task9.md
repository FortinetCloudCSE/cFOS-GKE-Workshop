---
title: "Task 9 - Validate deployment status of cFOS"
menuTitle: "Validate status of cFOS"
weight: 9
---

### Validate deployment status of cFOS after the restart

```
kubectl rollout status ds/fos-deployment
```

> output will be similar as below

```
daemon set "fos-deployment" successfully rolled out
```