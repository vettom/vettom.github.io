# Low cost Single node GKE cluster 

## Quick cheap GKE cluster with Gcloud command.(About $0.11 per hour)
While experimenting with GKE cluster, we need to keep cost to minimum. By default Google prompts you to create `Autopilot cluster` which is regional, which means minimum 3 nodes. By switching to `Classic` mode you will be able to create Zonal cluster with single node, and make use of `Pre-emptible` VM's to reduce cost. You can also turn off logging and monitoring options unless required.

Here is Gcloud command to create single node GKE cluster that costs about $0.11 an hours. Set variables as required and create new cluster or follow console instructions below. Check out [Cluster Management Free tier](https://cloud.google.com/kubernetes-engine/pricing#cluster_management_fee_and_free_tier) for cost details

## Create zonal cluster Gcloud
**Set variables**
```bash
# Open Cloud shell in browser and execute.
CLUSTER_NAME="gke-cluster"
PROJECTNAME="master-project-373217"
MACHINE_TYPE="e2-small"
DISK_SIZE="30"
CLUSTER_VERSION="latest"
VPC="master-vpc"
SUBNET_NAME="prod-k8s-network"
SUBNET="projects/$PROJECTNAME/regions/us-west1/subnetworks/$SUBNET_NAME"
MASTER_NETWORK="projects/$PROJECTNAME/global/networks/$VPC"
```
**Create Cluster**
```bash
gcloud beta container --project $PROJECTNAME clusters create $CLUSTER_NAME --zone "us-west1-a" --tier "standard" --no-enable-basic-auth --cluster-version $CLUSTER_VERSION --release-channel "regular" --machine-type "e2-small" --image-type "COS_CONTAINERD" --disk-type "pd-standard" --disk-size $DISK_SIZE --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --spot --num-nodes "1" --enable-ip-alias --network $MASTER_NETWORK --subnetwork $SUBNET --no-enable-intra-node-visibility --default-max-pods-per-node "110" --enable-ip-access --security-posture=standard --workload-vulnerability-scanning=disabled --no-enable-master-authorized-networks --no-enable-google-cloud-access --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 --binauthz-evaluation-mode=DISABLED --no-enable-managed-prometheus --enable-shielded-nodes  --node-locations "us-west1-a"
```
### Connect to GKE cluster
```bash
gcloud container clusters get-credentials gke-cluster --zone us-west1-a --project master-project-373217
```

## Create Zonal cluster via Console
- Switch to classic/Standard mode
- Select location as Zonal
- Navigate to `defaultpools->Nodes` and select e2-small
    - Select `standard persistent disk`
    - Select `Enable Nodes on Spot VM's`
- Networking tab, select networks
- Features tab disable/uncheck following unless required
    - `Enable Logging`
    - `Enable Cloud Monitoring`
    - `Enable Managed Service for Prometheus`
        