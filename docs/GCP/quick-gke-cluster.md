# Single node GKE cluster 

### Quick cheap GKE cluster with Gcloud command.(less than 15p an hour)

```bash
# Open Cloud shell in browser and execute.
CLUSTER_NAME="gke-cluster"
PROJECTNAME="master-project-373217"
MACHINE_TYPE="e2-micro"
DISK_SIZE="30"
CLUSTER_VERSION="latest"
VPC="master-vpc"
SUBNET_NAME="prod-k8s-network"
SUBNET="projects/$PROJECTNAME/regions/us-west1/subnetworks/$SUBNET_NAME"
MASTER_NETWORK="projects/$PROJECTNAME/global/networks/$VPC"

# New config


gcloud beta container --project $PROJECTNAME clusters create $CLUSTER_NAME --zone "us-west1-b" --no-enable-basic-auth  --cluster-version $CLUSTER_VERSION --release-channel "rapid" --machine-type $MACHINE_TYPE --image-type "COS_CONTAINERD"  --disk-type "pd-balanced" --disk-size $DISK_SIZE --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --spot --num-nodes "1" --enable-ip-alias --network $MASTER_NETWORK --subnetwork $SUBNET --no-enable-intra-node-visibility --default-max-pods-per-node "110" --security-posture=standard --workload-vulnerability-scanning=disabled --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0  --no-enable-managed-prometheus --enable-shielded-nodes --node-locations "us-west1-b"
```