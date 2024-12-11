# EKS Pod identity
![EKS logo ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/eks_logo.jpg){: style="height:100px;width:100px" align=right }
Applications in a Pod’s containers can use an AWS SDK or the AWS CLI to make API requests to AWS services using AWS Identity and Access Management (IAM) permissions. Applications must sign their AWS API requests with AWS credentials. Until recently IAM RSA using OpenID connect was used to provide authentication mechanism for the pods. 

EKS Pod Identity was introduced to simplify authentication process for the pods and it enables mapping of K8s service account to AWS IAM role. This simplifies the configuration as well as enabling re-use of IAM roles.

## Configuring Pod identity
1. Install EKS pod Identity agent
2. Create a role with trust policy for `pod identity` and assign necessary permissions.
3. Create Pod Identity association to role in EKS
4. Configure application to use configured serviceAccount

### Scenario
Your pod must be able to list and retrieve items from an S3 bucket. Pod identity agent already installed on EKS cluster. Application will run in `example` namespace and service account `exampleserviceaccount`

#### Create IAM role
First task is to create IAM role with pod identity trust policy. Assign necessary S3 permission and name policy (pod-id-example-role). 
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowEksAuthToAssumeRoleForPodIdentity",
            "Effect": "Allow",
            "Principal": {
                "Service": "pods.eks.amazonaws.com"
            },
            "Action": [
                "sts:AssumeRole",
                "sts:TagSession"
            ]
        }
    ]
}
```
#### Associate service account to role
Next step is to associate IAM role to kubernetes serviceAccount. Navigate to AWS Console -> EKS cluster -> Access tab and `create pod identity association`. Specify Role, namespace and service account.
![pod identity association](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/podid-associate.png){: style="height:300px;width:600px" }

#### Deploy application
Create Service account in `example` namespace and configure app to run using this service account. Application is a pod with AWS CLI pre-installed for testing.

```yaml
cat >example.yaml <<EOF
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: example
spec: {}
status: {}
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: exampleserviceaccount
  namespace: example
---
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: dvawscli
  name: dvawscli
  namespace: example
spec:
  replicas: 1
  selector:
    matchLabels:
      app: dvawscli
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: dvawscli
    spec:
      serviceAccountName: exampleserviceaccount
      containers:
      - image: dennysv/dvawscli
        name: dvawscli
        resources: {}
EOF
```
Apply and test configuration
```bash
# Create application
kubectl apply -f example.yaml
# ssh on to the pod and verify S3 access
aws s3 ls s3://eks-automation/
```

## Pod Identity vs IAM-RSA comparison
POD Identity and IAMRSA are two methods that can be used to allow pods to access AWS resources while running on EKS cluster. Pod Identity is more flexible, can work across clusters, does not require `annotations` to be added to serviceAccount, and enables reuse of IAM policy. This works more like InstanceProfile.

|Pod Identity |IAM RSA|
|-------------------------| ------------------------------------------------|
|No external Authentication |Requires OIDC provider to be configured|
|ServiceAccount name maps to Role|Requires annotation with IAM role ARN|
|Works across cluster |Maps to single OIDC provider |
|IAM role can be reused|IAM role created with trust policy and OIDC configuration. |

## Terraform code 
Here is an example terraform code that creates role with Trust policy for Pod identity and permission to list s3 buckets. Then role is  associated with namespace and service account.

```bash
data "aws_iam_policy_document" "assume_role" {
  statement {
    effect = "Allow"

    principals {
      type        = "Service"
      identifiers = ["pods.eks.amazonaws.com"]
    }

    actions = [
      "sts:AssumeRole",
      "sts:TagSession"
    ]
  }
}

resource "aws_iam_role" "example" {
  name               = "eks-pod-identity-example"
  assume_role_policy = data.aws_iam_policy_document.assume_role.json
}

resource "aws_iam_role_policy_attachment" "example_s3" {
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
  role       = aws_iam_role.example.name
}

resource "aws_eks_pod_identity_association" "example" {
  cluster_name    = aws_eks_cluster.example.name
  namespace       = "example"
  service_account = "exampleserviceaccount"
  role_arn        = aws_iam_role.example.arn
}
```