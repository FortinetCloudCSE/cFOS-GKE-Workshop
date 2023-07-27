---
title: "Task 2 - Validate deployment"
menuTitle: "Validate Demo Application deployment"
weight: 2
---

### Validate Demo Application deployment

1. Validate Demo Application deployment

    ```
    kubectl rollout status deployment multitool01-deployment
    ```

    > output will be similar as below

    ```
    deployment "multitool01-deployment" successfully rolled out
    ```

    ```
    kubectl get pod -l app=multitool01
    ```

    > output will be similar as below

    ```
    NAME                                      READY   STATUS    RESTARTS   AGE
    multitool01-deployment-7f5bf4b7cd-46vv4   1/1     Running   0          8s
    multitool01-deployment-7f5bf4b7cd-5v2fw   1/1     Running   0          8s
    multitool01-deployment-7f5bf4b7cd-ds9t8   1/1     Running   0          8s
    multitool01-deployment-7f5bf4b7cd-j5hcr   1/1     Running   0          8s
    ```

    ```
    nodeName=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}') && for node in $nodeName; do podName=$(kubectl get pods -l app=multitool01 --field-selector spec.nodeName="$node" -o jsonpath='{.items[*].metadata.name}') ; kubectl exec -it po/$podName -- ip route && kubectl exec -t po/$podName -- ip address ; done
    ```

    > output will be similar as below

    ```
    default via 10.140.1.1 dev eth0 
    1.1.1.1 via 10.1.200.252 dev net1 
    10.1.200.0/24 dev net1 proto kernel scope link src 10.1.200.21 
    10.140.1.0/24 via 10.140.1.1 dev eth0 src 10.140.1.10 
    10.140.1.1 dev eth0 scope link src 10.140.1.10 
    89.238.73.97 via 10.1.200.252 dev net1 
    104.18.8.132 via 10.1.200.252 dev net1 
    104.18.9.132 via 10.1.200.252 dev net1 
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
    2: eth0@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
        link/ether de:98:13:86:d0:23 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 10.140.1.10/24 brd 10.140.1.255 scope global eth0
        valid_lft forever preferred_lft forever
    3: net1@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
        link/ether 66:ed:7c:24:ab:9d brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 10.1.200.21/24 brd 10.1.200.255 scope global net1
        valid_lft forever preferred_lft forever
    default via 10.140.0.1 dev eth0 
    1.1.1.1 via 10.1.200.252 dev net1 
    10.1.200.0/24 dev net1 proto kernel scope link src 10.1.200.21 
    10.140.0.0/24 via 10.140.0.1 dev eth0 src 10.140.0.5 
    10.140.0.1 dev eth0 scope link src 10.140.0.5 
    89.238.73.97 via 10.1.200.252 dev net1 
    104.18.8.132 via 10.1.200.252 dev net1 
    104.18.9.132 via 10.1.200.252 dev net1 
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
    2: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
        link/ether 42:11:fd:13:a1:71 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 10.140.0.5/24 brd 10.140.0.255 scope global eth0
        valid_lft forever preferred_lft forever
    3: net1@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc noqueue state UP group default 
        link/ether 0e:6b:ce:0c:d6:37 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 10.1.200.21/24 brd 10.1.200.255 scope global net1
        valid_lft forever preferred_lft forever
    ```
