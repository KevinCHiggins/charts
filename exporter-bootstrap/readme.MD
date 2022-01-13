### Metrics visualisation PoC

#### Description

This is a PoC for a single ArgoCD application to provide observability for a Kubernetes cluster. It is composed of Prometheus, Grafana (both provided by the kube-prometheus-stack chart), a customised version of k8s-sidecar, and custom metrics exporters. This solution will automatically pick up new metrics exporters in the mn namespace (if they have serviceMonitor.enabled) and will automatically display Grafana dashboards specified in its dashboards/kong.json file.

To start the app, click ‘New App’ in ArgoCD and point it to this Git repository. Most of the components are included as subchart dependencies in the Chart.yaml file. The exception is k8s-sidecar which has been customised. **TODO**

#### Requirements:

 - a Kubernetes cluster

 - ArgoCD

 - some apps to scrape... currently this PoC is set up to scrape two instances each of Redis and Postgres

#### Solution Components:

 - Prometheus

 - Grafana

 - k8s-sidecar

 - exporter-bootstrap - I deployed it as an ArgoCD app created from a manifest, referencing…

 - prometheus-redis-exporter - an example of an exporter which must have a 1:1 relation with the app instance being scraped (so we deploy it twice, once for each Redis instance)

 - prometheus-postgres-exporter - an example of an exporter which may have a 1:many relation with app instances (so we deploy it once to scrape two separate Postgres instances)

#### Workarounds and known issues

Prometheus admission webhooks have been disabled for ease of development, as they were consistently failing to be deleted in ArgoCD. prometheusOperator.tls has been disabled as part of this too.

For simplicity, Prometheus has been set to discover **all** metrics exporters in its namespace. This avoids the need to configure labels.

The Postgres exporter supports connecting to multiple instances, but only if a Secret containing connection strings in a particular format is used. Note that **newlines are forbidden** in the connection string yet it is quite easy to introduce them accidentally during conversion to base64.

#### Security concerns

Login details for apps to be scraped, as well as for Grafana, are currently stored in plain text in values.yaml.
