# Helm Verification

## Install

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
```

## Task 12 — Demonstrate Helm Knowledge

### List installed releases

```bash
helm list -n monitoring
```

Confirms the `monitoring` release is installed, its chart version, and its status (`deployed`).

### Check release status

```bash
helm status monitoring -n monitoring
```

Shows the release’s current health, notes from the chart, and revision number.

### Inspect the configured values

```bash
helm get values monitoring -n monitoring
```

Shows which configuration values were applied at install time (or confirms defaults were used).

### Inspect the rendered Kubernetes manifests

```bash
helm get manifest monitoring -n monitoring
```

Shows the full, raw Kubernetes YAML that Helm generated and applied — this is what turns a chart
into actual running resources.

### Identify the Kubernetes resources created by the release

```bash
kubectl get all -n monitoring
```

Lists every Deployment, StatefulSet, Pod, Service, and related object the chart created,
including Prometheus, Grafana, Alertmanager, and their supporting components.

## Why this matters

Helm charts can install dozens of resources at once. Being able to inspect what a chart actually
installed — not just trust that “helm install worked” — is essential for debugging, upgrading, or
uninstalling cleanly later.
