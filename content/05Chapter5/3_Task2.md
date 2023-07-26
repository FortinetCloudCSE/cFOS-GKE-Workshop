---
title: "Task 2 - Validate Role and Service Account for cFOS"
menuTitle: "Validate Role and Service Account for cFOS"
weight: 2
---

### Validate Role and Service Account for cFOS

Validate Role and Service Account for cFOS

    ```
    kubectl get rolebinding read-configmaps && kubectl get rolebinding read-secrets -o yaml
    ```

    > output will be similar as below

    ```
    NAME              ROLE                           AGE
    read-configmaps   ClusterRole/configmap-reader   0s
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
        creationTimestamp: "2023-05-18T07:42:04Z"
        name: read-secrets
        namespace: default
        resourceVersion: "2972"
        uid: b70f038e-b9ec-4440-bebe-5ea0ed06f7c8
    roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: ClusterRole
        name: secrets-reader
    subjects:
    - kind: ServiceAccount
        name: default    
  ```
