# CFK Installation via Bundle (Helm)

This guide explains how to install **Confluent for Kubernetes (CFK)** using the downloaded bundle and Helm.

---

## Overview

This method installs CFK from a **downloaded tar bundle** instead of directly from the Helm repository.

---

## Prerequisites

* Running Kubernetes cluster
* `kubectl` configured
* Helm v3+ installed
* Sufficient permissions (CRDs, RBAC)

---

## Step 1: Download CFK Bundle

```bash
curl -O https://packages.confluent.io/bundle/cfk/confluent-for-kubernetes-3.2.0.tar.gz
```

---

## Step 2: Extract Bundle

```bash
tar -xvf confluent-for-kubernetes-3.2.0.tar.gz
cd confluent-for-kubernetes-3.2.0-*/
```

---

## Step 3: Create Namespace

```bash
kubectl create namespace confluent
```

---

## Step 4: Install CRDs

Navigate to the Helm chart directory:

```bash
cd helm
```

Install CRDs first before the operator:

```bash
kubectl apply -f ./crds/
```

Verify CRDs are registered:

```bash
kubectl get crds | grep confluent
```

---

## Step 5: Install CFK Operator using Helm

> **Note:** If you hit `chart file "dynamic-quorum-cm" is larger than the maximum file size` error, set the env var before running Helm:

Install the operator, skipping CRDs since they are already applied:

```bash
helm upgrade --install confluent-operator \
  --set operator.enabled=true \
  --namespace confluent \
  ./confluent-for-kubernetes
```

---

## Step 6: Verify Installation

```bash
kubectl get pods -n confluent
```

You should see the **CFK operator pod** running.

Check operator logs:

```bash
kubectl logs -f deployment/confluent-operator -n confluent
```

---

## Validate CRDs

```bash
kubectl get crds | grep confluent
```

---

## Optional: Dry Run (Recommended)

```bash
helm upgrade --install confluent-operator \
  --set operator.enabled=true \
  --namespace confluent \
  ./confluent-for-kubernetes \
  --dry-run
```

---

## Optional Configurations

### Enable Cluster-wide Mode

```bash
helm upgrade --install confluent-operator \
  --set operator.enabled=true \
  --set namespaced=false \
  --namespace confluent \
  ./confluent-for-kubernetes
```

### Use Custom Values File

```bash
helm upgrade --install confluent-operator \
  -f custom-values.yaml \
  --namespace confluent \
  ./confluent-for-kubernetes
```

### Skip CRDs (if pre-installed)

```bash
helm upgrade --install confluent-operator \
  --skip-crds \
  --namespace confluent \
  ./confluent-for-kubernetes
```

### Disable RBAC Creation

```bash
helm upgrade --install confluent-operator \
  --set rbac=false \
  --namespace confluent \
  ./confluent-for-kubernetes
```

---

## Uninstall

```bash
helm uninstall confluent-operator -n confluent
kubectl delete namespace confluent
```

---

## Common Issues

| Error | Fix |
|-------|-----|
| `chart file is larger than maximum file size` | Run `export HELM_MAX_CHART_SIZE=20971520` before Helm install |
| CRD permission error | Use `--skip-crds` if CRDs were pre-installed by admin |
| RBAC error | Use `--set rbac=false` |
| Pod not starting | `kubectl logs -f deployment/confluent-operator -n confluent` |
| Describe Kafka/KRaftController CR | `kubectl describe kafka kafka -n confluent` |

---

## Summary

| Step | Action |
|------|--------|
| 1 | Download bundle |
| 2 | Extract files |
| 3 | Create namespace |
| 4 | Install CRDs |
| 5 | Install operator via Helm |
| 6 | Verify pods |
