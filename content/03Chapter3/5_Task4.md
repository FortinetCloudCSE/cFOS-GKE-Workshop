---
title: "Task 4 - Validate net-attach-def"
menuTitle: "Validate net-attach-def"
weight: 4
---

### Validate net-attach-def

```
kubectl get net-attach-def cfosdefaultcni5 -o yaml
```

> output will be similar as below

```
apiVersion: k8s.cni.cncf.io/v1
kind: NetworkAttachmentDefinition
metadata:
  creationTimestamp: "2023-05-18T07:41:51Z"
  generation: 1
  name: cfosdefaultcni5
  namespace: default
  resourceVersion: "2821"
  uid: 4314fde9-cc8f-4744-bb99-8083f1e6a5e3
spec:
  config: '{ "cniVersion": "0.3.1", "type": "macvlan", "master": "ens4", "mode": "bridge",
    "ipam": { "type": "host-local", "subnet": "10.1.200.0/24", "rangeStart": "10.1.200.251",
    "rangeEnd": "10.1.200.253", "gateway": "10.1.200.1" } }'
```