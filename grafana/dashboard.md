# Grafana Dashboard

## Access

```bash
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
```

Opened at `http://localhost:3000`.

Login: username `admin`, password retrieved with:

```bash
kubectl get secret monitoring-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 -d
```

Prometheus was already available as a pre-configured data source (installed by the
`kube-prometheus-stack` Helm chart).

-----

## Dashboard: Kubernetes Workload Monitoring

Six panels were created, each backed by a PromQL query from `../prometheus/queries.md`.

### Panel 1 — CPU Usage

Shows CPU consumption of the Kubernetes workloads over time.
Query: `sum(rate(container_cpu_usage_seconds_total{namespace="default"}[5m])) by (pod)`

### Panel 2 — Memory Usage

Shows memory consumption per pod over time.
Query: `sum(container_memory_working_set_bytes{namespace="default"}) by (pod)`

### Panel 3 — Pod Count

Displays the number of currently running pods.
Query: `count(kube_pod_info{namespace="default"})`

### Panel 4 — Pod Restarts

Displays container/pod restart counts.
Query: `kube_pod_container_status_restarts_total`

### Panel 5 — Deployment Replicas

Shows desired vs. available replicas side by side.
Queries: `kube_deployment_spec_replicas{deployment="nginx-deployment"}` and
`kube_deployment_status_replicas_available{deployment="nginx-deployment"}`

### Panel 6 — Node Resource Usage

Displays CPU (or memory) usage at the node level.
Query: `sum(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)`

**Screenshots:** `../screenshots/grafana/`

## Why a dashboard instead of ad-hoc queries

Running PromQL by hand in Prometheus’s UI is fine for investigation, but it doesn’t give an
at-a-glance view of system health, and it resets every time you close the tab. A Grafana
dashboard keeps all six signals visible together and updates live, which is what makes it
possible to notice a problem (like the one simulated in Part 7) as it’s happening.
