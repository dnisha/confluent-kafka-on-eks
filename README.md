# CFK Installation via Bundle (Helm)

This guide explains how to install **Confluent for Kubernetes (CFK)** using the downloaded bundle and Helm.

---

## Overview

This method installs CFK from a **downloaded tar bundle** instead of directly from the Helm repository.

---

## Prerequisites

Make sure you have:

* Running Kubernetes cluster
* kubectl configured
* Helm (v3+) installed
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
cd confluent-for-kubernetes-3.2.0
```

---

## Step 3: Create Namespace

```bash
kubectl create namespace operator
```

---

## Step 4: Install CFK Operator using Helm

Navigate to the Helm chart directory:

```bash
cd helm
```

Run installation:

```bash
helm upgrade --install confluent-operator \
  --set operator.enabled=true \
  --namespace operator \
  ./confluent-for-kubernetes
```

---

## Step 5: Verify Installation

```bash
kubectl get pods -n operator
```

You should see the **CFK operator pod** running.

---

## Validate CRDs

```bash
kubectl get crds | grep confluent
```

---

## Optional: Dry Run (Recommended)

Before actual deployment:

```bash
helm upgrade --install confluent-operator \
  --set operator.enabled=true \
  --namespace operator \
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
  --namespace operator \
  ./confluent-for-kubernetes
```

---

### Use Custom Values File

```bash
helm upgrade --install confluent-operator \
  -f custom-values.yaml \
  --namespace operator \
  ./confluent-for-kubernetes
```

---

### Skip CRDs (if pre-installed)

```bash
helm upgrade --install confluent-operator \
  --skip-crds \
  --namespace operator \
  ./confluent-for-kubernetes
```

---

### Disable RBAC Creation

```bash
helm upgrade --install confluent-operator \
  --set rbac=false \
  --namespace operator \
  ./confluent-for-kubernetes
```

---

## Uninstall

```bash
helm uninstall confluent-operator -n operator
kubectl delete namespace operator
```

---

## Common Issues

* **CRD permission error** → Use `--skip-crds` if admin installed them
* **RBAC error** → Use `--set rbac=false`
* **Pod not starting** → Check logs:

  ```bash
  kubectl logs <pod-name> -n operator
  ```

---

## Summary

| Step | Action           |
| ---- | ---------------- |
| 1    | Download bundle  |
| 2    | Extract files    |
| 3    | Create namespace |
| 4    | Install via Helm |
| 5    | Verify pods      |

