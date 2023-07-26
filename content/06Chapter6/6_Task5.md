---
title: "Task 5 - Perform IPS test again on a target website"
menuTitle: "Perform IPS test again on a target website"
weight: 5
---

### Perform IPS test again on a target website

we do ips test again, this time, the policy created by policymanager will take the action. we can chech the ips log to prove it. the traffic shall match different policy ID which is 101


> Below command will send malicous traffic from application pod

```
#!/bin/bash
[[ $ips_target_url == "" ]] && ips_target_url="www.hackthebox.eu"
[[ -z $cfos_label ]] && cfos_label="fos"
policy_id="101"
echo -e 'generate traffic to $ips_target_url' 
kubectl get pod | grep multi | grep -v termin | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- dig $ips_target_url ; done && \
kubectl get pod | grep multi | grep -v termin | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- ping -c 2  $ips_target_url ; done && \
kubectl get pod | grep multi | grep -v termin | awk '{print $1}'  | while read line; do kubectl exec -t po/$line --  curl --max-time 5  -k -H "User-Agent: () { :; }; /bin/ls" https://$ips_target_url ; done
kubectl get pod | grep $cfos_label | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- tail  /data/var/log/log/ips.0 | grep policyid=$policy_id ; done
```

### Validate the result

```
kubectl get pod | grep fos | awk '{print $1}'  | while read line; do kubectl exec -t po/$line -- tail  /data/var/log/log/ips.0 | grep policyid=101 ; done
```

> output will be similar as below

```
date=2023-05-18 time=07:45:17 eventtime=1684395917 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.21 dstip=104.18.8.132 srcintf="net1" dstintf="eth0" sessionid=15 action="dropped" proto=6 service="HTTPS" policyid=101 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=35192 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=30408707 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
date=2023-05-18 time=07:45:22 eventtime=1684395922 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.20 dstip=104.18.8.132 srcintf="net1" dstintf="eth0" sessionid=17 action="dropped" proto=6 service="HTTPS" policyid=101 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=33312 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=30408708 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
date=2023-05-18 time=07:45:06 eventtime=1684395906 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.21 dstip=104.18.8.132 srcintf="net1" dstintf="eth0" sessionid=18 action="dropped" proto=6 service="HTTPS" policyid=101 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=48880 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=224395267 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
date=2023-05-18 time=07:45:11 eventtime=1684395911 tz="+0000" logid="0419016384" type="utm" subtype="ips" eventtype="signature" level="alert" severity="critical" srcip=10.1.200.20 dstip=104.18.9.132 srcintf="net1" dstintf="eth0" sessionid=20 action="dropped" proto=6 service="HTTPS" policyid=101 attack="Bash.Function.Definitions.Remote.Code.Execution" srcport=50006 dstport=443 hostname="www.hackthebox.eu" url="/" direction="outgoing" attackid=39294 profile="default" incidentserialno=224395268 msg="applications3: Bash.Function.Definitions.Remote.Code.Execution"
```