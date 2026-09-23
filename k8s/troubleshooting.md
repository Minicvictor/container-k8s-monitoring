# Task 11 — Simulate a Kubernetes Problem

## Problem created

A failed Deployment rollout was deliberately triggered by pointing the Deployment at a
nonexistent image tag:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:doesnotexist
```

## What Kubernetes showed

```bash
kubectl get pods
```

New pods were stuck in `ErrImagePull` / `ImagePullBackOff` status, while the old, healthy pods
from the previous ReplicaSet remained running (Kubernetes does not kill working pods until
replacements are confirmed healthy).

```bash
kubectl describe pod <new-pod-name>
```

The Events section showed repeated failed pull attempts and the reason
(`manifest for nginx:doesnotexist not found`).

## What metrics changed

In Grafana’s **Panel 5 — Deployment Replicas**, the “available” replica count dropped below the
“desired” count (3) while the new pods failed to become ready. Prometheus query
`kube_deployment_status_replicas_available{deployment="nginx-deployment"}` reflected the same dip.
No restart count increase was observed, since these pods never successfully started (this is
distinct from a crash-loop, which shows up as restarts).

## How the problem was identified

1. `kubectl get pods` showed pods in a non-`Running` state.
1. `kubectl describe pod` confirmed the root cause was an image pull failure, not a crash.
1. The Grafana replicas panel confirmed the same issue was visible from the monitoring layer,
   not just the command line.

## How it was resolved

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:latest
kubectl rollout status deployment/nginx-deployment
```

The Deployment rolled forward to the valid image, all 3 replicas returned to `Running`, and the
Grafana replicas panel showed available replicas back at 3/3.

## Why this exercise matters

Installing Prometheus and Grafana proves the tools work. Deliberately breaking something and using
those same tools to *diagnose* it proves the monitoring is actually useful — which is the real
point of the assignment.
