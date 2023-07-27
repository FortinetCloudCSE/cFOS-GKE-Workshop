---
title: "Task 2 - Validate VPC Network"
menuTitle: "Validate VPC Network"
weight: 2
---

### Validate VPC Network, Subnets and Firewall-rules

1. Validate VPC Network

    ```
    gcloud compute networks list --format json --filter gke
    ```

    > output will be similar as below

    ```
    [
        {
            "autoCreateSubnetworks": false,
            "creationTimestamp": "2023-05-18T00:32:47.108-07:00",
            "id": "7011832803059329648",
            "kind": "compute#network",
            "name": "gkenetwork1",
            "networkFirewallPolicyEnforcementOrder": "AFTER_CLASSIC_FIREWALL",
            "routingConfig": {
            "routingMode": "REGIONAL"
            },
            "selfLink": "https://www.googleapis.com/compute/v1/projects/cfos-384323/global/networks/gkenetwork1",
            "selfLinkWithId": "https://www.googleapis.com/compute/v1/projects/cfos-384323/global/networks/7011832803059329648",
            "subnetworks": [
            "https://www.googleapis.com/compute/v1/projects/cfos-384323/regions/asia-east1/subnetworks/gkenode"
            ],
            "x_gcloud_bgp_routing_mode": "REGIONAL",
            "x_gcloud_subnet_mode": "CUSTOM"
        }
    ]
    ```

2. Validate Subnets

    ```
    gcloud compute networks subnets list --format json --filter gke
    ```

    > output will be similar as below

    ```
    [
        {
            "creationTimestamp": "2023-05-18T00:33:02.987-07:00",
            "fingerprint": "UJPhD-8NtFU=",
            "gatewayAddress": "10.0.0.1",
            "id": "973775312268061249",
            "ipCidrRange": "10.0.0.0/24",
            "kind": "compute#subnetwork",
            "name": "gkenode",
            "network": "https://www.googleapis.com/compute/v1/projects/cfos-384323/global/networks/gkenetwork1",
            "privateIpGoogleAccess": false,
            "privateIpv6GoogleAccess": "DISABLE_GOOGLE_ACCESS",
            "purpose": "PRIVATE",
            "region": "https://www.googleapis.com/compute/v1/projects/cfos-384323/regions/asia-east1",
            "selfLink": "https://www.googleapis.com/compute/v1/projects/cfos-384323/regions/asia-east1/subnetworks/gkenode",
            "stackType": "IPV4_ONLY"
        }
    ]    
    ```

3. Validate Firewall-rules

    ```
    gcloud compute firewall-rules list --format json --filter gke
    ```

    > output will be similar as below

    ```
    [
        {
            "allowed": [
            {
                "IPProtocol": "all"
            }
            ],
            "creationTimestamp": "2023-05-18T00:33:22.123-07:00",
            "description": "",
            "direction": "INGRESS",
            "disabled": false,
            "id": "7153549930505202221",
            "kind": "compute#firewall",
            "logConfig": {
            "enable": false
            },
            "name": "gkenetwork1-allow-custom",
            "network": "https://www.googleapis.com/compute/v1/projects/cfos-384323/global/networks/gkenetwork1",
            "priority": 100,
            "selfLink": "https://www.googleapis.com/compute/v1/projects/cfos-384323/global/firewalls/gkenetwork1-allow-custom",
            "sourceRanges": [
            "0.0.0.0/0"
            ]
        }
    ]    
    ```