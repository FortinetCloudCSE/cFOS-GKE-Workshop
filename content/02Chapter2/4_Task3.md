---
title: "Task 3 - Create GKE Cluster"
menuTitle: "Create GKE Cluster"
weight: 3
---

### Create GKE Cluster

> Below configuration attributes need to be set

   - *enable-ip-alias* to enable use alias ip on VM for pod ip address

   - *service-ipv4-cidr* is the cidr for clusterVIP address

   - *cluster-ipv4-cidr* is for POD ip address scope

   - *kubectl get node -o wide* shall show the node in ready state. 

> Below command will Create GKE Cluster 

    ```
      #!/bin/bash -xe
      [[ $defaultClustername == "" ]] && defaultClustername="my-first-cluster-1"
      [[ $networkName == "" ]] && networkName="gkenetwork"
      [[ $subnetName == "" ]] && subnetName="gkenode"
      [[ $machineType == "" ]] && machineType="g1-small"
      [[ $num_nodes == "" ]] && num_nodes="1"
      [[ $services_ipv4_cidr == "" ]] && services_ipv4_cidr="10.144.0.0/20"
      [[ $cluster_ipv4_cidr == "" ]] && cluster_ipv4_cidr="10.140.0.0/14"


      gkeClusterName=$defaultClustername
      machineType=$machineType
      gkeNetworkName=$(gcloud compute networks list --format="value(name)" --filter="name="$networkName""  --limit=1)
      gkeSubnetworkName=$(gcloud compute networks subnets  list --format="value(name)" --filter="name="$subnetName"" --limit=1)

      projectName=$(gcloud config list --format="value(core.project)") && \
      region=$(gcloud compute networks subnets list --format="value(region)" --limit=1) && \

      gcloud services enable container.googleapis.com  && \

      gcloud container clusters create $gkeClusterName  \
         --no-enable-basic-auth \
         --cluster-version "1.26.3-gke.1000" \
         --release-channel "rapid" \
         --machine-type $machineType \
         --image-type "UBUNTU_CONTAINERD" \
         --disk-type "pd-balanced" \
         --disk-size "32" \
         --metadata disable-legacy-endpoints=true \
         --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
         --max-pods-per-node "110" \
         --num-nodes $num_nodes \
         --enable-ip-alias \
         --network "projects/$projectName/global/networks/$gkeNetworkName" \
         --subnetwork "projects/$projectName/regions/$region/subnetworks/$gkeSubnetworkName" \
               --no-enable-intra-node-visibility \
         --default-max-pods-per-node "110" \
         --no-enable-master-authorized-networks \
         --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver \
         --enable-autoupgrade \
         --enable-autorepair \
               --max-surge-upgrade 1 \
         --max-unavailable-upgrade 0 \
         --enable-shielded-nodes \
         --services-ipv4-cidr $services_ipv4_cidr \
            --cluster-ipv4-cidr  $cluster_ipv4_cidr

      echo done
      echo cluster has podIpv4CidrBlock $(gcloud container clusters describe $gkeClusterName --format="value(nodePools.networkConfig.podIpv4CidrBlock)")
      echo cluster has servicesIpv4Cidr $(gcloud container clusters describe $gkeClusterName --format="value(servicesIpv4Cidr)")


      clustersearchstring=$(gcloud container clusters list --format="value(name)" --limit=1)
      name=$(gcloud compute instances list --filter="name~'$clustersearchstring'"  --format="value(name)" --limit=1)
      echo cluster worker node vm has internal ip $(gcloud compute instances describe $name --format="value(networkInterfaces.aliasIpRanges)" --format="value(networkInterfaces.networkIP)")
      echo cluster worker node vm has alias ip $(gcloud compute instances describe $name  --format="value(networkInterfaces.aliasIpRanges)")
    ```