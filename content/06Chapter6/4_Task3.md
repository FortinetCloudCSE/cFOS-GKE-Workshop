---
title: "Task 3 - Delete firewall policy using cFOS API"
menuTitle: "Delete firewall policy using cFOS API"
weight: 3
---

### Delete firewall policy using cFOS Rest API

we can use cFOS shell to change firewall policy, we can also use cFOS restAPI to do the same. 
after delete firewall policy, ping to 1.1.1.1 from application pod will no longer reachable

> Below command will delete firewall policy 

```
[[ -z $cfos_label ]] && cfos_label="fos"
[[ -z $configmap_policy_id ]] && configmap_policy_id="300"
nodeList=$(kubectl get pod -l app=$cfos_label -o jsonpath='{.items[*].status.podIP}')
kubectl delete cm foscfgfirewallpolicy
echo $nodeList
for i in $nodeList; do {
kubectl exec -it po/policymanager -- curl -X DELETE "$i/api/v2/cmdb/firewall/policy/$configmap_policy_id"
}
done
```

### Validate the result

```
kubectl get pod | grep multi | grep -v termin  | awk '{print $1}'  | while read line; do echo -e pod $line; kubectl exec -t po/$line -- ping -c1 1.1.1.1 ; done
```

> output will be similar as below

```
pod multitool01-deployment-7f5bf4b7cd-46vv4
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=60 time=4.64 ms

--- 1.1.1.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 4.640/4.640/4.640/0.000 ms
pod multitool01-deployment-7f5bf4b7cd-5v2fw
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=60 time=3.80 ms

--- 1.1.1.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 3.799/3.799/3.799/0.000 ms
pod multitool01-deployment-7f5bf4b7cd-ds9t8
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=60 time=5.01 ms

--- 1.1.1.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 5.006/5.006/5.006/0.000 ms
pod multitool01-deployment-7f5bf4b7cd-j5hcr
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=60 time=4.15 ms

--- 1.1.1.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 4.153/4.153/4.153/0.000 ms
```
