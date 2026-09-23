# Prometheus Queries

Prometheus was accessed via port-forward:

```bash
kubectl port-forward -n monitoring svc/monitoring-kube-prometheus-prometheus 9091:9090
```

Then opened at `http://localhost:9090` → **Graph** tab.

> The exact service name depends on the Helm release name used during install
> (`helm list -n monitoring` shows it). This project used the release name `monitoring`.

-----

## Query 1 — Pod CPU Usage

```promql
sum(rate(container_cpu_usage_seconds_total{namespace="default"}[5m])) by (pod)
```

**Measures:** CPU cores consumed per pod, averaged over the last 5 minutes.

**Screenshot:** `../screenshots/prometheus/query1-cpu-usage.png`

-----

## Query 2 — Pod Memory Usage

```promql
sum(container_memory_working_set_bytes{namespace="default"}) by (pod)
```

**Measures:** Current memory actively used per pod (the metric Kubernetes itself uses for OOM decisions).

**Screenshot:** `../screenshots/prometheus/query2-memory-usage.png`

-----

## Query 3 — Pod Information

```promql
kube_pod_info{namespace="default"}
```

**Measures:** Metadata about each pod — node it’s running on, pod IP, and other identifying labels.

**Screenshot:** `../screenshots/prometheus/query3-pod-info.png`

-----

## Query 4 — Pod Restart Counts

```promql
kube_pod_container_status_restarts_total
```

**Measures:** How many times each container has restarted — a key signal of crash-looping or instability.

**Screenshot:** `../screenshots/prometheus/query4-restart-counts.png`

-----

## Query 5 — Deployment Replica Information

```promql
kube_deployment_status_replicas_available{deployment="nginx-deployment"}
```

**Measures:** How many replicas are actually available and serving traffic, compared against the
desired count (`kube_deployment_spec_replicas`). A gap between the two indicates a rollout or
scheduling problem.

**Screenshot:** `../screenshots/prometheus/query5-replicas.png`

-----

## Query 6 — Node-Related Metrics

```promql
sum(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)
```

**Measures:** CPU usage per node (excluding idle time) — shows overall cluster capacity pressure,
not just individual pods.

**Screenshot:** `../screenshots/prometheus/query6-node-cpu.png`
