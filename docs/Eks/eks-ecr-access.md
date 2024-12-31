# EKS deploy from ECR
Amazon Elastic Container Registry (ECR) is a fully managed container registry that makes it easy to store, manage, share, and deploy your container images and artifacts anywhere. Configuring EKS to access to ECR in local account is usually via IAM role attached to instance profile. However in scenarios when EKS and ECR are configured in different account, some additional configuration is required.

In this example, we will create an ECR repo on account A and will configure access from Account B.

**Configuration.**

Create new ECR registry in Account A, and then edit permissions.

![Create ECR registry ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/ecr-create-repo.png){: style="height:250px;width:500px"  }
![ECR edit permission ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/ecr-permission.png){: style="height:250px;width:200px"  }

Add following Json policy, updating Aws Account ID

```json
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::<accountB_id>:root",
        ]
      },
      "Action": [
        "ecr:BatchCheckLayerAvailability",
        "ecr:BatchGetImage",
        "ecr:GetAuthorizationToken",
        "ecr:GetDownloadUrlForLayer"
      ]
    }
  ]
}
```
Permission summary
![ECR permission summary ](https://vettom-images.s3.eu-west-1.amazonaws.com/aws/ecr-permission-summary.png){: style="height:300px;width:600px" }


This will allow EKS on  AWS account A to pull image from ECR registry in account B. Policy must be applied to each repository you create.

## Pushing image to ECR
Log on to ECR repository via Podman/Docker. Once logged in you can push the image
```bash
aws ecr get-login-password --region eu-west-2 | podman login --username AWS --password-stdin <aws account id>.dkr.ecr.eu-west-2.amazonaws.com
```
