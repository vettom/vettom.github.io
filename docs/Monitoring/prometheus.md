# Prometheus

![Prometheus](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/prometheus.png){: style="height:100px;width:100px" align=right }

## Metric type
- Gauges    : Snapshot of changing value, can go up or down. Eg: current memory usage.
- Counter   : Always increases. Like total requests count. Used to identify rate of increase
- Histogram : Records data in buckets. Like 99% requests time, 90% requests etc. 
- Summary   : Record average. It provide 2 information, count and seconds. For eg : 5 req per sec requests

## Prometheus Service Discovery
Is a mechanism that enables Prometheus to automatically find and monitor targets (e.g., services, instances, or endpoints) without requiring manual configuration. This is essential in dynamic environments, such as Kubernetes, cloud platforms, or containerized applications, where services and their instances frequently change. Supports varioud SD configuration like file, HTTP, and various cloud providers.

### relabel_config
Enables renaming the label received to something else. In below example, if the value of the source label `runtime` is `container`, replace it as `docker`
```yaml
relabel_config:
    - source_labels: [runtime]
      regex: container
      target_label: runtime
      replacement: docker
```
### metric_relabel_config
Allows relabel, drop metrics altogether at time of scrape. 
Below example if the metric name start with `go_` it is dropped immediately.
```yaml
metric_relabel_configs:
    - source_labels: [__name__]
      regex: 'go_.*'
      action: drop
```
Metric relabel can be used to change the metric name, value etc.

## PromQL
Simple promQl selector allow retrieving data matching those selector
`kube_pod_owner{namespace="argocd", pod=~".*repo.*"}` Selects namespace `argocd` and pod name containing `repo`
`rate(node_cpu_guest_seconds_total[5m])`  : Counter increase rate over 5min period
`sum(rate(node_cpu_guest_seconds_total[5m])) by (namespace,pod)` : Counter sum of rate change by namespace and podname.
`sum(rate(node_cpu_guest_seconds_total[5m])) without (namespace)` : Counter sum and omit namespace data.
