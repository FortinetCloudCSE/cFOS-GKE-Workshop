---
title: "Task 2 - Validate Multus CNI"
menuTitle: "Validate Multus CNI installation"
weight: 2
---

### Validate Multus CNI installation

1. Validate VPC Network

    ```
    kubectl rollout status ds/kube-multus-ds -n kube-system
    ```

    ```
    kubectl logs ds/kube-multus-ds -c kube-multus -n kube-system
    ```

    > output will be similar as below

    ```
    daemon set "kube-multus-ds" successfully rolled out
    2023-05-18T07:41:46+00:00 Generating Multus configuration file using files in /host/etc/cni/net.d...
    2023-05-18T07:41:47+00:00 Using MASTER_PLUGIN: 10-containerd-net.conflist
    2023-05-18T07:41:48+00:00 Nested capabilities string: "capabilities": {"portMappings": true},
    2023-05-18T07:41:48+00:00 Using /host/etc/cni/net.d/10-containerd-net.conflist as a source to generate the Multus configuration
    2023-05-18T07:41:48+00:00 Config file created @ /host/etc/cni/net.d/00-multus.conf
    { "cniVersion": "0.3.1", "name": "multus-cni-network", "type": "multus", "capabilities": {"portMappings": true}, "kubeconfig": "/etc/cni/net.d/multus.d/multus.kubeconfig", "delegates": [ { "name": "k8s-pod-network", "cniVersion": "0.3.1", "plugins": [ { "type": "ptp", "mtu": 1460, "ipam": { "type": "host-local", "subnet": "10.140.0.0/24", "routes": [ { "dst": "0.0.0.0/0" } ] } }, { "type": "portmap", "capabilities": { "portMappings": true } } ] } ] }
    2023-05-18T07:41:48+00:00 Entering sleep (success)...
    ```
