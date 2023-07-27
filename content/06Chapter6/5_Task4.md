---
title: "Task 4 - Create an POD to update POD source IP to cFOS"
menuTitle: "Create an POD to update POD source IP to cFOS"
weight: 4
---

# Create an POD to update POD source IP to cFOS

POD IPs are keep changing due to scale in/out or reborn , deleting etc for various reason, we need to keep update the POD ip address to cFOS address group. 
we create a POD dedicated for this. this POD keep running a background proces which update the application POD's IP  that has annoation to net-attach-def *cfosapp"* to cFOS via cFOS restful API. 
the API call to cFOS can use either cFOS dns name or cFOS node IPs. if cFOS use shared storage for configuration, then use dns name is proper way, otherwise, we will need to update each cFOS POD directly via CFOS POD ip address. the policy_manager by default using cFOS POD ip address. 
the policy_manager also create  firewallpolicy for target application unless the policy has already createdby gatekeeper. this is only for demo purpose.  the firewall policy created on cFOS has fixed policyID=200
the policy_manager pod use image from *interbeing/kubectl-cfos:gke_demo_v1*
the source code of this image is under policymanager/
build.sh  Dockerfile  script.sh
you can build by yourself.

> Below command will create policy_manager

```
#!/bin/bash

filename="18_cfospolicymanager.yml"
[[ -z $policymanagerimage ]] && policymanagerimage="interbeing/kubectl-cfos:gke_demo_v2"
[[ -z $app_nad_annotation ]] && app_nad_annotation="cfosapp"
[[ -z $cfos_label ]] && cfos_label="fos"
function wait_for_pod_ready {
pod_name=$(kubectl get pods -l app=policy_manager -o jsonpath='{.items[0].metadata.name}')

while true; do
    pod_status=$(kubectl get pods $pod_name -o jsonpath='{.status.phase}')
    if [[ $pod_status == "Running" ]]; then
	kubectl get pod -l app=policy_manager
        break
    else
        echo "Waiting for pod to be in Running state..."
        sleep 5
    fi
done

}

cat << EOF > $filename
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: pod-reader

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "watch"]
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["list","get","watch","create"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list", "get", "watch"]
- apiGroups: ["apps"]
  resources: ["daemonsets"]
  verbs: ["get", "list", "watch", "patch", "update"]
- apiGroups: ["constraints.gatekeeper.sh"]
  resources: ["k8segressnetworkpolicytocfosutmpolicy"]
  verbs: ["list","get","watch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: pod-reader
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: pod-reader
subjects:
- kind: ServiceAccount
  name: pod-reader
  namespace: default

---
apiVersion: v1
kind: Pod
metadata:
  name: policymanager
  labels: 
    app: policy_manager
spec:
  serviceAccountName: pod-reader
  containers:
  - name: kubectl-container
    image: $policymanagerimage
    env:
      - name: app_label
        value: $app_nad_annotation

EOF

kubectl apply -f $filename  && wait_for_pod_ready && kubectl exec -it po/policymanager -- curl -X GET "http://$cfos_label-deployment.default.svc.cluster.local/api/v2/cmdb/firewall/policy" && kubectl exec -it po/policymanager -- curl -X GET "http://$cfos_label-deployment.default.svc.cluster.local/api/v2/cmdb/firewall/addrgrp"
```

### Validate the result

```
kubectl get pod policymanager && kubectl exec -it po/policymanager -- curl -X GET "http://fos-deployment.default.svc.cluster.local/api/v2/cmdb/firewall/addrgrp"
```

> output will be similar as below

```
NAME            READY   STATUS    RESTARTS   AGE
policymanager   1/1     Running   0          12s
{
  "status": "success",
  "http_status": 200,
  "path": "firewall",
  "name": "addrgrp",
  "http_method": "GET",
  "results": [
    {
      "name": "defaultappmultitool",
      "type": "default",
      "category": "default",
      "member": [
        {
          "name": "10.1.200.21"
        },
        {
          "name": "10.1.200.20"
        },
        {
          "name": "10.1.200.21"
        },
        {
          "name": "10.1.200.20"
        }
      ],
      "comment": "",
      "exclude": "disable",
      "exclude-member": [
        {
          "name": "none"
        }
      ]
    }
  ],
  "serial": "FGVMULTM23000044",
  "version": "v7.2.0",
  "build": "231"
}
```


