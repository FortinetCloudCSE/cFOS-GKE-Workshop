---
title: "Task 3 - Create net-attach-def for cFOS"
menuTitle: "Create net-attach-def for cFOS"
weight: 3
---

### Create net-attach-def for cFOS

We will create *net-attach-def* with mac-vlan CNI, Multus CNI will use this *net-attach-def* to create  network and attach POD to the network.  
We use host-local as IPAM CNI. The *net-attach-def* is for cfos to attach.  
The cni config of macvlan use bridge mode and associated with ens4 interface on Worker Node.  
If the master interface on Worker Node is other than ens4, we need to change that to match the actual one on the Host Node.   
One can ssh into Worker Node to check Master interface name.  
The *net-attach-def* has name **cfosdefaultcni5**.

> Below command will create net-attach-def

```
#!/bin/bash -xe
[[ $master_interface_on_worker_node == "" ]] && master_interface_on_worker_node="ens4"
[[ $net_attach_def_name_for_cfos == "" ]]    &&  net_attach_def_name_for_cfos="cfosdefaultcni5"
[[ $cfosIpshort == "" ]] && cfosIpshort="10.1.200.252" 
filename="04_nad_macvlan_cfos.yml"
cat << EOF > $filename
apiVersion: "k8s.cni.cncf.io/v1"
kind: NetworkAttachmentDefinition
metadata:
  name: $net_attach_def_name_for_cfos
spec:
  config: '{
      "cniVersion": "0.3.1",
      "type": "macvlan",
      "master": "$master_interface_on_worker_node",
      "mode": "bridge",
      "ipam": {
        "type": "host-local",
        "subnet": "${cfosIpshort%.*}.0/24",
        "rangeStart": "${cfosIpshort%.*}.251",
        "rangeEnd": "${cfosIpshort%.*}.253",
        "gateway": "${cfosIpshort%.*}.1"
      }
    }'
EOF
kubectl create -f $filename && kubectl rollout status ds/kube-multus-ds -n kube-system  && echo "done"
kubectl get net-attach-def cfosdefaultcni5 -o yaml
```
