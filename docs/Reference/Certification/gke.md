# Kubernetes

## Cluster options
- Release channel
    - Regular : Balance feature and availability
    - Rapid : All latest features
    - Stable : Stability over feature
    - Extended : Stay on minor version 
    - No channel : No auto upgrades.
- Location 
    - Zonal
    - Regional
- Fleet : Logically grou cluster. For outside project only CLI option to register
- Nodepool upgrade
    - Surge : Manage how many nodes at a time rolling
    - Blue/green
    - OS : Optimized, Ubuntu or windown
- GKE backup optional
- Features
    - Logging
    - Monitoring
    - Service mesh
    
## Gcloud GKE cluster
Single node, zonal cluster with no logging or monitoring. Minimal install with SPOT instance.

```bash
gcloud beta container --project "master-project-373217" clusters create "cluster-1" --zone "us-west1-a" --no-enable-basic-auth --cluster-version "1.30.5-gke.1014001" --release-channel "regular" --machine-type "e2-medium" --image-type "COS_CONTAINERD" --disk-type "pd-standard" --disk-size "50" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --spot --num-nodes "1" --enable-ip-alias --network "projects/master-project-373217/global/networks/master-vpc" --subnetwork "projects/master-project-373217/regions/us-west1/subnetworks/prod-k8s-network" --no-enable-intra-node-visibility --default-max-pods-per-node "110" --security-posture=standard --workload-vulnerability-scanning=disabled --enable-dataplane-v2 --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 --binauthz-evaluation-mode=DISABLED --no-enable-managed-prometheus --enable-shielded-nodes --node-locations "us-west1-a"
```

## Autoscaler
- Specify min and max nodes
- Location `balanced` or `any`
