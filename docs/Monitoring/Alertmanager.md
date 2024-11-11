# Prometheus Alert Manager

Alertmanager configuration allows multiple way to customize the alerts

- Grouping : Group similar kind of alerts
- Routing  : Sending alerts to different channels
- Inhibit  : Define dependency to get alert for actual cause of problem
- Silence  : Quiet alerts or schedules alerts like disable for maintenance.

## Configuring alerts
To test an alert define `exps:1` and this will always fire
Define th alerts with Labels like Severity, service, team etc so that filters and inhibit rules can be applied.
```yaml
    labels:
        team: operations
        severity: warning
        service: webapp
```
##### Configure routes
Configure routes to define what action to be taken for specific alert. There must be a default alert receiver for anything that does not match the rules.
```yaml
    route:
        - match
            severity: critical
            receiver: devops_pager
        - match
            service: webapp
            receiver: webteam_email
```
##### Receivers
Receivers mustbe configured with matching names and define how the alerts are delivered
```yaml
receivers:
    - name: devops_pager
      webhook_configs:
        - url: htpps:someurl.com
          send_resolved: true
    - name: webteam_email
      webhook_configs:
        - url: htpps:someurl.com
          send_resolved: false
```
#### Group alerts
Configure alerts groups based on labels to reduce individual alerts. for example group by `[team, service, severity]`. There is a default grouping rule by default.

### Group delivery config
- group_wait  : how long to wait before sending signal
- group_interval :  How long before new alert is send to group that already received an alert
- repeat_interval : How long before resend alert

## Inhibit rules
This enables duplication of alerts, for example if network is down, no need to alert for server down. Inhibit_rules has 2 sections, `source_match` is the root/parent alert and `target_match` is the child. If `source_match` is alerting, alerts from `target_match` are ignored.
In below example, when network is down, server alerts are not send. Severity based rule ignores `warning` if `critical` alert is triggering provided both alerts share same service label.
```yaml
- inhibit_rules:
    - source_match: 
        service: network
      target_match:
        service: servers
    - source_match: 
        severity: critical
      target_match:
        severity: warning
      equal: ['service']
```
## Notification templates
Allows customization of alert by populating dynamic values leveraging GO template. 
