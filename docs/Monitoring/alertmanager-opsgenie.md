# Alertmanager Opsgenie/Jira Service Management 
![Prometheus Alertmanager](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/alertmanager.jpg){: style="height:100px;width:100px" align=right }
[Prometheus Alertmanager](https://prometheus.io/docs/alerting/latest/alertmanager/) supports various kind of alerting. In this example, I will demonstrate how to configure Jira Service Manager (JSM) previously knows and OpsGenie to send alerts. I will be using `kube-prometheus-stack` and only covering alertmanager section here.

### Configure Prometheus integration in JSM
In your JSM console, configure Prometheus integration and enable it in your team and copy API key. In this example I am using a team called `AlertTesting`. 

![JSM Prometheus integration](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/jsm-prom.jpg){: style="height:150px;width:250px" }

### Configure Alertmanager
Including API key to the code is not safe practice, so we will create a secret with API key and mount it as volume in alertmanager
```bash
kubectl create secret generic opsgenie-secret \
 -n monitoring --from-literal=opsgenie_api_key=<api key>
```

- opsgenie_api_key_file  : Name of the secret file with API key
- opsgenie_api_url       : Opsgenie/JSM URL
- priority               : JSM does not recognize priorities like `warning|critical` so they will have to be converted to `P1,P2` etc.
- responders             : Specify name of your team and type.

```yaml
alertmanager:
  enabled: true
  config:
    global:
      opsgenie_api_key_file: /etc/alertmanager/secrets/opsgenie_api_key
      opsgenie_api_url: 'https://api.eu.opsgenie.com/'
    receivers:
      - name: 'null'
      - name: 'opsgenie'
        opsgenie_configs:
          - send_resolved: false
            priority: '{{ range .Alerts }}{{ if eq .Labels.severity "critical"}}P1{{else if eq .Labels.severity "warning"}}P2{{else if eq .Labels.severity "info"}}P3{{else}}P4{{end}}{{end}}'
            responders:
              - name: 'AlertsTesting'
                type: 'team'
    route:
      group_by: ['severity']
      routes:
        - receiver: 'opsgenie'
          matchers:
            - severity=~ "warning|critical"
  alertmanagerSpec:
    volumes:
      - name: opsgenie-secret-volume
        secret:
          secretName: opsgenie-secret
    volumeMounts:
      - name: opsgenie-secret-volume
        mountPath: /etc/alertmanager/secrets
        readOnly: true 
```

Check Alertmanager -> Status URL and ensure your configuration is loaded.

## Reload Prometheus/Alertmanager config
When changes are applied, `config-reloader` will automatically reload configuration after minute or so. You can force reload by sending a POST signal to `config-reloader` port. 
```bash
curl -X POST localhost:9093/-/reload
```