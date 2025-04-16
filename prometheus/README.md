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

### 3. Prometheus Service Configuration
The `deployment.yaml` also includes a `Service` definition:
- **Type:** `NodePort` (Allows external access to Prometheus from outside the cluster)
- **NodePort:** `30007` (Prometheus is accessible on this port from cluster nodes)
- **Target Port:** `9090` (Maps to Prometheus inside the pod)

## Resource Requirements & Probes
To ensure optimal performance and stability for Prometheus, we configure resource requests and limits to match exactly. This gives the pod a Guaranteed Quality of Service (QoS) in Kubernetes, meaning it receives the highest scheduling priority and is the least likely to be evicted under resource pressure.
### ⚙️ Guaranteed QoS Resource Configuration
```
resources:
  requests:
    memory: "1000Mi"
    cpu: "250m"
  limits:
    memory: "1000Mi"
    cpu: "250m"

```

## 🩺 Liveness & Readiness Probes

Prometheus exposes internal health endpoints that Kubernetes can use to monitor its state:

- `/-/healthy` – Used for liveness probing (whether the service is still functioning)
- `/-/ready` – Used for readiness probing (whether Prometheus is ready to serve traffic)



```
livenessProbe:
  httpGet:
    path: /-/healthy
    port: 9090
  initialDelaySeconds: 15
  periodSeconds: 10
  failureThreshold: 3

readinessProbe:
  httpGet:
    path: /-/ready
    port: 9090
  initialDelaySeconds: 5
  periodSeconds: 10
  failureThreshold: 3


```
- **Liveness Probe** restarts the pod if Prometheus becomes unresponsive or enters a bad state.
- **Readiness Probe** ensures that traffic is only routed to the pod when it is fully initialized and ready to handle requests.

Together, these probes help Kubernetes manage the Prometheus pod lifecycle with zero downtime during deploys or upgrades.

## Notes
- Ensure Grafana is correctly configured to visualize Prometheus metrics.
- Modify `deployment.yaml` as needed to adjust Prometheus configurations.
- Prometheus should be correctly integrated with Alertmanager for notifications.
- The `NodePort` service allows external access; consider using an `Ingress` for better security and flexibility.

## Contributing
Feel free to modify the deployment configurations to improve monitoring. Pull requests are welcome!
