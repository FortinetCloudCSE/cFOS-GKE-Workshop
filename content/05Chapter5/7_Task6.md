---
title: "Task 6 - Create configmap for cFOS to get firewall policy configuration"
menuTitle: "Create configmap for cFOS to get firewall policy configuration"
weight: 6
---

### Create configmap for cFOS to get firewall policy configuration

cFOS can be configured use cFOS shell, kubernetes configmap and restApi. here we use configmap to config cFOS
there is an issue in this version, the configuration applied via configmap will not take effect until you restart cFOS DS.
the firewall policy has policy id set to 300 and source address set to any. once configmap created, cFOS will read the configmap and apply the policy. you can chech the log on cFOS to verify this.
delete configmap will not delete the policy on cFOS. you can also edit the policy in configmap use *kubectl edit cm foscfgfirewallpolicy* to update the policy.

> Below command will Create configmap that include firewall policy configuration

```
policy_id="$configmap_policy_id"
[[ -z $cfos_label ]] && cfos_label="fos"
[[ -z $configmap_policy_id ]] && configmap_policy_id="300"
file="configmapfirewallpolicy.yml"
cat << EOF > $file
apiVersion: v1
kind: ConfigMap
metadata:
  name: foscfgfirewallpolicy
  labels:
      app: $cfos_label
      category: config
data:
  type: partial
  config: |-
    config firewall policy
           edit "$configmap_policy_id"
               set utm-status enable
               set name "pod_to_internet_HTTPS_HTTP"
               set srcintf any
               set dstintf eth0
               set srcaddr all
               set dstaddr all
               set service HTTPS HTTP PING DNS
               set ssl-ssh-profile "deep-inspection"
               set ips-sensor "default"
               set webfilter-profile "default"
               set av-profile "default"
               set nat enable
               set logtraffic all
           next
       end
EOF
kubectl create -f $file
```

### Validate configmap for cFOS

```
kubectl get configmap foscfgfirewallpolicy -o yaml
```

> output will be similar as below

```
apiVersion: v1
data:
  config: |-
    config firewall policy
           edit "300"
               set utm-status enable
               set name "pod_to_internet_HTTPS_HTTP"
               set srcintf any
               set dstintf eth0
               set srcaddr all
               set dstaddr all
               set service HTTPS HTTP PING DNS
               set ssl-ssh-profile "deep-inspection"
               set ips-sensor "default"
               set webfilter-profile "default"
               set av-profile "default"
               set nat enable
               set logtraffic all
           next
       end
  type: partial
kind: ConfigMap
metadata:
  creationTimestamp: "2023-05-18T07:42:18Z"
  labels:
    app: fos
    category: config
  name: foscfgfirewallpolicy
  namespace: default
  resourceVersion: "3124"
  uid: d0466e8c-b4db-4abb-bf7d-52925b03b43e
```