---
title: "Task 1 - Perform IPS test on a target website"
menuTitle: "Perform IPS test on a target website"
weight: 1
---

### Perform initial IPS test on a target website

it is very common that a malicous POD can geneate some malicous traffic targeting external network or VM or physical machine in custmer network. those traffic are often encrypted , when these traffic reach cFOS, cFOS can decrpyt the traffic and look into the IPS signature. if match the signature. cFOS can either block it or pass it with alert depends on the policy configured.

we will generate some malicous traffic from application POD targeting a testing website. cFOS will block the traffic and log it. 
you will exepct to see ips traffic log with matched firewall policy id to indicate which policy is in action.

> Below command will send malicous traffic from application pod

```
#!/bin/bash
[[ $ips_target_url == "" ]] && ips_target_url="www.hackthebox.eu"
[[ -z $configmap_policy_id ]] && configmap_policy_id="300"
[[ -z $cfos_label ]] && cfos_label="fos"
echo -e 'generate traffic to $ips_target_url' 
kubectl get pod | grep multi | grep -v termin | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- dig $ips_target_url ; done && \
kubectl get pod | grep multi | grep -v termin | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- ping -c 2  $ips_target_url ; done && \
kubectl get pod | grep multi | grep -v termin | awk '{print $1}'  | while read line; do kubectl exec -t po/$line --  curl --max-time 5  -k -H "User-Agent: () { :; }; /bin/ls" https://$ips_target_url ; done
kubectl get pod | grep $cfos_label | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- tail  /data/var/log/log/ips.0 | grep $configmap_policy_id ; done
```

### Validate the result

```
kubectl get pod | grep fos | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- tail  /data/var/log/log/ips.0 | grep 300  ; done
```

> output will be similar as below

```
date=2023-05-18 time=07:44:24 eventtime=1684395864 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.21 dstip=104.18.9.132 srcintf="net1" dstintf="eth0" sessionid=6 action="dropped" proto=6 service="HTTPS" policyid=300 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=45868 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=30408705 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
date=2023-05-18 time=07:44:29 eventtime=1684395869 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.20 dstip=104.18.8.132 srcintf="net1" dstintf="eth0" sessionid=6 action="dropped" proto=6 service="HTTPS" policyid=300 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=50376 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=30408706 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
date=2023-05-18 time=07:44:13 eventtime=1684395853 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.21 dstip=104.18.9.132 srcintf="net1" dstintf="eth0" sessionid=9 action="dropped" proto=6 service="HTTPS" policyid=300 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=45844 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=224395265 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
date=2023-05-18 time=07:44:18 eventtime=1684395858 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.20 dstip=104.18.8.132 srcintf="net1" dstintf="eth0" sessionid=3 action="dropped" proto=6 service="HTTPS" policyid=300 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=44936 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=224395266 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
```
