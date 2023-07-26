---
title: "Task 7 - Modify Worker Node default CNI"
menuTitle: "Modify Worker Node default CNI"
weight: 7
---

### Modify Worker Node default CNI Config

in previous section, we did not touch application POD's default route, only we interested destination like 1.1.1.1 is send to cFOS, the rest of traffic will contine go to internet via default route, what about if want send all traffic from application POD to cFOS ,to doing this,
we will need then insert a default route into application pod, for this purpose, we will need use add annotation with keyword default-route to the POD definition. but this is not enough. as you still want some other traffic continue to go to default interface instead goes to cFOS, for example, the traffic goes to gke cluster IP and cross POD to POD traffic. the GKE default cni come with host-local ipam, inside host-local ipam , we can insert custom route, we added clusterIP CIDR range and POD IP CIDR range, after that, restart multus DaemonSet to update Multus default config.

> Below command to modify default GKE cni config to insert route

```
#!/bin/bash 
clustersearchstring=$(gcloud container clusters list --format="value(name)" --limit=1) && \
namelist=$(gcloud compute instances list --filter="name~'$clustersearchstring'"  --format="value(name)" ) && \

source ./variable.sh

[[ $services_ipv4_cidr == "" ]] && services_ipv4_cidr="10.144.0.0/20"
[[ $cluster_ipv4_cidr == "" ]] && cluster_ipv4_cidr="10.140.0.0/14"

services_ip=${services_ipv4_cidr%%/*}
services_netmask=${services_ipv4_cidr#*/}
echo $services_ip
echo $services_netmask

pod_ip=${cluster_ipv4_cidr%%/*}
pod_netmask=${cluster_ipv4_cidr#*/}

for name in $namelist ; do {

route_exists=$(gcloud compute ssh $name --command="sudo grep -E '\"dst\": \"$services_ip\\/$services_netmask\"|\"dst\": \"$pod_ip\\/$pod_netmask\"' /etc/cni/net.d/10-containerd-net.conflist")

#route_exists=$(gcloud compute ssh $name --command="sudo grep -E '\"dst\": \"10.144.0.0\\/20\"|\"dst\": \"10.140.0.0\\/14\"' /etc/cni/net.d/10-containerd-net.conflist")

if [ -z "$route_exists" ]; then
  gcloud compute ssh $name --command="sudo sed -i '/\"dst\": \"0.0.0.0\\/0\"/!b;n;N;s/        \\]$/,\n          {\"dst\": \"$services_ip\\/$services_netmask\"},\n          {\"dst\": \"$pod_ip\\/$pod_netmask\"}\n        ]/' /etc/cni/net.d/10-containerd-net.conflist"
kubectl rollout restart ds/kube-multus-ds -n kube-system && 
kubectl rollout status ds/kube-multus-ds -n kube-system 
kubectl logs  ds/kube-multus-ds -n kube-system
fi

kubectl logs  ds/kube-multus-ds -n kube-system
}
done
```

### Validate the result

```
kubectl logs ds/kube-multus-ds -n kube-system
```

> output will be similar as below

```
2023-05-18T07:46:34+00:00 Generating Multus configuration file using files in /host/etc/cni/net.d...
2023-05-18T07:46:34+00:00 Using MASTER_PLUGIN: 10-containerd-net.conflist
2023-05-18T07:46:35+00:00 Nested capabilities string: "capabilities": {"portMappings": true},
2023-05-18T07:46:35+00:00 Using /host/etc/cni/net.d/10-containerd-net.conflist as a source to generate the Multus configuration
2023-05-18T07:46:36+00:00 Config file created @ /host/etc/cni/net.d/00-multus.conf
{ "cniVersion": "0.3.1", "name": "multus-cni-network", "type": "multus", "capabilities": {"portMappings": true}, "kubeconfig": "/etc/cni/net.d/multus.d/multus.kubeconfig", "delegates": [ { "name": "k8s-pod-network", "cniVersion": "0.3.1", "plugins": [ { "type": "ptp", "mtu": 1460, "ipam": { "type": "host-local", "subnet": "10.140.1.0/24", "routes": [ { "dst": "0.0.0.0/0" } , {"dst": "10.144.0.0/20"}, {"dst": "10.140.0.0/14"} ] } }, { "type": "portmap", "capabilities": { "portMappings": true } } ] } ] }
2023-05-18T07:46:36+00:00 Entering sleep (success)...
```