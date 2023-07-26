---
title: "Task 9 - Create application deployment"
menuTitle: "Create application deployment"
weight: 9
---

### Create application deployment

create deployment with annotation to use net-attach-def and also config default route point to net-attach-def attached interface. which is cFOS interface. 
the annotation field has context 
*k8s.v1.cni.cncf.io/networks: '[ { "name": "cfosapp",  "default-route": ["10.1.200.252"]  } ]'* , which config an default route with nexthop to 10.1.200.252.
check ip route table on application shall see the default route point to cFOS interface.

> Below command to create deployment

```
file="app_with_annotations_cfosapp_with_defalt_route.yml"
[[ $cfosIpshort == "" ]] && cfosIpshort="10.1.200.252" 
annotations="k8s.v1.cni.cncf.io/networks: '[ { \"name\": \"$app_nad_annotation\", \"default-route\": [\"$cfosIpshort\"] } ]'"
[[ $app_image == "" ]] && app_image="praqma/network-multitool"
[[ -z $app_deployment_label ]] && app_deployment_label="multitool01"

cat << EOF > $file 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: $app_deployment_label-deployment
  labels:
      app: $app_deployment_label
spec:
  replicas: 4
  selector:
    matchLabels:
        app: $app_deployment_label
  template:
    metadata:
      labels:
        app: $app_deployment_label
      annotations:
        $annotations
    spec:
      containers:
        - name: $app_deployment_label
          image: $app_image
          #image: praqma/network-multitool
          imagePullPolicy: Always
            #command: ["/bin/sh","-c"]
          args:
            - /bin/sh
            - -c
            - /usr/sbin/nginx -g "daemon off;"
          securityContext:
            privileged: true
EOF

kubectl create -f $file && kubectl rollout status deployment $app_deployment_label-deployment
echo "sleep 30 seconds for it will take some time to trigger policymanager to update cfos addressgrp"
sleep 30
```

### Validate the result

```
nodeName=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}') && for node in $nodeName; do podName=$(kubectl get pods -l app=multitool01 --field-selector spec.nodeName="$node" -o jsonpath='{.items[*].metadata.name}') ; kubectl exec -it po/$podName -- ip route && kubectl exec -t po/$podName -- ip address ; done
```

> output will be similar as below

```
default via 10.1.200.252 dev net1 
1.1.1.1 via 10.1.200.252 dev net1 
10.1.200.0/24 dev net1 proto kernel scope link src 10.1.200.23 
10.140.0.0/14 via 10.140.1.1 dev eth0 
10.140.1.0/24 via 10.140.1.1 dev eth0 src 10.140.1.13 
10.140.1.1 dev eth0 scope link src 10.140.1.13 
10.144.0.0/20 via 10.140.1.1 dev eth0 
89.238.73.97 via 10.1.200.252 dev net1 
104.18.8.132 via 10.1.200.252 dev net1 
104.18.9.132 via 10.1.200.252 dev net1 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0@if15: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
    link/ether 02:cb:d4:c8:66:8e brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.140.1.13/24 brd 10.140.1.255 scope global eth0
       valid_lft forever preferred_lft forever
3: net1@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
    link/ether fe:40:31:88:6f:cc brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.200.23/24 brd 10.1.200.255 scope global net1
       valid_lft forever preferred_lft forever
default via 10.1.200.252 dev net1 
1.1.1.1 via 10.1.200.252 dev net1 
10.1.200.0/24 dev net1 proto kernel scope link src 10.1.200.22 
10.140.0.0/24 via 10.140.0.1 dev eth0 src 10.140.0.10 
10.140.0.0/14 via 10.140.0.1 dev eth0 
10.140.0.1 dev eth0 scope link src 10.140.0.10 
10.144.0.0/20 via 10.140.0.1 dev eth0 
89.238.73.97 via 10.1.200.252 dev net1 
104.18.8.132 via 10.1.200.252 dev net1 
104.18.9.132 via 10.1.200.252 dev net1 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
    link/ether 1a:4d:45:d6:0d:62 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.140.0.10/24 brd 10.140.0.255 scope global eth0
       valid_lft forever preferred_lft forever
3: net1@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
    link/ether a2:64:89:4d:75:2e brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.200.22/24 brd 10.1.200.255 scope global net1
       valid_lft forever preferred_lft forever
```