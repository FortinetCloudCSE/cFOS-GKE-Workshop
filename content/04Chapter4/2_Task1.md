---
title: "Task 1 - Create Demo Application deployment"
menuTitle: "Create Demo Application deployment"
weight: 1
---

### Create Demo Application deployment

we use annotation *k8s.v1.cni.cncf.io/networks: '[ { "name": "cfosapp" } ]'* to config to POD for secondary interface and custom route entry.
we did not touch pod default route, instead we only insert custom route that we are interested. so for destination, the next hop will be cFOS. cFOS will inspect traffic for those traffic.
when POD attach to *cfosapp*, it will obtain *{ "dst": "104.18.8.132/32", "gw": "10.1.200.252"},, { "dst": "104.18.9.132/32", "gw": "10.1.200.252"},, { "dst": "1.1.1.1/32", "gw": "10.1.200.252"}*  route point to cFOS for inspection in this demo. 

> Below command will create application deployment

```
file="app_with_annotations_cfosapp.yml"
[[ $app_image == "" ]] && app_image="praqma/network-multitool"
[[ -z $app_deployment_label ]] && app_deployment_label="multitool01"

annotations="k8s.v1.cni.cncf.io/networks: '[ { \"name\": \"$app_nad_annotation\" } ]'"
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
```
  