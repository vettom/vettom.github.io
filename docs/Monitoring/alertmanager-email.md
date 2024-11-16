# Alertmanager email notification
![Prometheus Alertmanager](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/alertmanager.jpg){: style="height:100px;width:100px" align=right }
[Prometheus Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/) supports various kind of alerting. In this example, I will demonstrate how to configure Gmail account to send alerts. I will be using `kube-prometheus-stack` and only covering alertmanager section.

### Getting Gmail credentials
Log on to your Gmail account and Navigate to `Manage your google accounts`. Search for `App passwords` and generate a new password.

### Create K8s secret
Alertmanager needs to authenticate with Email server, however storing password in configuration file is not secure. So we will create a k8s secret in right namespace and mount it as a file in Alertmanager. Ideally password should be stored in secrets manager and tools like (External Secrets Manager)[https://external-secrets.io/latest/] should be used.
```bash
kubectl create secret generic alertmanager-smtp-secret \
 -n monitoring --from-literal=gmail-password=<password>
```

### Configure Alerting
In this example I am configuring my email as default receiver, and configurations are stored in global section. Prometheus is installed in monitoring namespace
- Volume mount   : Create volume mont so that the secret is available as a file
- receiver       : Configuration for receiver. Smtp host and authentication details
- routes         : Defines which messages are routed to this receiver and can be controlled by labels. Since it is default receiver all alerts are send.

```yaml
alertmanager:
  enabled: true
  config:
    global:
      smtp_smarthost: smtp.gmail.com:587
      smtp_auth_username: vettom@gmail.com
      smtp_from: vettom@gmail.com
      smtp_auth_password_file: /etc/alertmanager/secrets/gmail-password
    receivers:
      - name: 'null'
      - name: 'default-receiver'
        email_configs:
          - to: 'vettom@example.com'
    route:
      routes:
        - receiver: 'default-receiver'
        #   matchers:
        #     - severity="warning"
        #     - severity="critical"
  alertmanagerSpec:
    volumes: 
      - name: smtp-secret-volume
        secret:
          secretName: alertmanager-smtp-secret
    volumeMounts:
      - name: smtp-secret-volume
        mountPath: /etc/alertmanager/secrets
        readOnly: true
```

## Reload Prometheus/Alertmanager config
When changes are applied, `config-reloader` will automatically reload configuration after minute or so. You can force reload by sending a POST signal to `config-reloader` port. 
```bash
curl -X POST localhost:9093/-/reload
```