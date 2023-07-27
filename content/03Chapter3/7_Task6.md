---
title: "Task 6 - Validate net-attach-def for application"
menuTitle: "Validate net-attach-def"
weight: 6
---

### Validate net-attach-def for application

```
kubectl get net-attach-def cfosapp -o yaml
```

> output will be similar as below

```
apiVersion: k8s.cni.cncf.io/v1
kind: NetworkAttachmentDefinition
metadata:
  creationTimestamp: "2023-05-18T07:41:52Z"
  generation: 1
  name: cfosapp
  namespace: default
  resourceVersion: "2831"
  uid: 6618ec98-5f33-47a8-9c69-fe61ea382034
spec:
  config: |-
    { "cniVersion": "0.3.1", "type": "macvlan", "master": "ens4", "mode": "bridge", "ipam": { "type": "host-local", "subnet": "10.1.200.0/24", "routes": [
    { "dst": "104.18.8.132/32", "gw": "10.1.200.252"}, { "dst": "104.18.9.132/32", "gw": "10.1.200.252"}, { "dst": "89.238.73.97/32", "gw": "10.1.200.252"}, { "dst": "1.1.1.1/32", "gw": "10.1.200.252"} ], "rangeStart": "10.1.200.20", "rangeEnd": "10.1.200.251", "gateway": "10.1.200.1" } }
```