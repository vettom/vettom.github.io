---
draft: false 
date: 2024-04-02 
categories:
  - eks
  - kubernetes
  - aws secrets manager
  - external secrets
  - secrets as volume
---
# EKS secrets as env variable with CSI driver
![alt text](img/kubernetes-csi.jpg "target group status"){ width="250" }

CSI driver by default configured to mount secrets as file. However it is possible to mount secrets are Environment variable using below method. When the pod is started,
driver will create a secret and mount as Environment variable. Secrets object only exists while pod is active.

[ Source code Aws-Eks-SecretsManager](https://github.com/vettom/Aws-Eks-SecretsManager)

Configuring Secrets manager [AWS Secrets Manager and Config Provider](https://github.com/aws/secrets-store-csi-driver-provider-aws#usage)

## Scenario
- You have configured Secret called MySecret with data username and password
- Necessary policy created in AWS to allow access to Secret
- Iamservice account called 'nginx-deployment-sa' created and policy attached

## Creating K8s Secrets from AWS Secrets

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: aws-secret-to-k8s-secret
  namespace: default
spec:
  provider: aws
  parameters:
    objects: |
        - objectName: "MySecret"
          objectType: "secretsmanager"
          objectAlias: mysecret
          jmesPath:
            - path: "username"
              objectAlias: "Username"
  secretObjects:
    - secretName: myusername
      type: Opaque
      data: 
        - objectName: "Username"
          key: "username"
    - secretName: myk8ssecret
      type: Opaque
      data: 
        - objectName: "mysecret"
          key: "mysecret"

```
#### spec.parameters
  - objectName: Name of the secret object in secretStore
  - objectAlias: Optional Alias name for secretObject.
  - jmesPath.path : Name of specific secret to be exposed
  - jmesPath.objectAlias: Alias name for the seccret to be used.

#### spec.secretObjects
  - secretName: Name of the secret to be created in k8s
  - data.objectName: Name of the secretObject/Alias to retrieve data from
  - key: Name of the key with in k8s secret to be used for storing retrieved data.

  Above configuration will create k8s secret called 'myusername' with value of username in key 'username'. k8s secret 'mysecrets' will contain all objects in Mysecrets under k8s secret key 'mysecrets'