# A cheat sheet

### S3
```bash
# Calculate S3 size
aws s3 --region eu-central-1 ls s3://vettom --recursive --human-readable --summarize 
```

### MFA
```bash
# List MFA devices
aws iam list-virtual-mfa-devices | grep -i user

# Delete MFA device
aws iam delete-virtual-mfa-device --serial-number 
```


