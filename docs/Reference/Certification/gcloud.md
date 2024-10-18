# GcloudCLI

## Instances
```bash
gcloud compute instances start in1 in2
# Listinstances
gcloud compute instances list --filter="zone=eu-west1" 

# Get instancestart info
gcloud compute instances start in1  --async --format json
```

## Cluster
```bash
# Create cluster
gcloud container clusters create 

# List cluster
gcloud container clusters list

# Confogire cluster for kubectl
gcloud container clusters get-credentials --zone eu-west1 cluster1

# Add nodes
gcloud container clusters resize cluster1 --node-pool nodepool1 --size=5 

```