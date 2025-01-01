# AWS Quick reference
# Site moved to [https://vettom.pages.dev](https://vettom.pages.dev)

## ECR 
Log on to ECR registry
```bash
aws ecr get-login-password --profile labs --region eu-west-1 | podman login --username AWS --password-stdin <account_id>.dkr.ecr.eu-west-1.amazonaws.com    
```

## S3
```bash
# Calculate S3 size
aws s3 --region eu-central-1 ls s3://vettom --recursive --human-readable --summarize 
```

## MFA
```bash
# List MFA devices
aws iam list-virtual-mfa-devices | grep -i user

# Delete MFA device
aws iam delete-virtual-mfa-device --serial-number 
```


