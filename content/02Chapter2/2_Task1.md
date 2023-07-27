---
title: "Task 1 - Create VPC Network for GKE Cluster"
menuTitle: "Create VPC Network"
weight: 1
---

### Create VPC Network for GKE Cluster

Create VPC Network for GKE VM instances.

- *ipcidrRange* is the ip range for VM node.

- *firewallallowProtocol=all* allow ssh into worker node from anywhere  to *all* protocols

> Below command will Create VPC Networks, Subnets and Firewall-rules

```
#!/bin/bash -xe
echo $networkName

[[ $networkName == "" ]] && networkName="gkenetwork"
[[ $subnetName == "" ]] && subnetName="gkenode"
[[ $ipcidrRange == "" ]] && ipcidrRange="10.0.0.0/24"
[[ $firewallruleName == "" ]] && firewallruleName="$networkName-allow-custom"
[[ $firewallallowProtocol == "" ]] && firewallallowProtocol="all"

echo $networkName
gcloud compute networks create $networkName --subnet-mode custom --bgp-routing-mode  regional
gcloud compute networks subnets create $subnetName --network=$networkName --range=$ipcidrRange &&  \
gcloud compute firewall-rules create $firewallruleName --network $networkName --allow $firewallallowProtocol --direction ingress --priority  100
```
  