# Prometheus Configuration for Skyline-Checkpoint

## Overview
This directory contains the necessary configurations for setting up Prometheus to monitor Skyline-Checkpoint environments. Prometheus is used to collect and store metrics from Check Point security appliances and other infrastructure components.

## Directory Structure
```
├── deployment.yaml       # Kubernetes deployment file for Prometheus
├── prometheus_pv.yaml       # PersistentVolume (PV) configuration for prometheus storage
├── prometheus_pvc.yaml      # PersistentVolumeClaim (PVC) for prometheus data storage
```

## Setup & Deployment
### 1. Deploying Prometheus using ArgoCD
To deploy Prometheus in your Kubernetes cluster using ArgoCD:
1. Add the application configuration to your ArgoCD instance.
2. Ensure that `deployment.yaml` is correctly referenced in the ArgoCD application manifest.
3. Sync the application in ArgoCD to deploy Prometheus.

### 2. Prometheus Deployment Configuration
The `deployment.yaml` file defines the Prometheus deployment:
- **Replicas:** 2 instances of Prometheus are deployed for high availability.
- **Container Image:** `prom/prometheus:v2.45.3`
- **Port:** Prometheus runs on port `9090`.
#### Environment Variables
Grafana supports environment variables to customize its behavior. The following environment variables can be set in deployment.yaml:
- **GF_SECURITY_ADMIN_USER**: Specifies the admin username for Grafana.
- **GF_SECURITY_ADMIN_PASSWORD**: Defines the admin password for Grafana.

### 3. Prometheus Service Configuration
The `deployment.yaml` also includes a `Service` definition:
- **Type:** `NodePort` (Allows external access to Prometheus from outside the cluster)
- **NodePort:** `30007` (Prometheus is accessible on this port from cluster nodes)
- **Target Port:** `9090` (Maps to Prometheus inside the pod)

## Notes
- Ensure Grafana is correctly configured to visualize Prometheus metrics.
- Modify `deployment.yaml` as needed to adjust Prometheus configurations.
- Prometheus should be correctly integrated with Alertmanager for notifications.
- The `NodePort` service allows external access; consider using an `Ingress` for better security and flexibility.

## Contributing
Feel free to modify the deployment configurations to improve monitoring. Pull requests are welcome!
