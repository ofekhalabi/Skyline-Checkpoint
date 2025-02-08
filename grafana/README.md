# Grafana Configuration for Skyline-Checkpoint

## Overview
This directory contains the necessary configurations for deploying Grafana in a Kubernetes environment using ArgoCD. Grafana is used for visualizing metrics collected from Check Point security appliances and other monitored components.

## Directory Structure
```
├── configMap.yaml          # Configuration for dashboards and data sources
├── deployment.yaml         # Kubernetes deployment file for Grafana
├── ingress.yaml            # Kubernetes ingress file for Grafana (optional)
├── grafana-dashboards/     # Contains JSON files for pre-configured dashboards
```

## Setup & Deployment
### 1. Deploying Grafana using ArgoCD
To deploy Grafana in your Kubernetes cluster using ArgoCD:
1. Add the application configuration to your ArgoCD instance.
2. Ensure that `deployment.yaml` and `configMap.yaml` are correctly referenced in the ArgoCD application manifest.
3. Sync the application in ArgoCD to deploy Grafana.

### 2. Grafana Deployment Configuration
The `deployment.yaml` file defines the Grafana deployment:
- **Replicas:** 2 instances of Grafana are deployed for high availability.
- **Container Image:** `grafana/grafana:10.4.14`
- **Port:** Grafana runs on port `3000`.
- **ConfigMaps Mounted:**
  - `grafana-dashboard-provider`: Provides pre-configured dashboards.
  - `grafana-datasources`: Configures Prometheus as the default data source.

### 3. Grafana Service Configuration
The `deployment.yaml` also includes a `Service` definition:
- **Type:** `ClusterIP` (Accessible within the cluster)
- **Port:** `3000`

## Configuration Details
### 1. Dashboard Configuration (`grafana-dashboard-provider` ConfigMap)
The `configMap.yaml` file includes:
- **Dashboards Path:** `/var/lib/grafana/dashboards`
- **Refresh Interval:** Dashboards are updated every 30 seconds.
- **Editable:** Dashboards can be modified within Grafana.

### 2. Data Source Configuration (`grafana-datasources` ConfigMap)
- **Prometheus URL:** `http://prometheus-service.monitoring.svc.cluster.local:9090`
- **Access Mode:** `proxy`
- **Default Time Interval:** 5 seconds

## Optional: Exposing Grafana with an Ingress Controller
To expose Grafana externally, use an Nginx Ingress Controller.
Nginx Ingress Controller is one of the most widely used options for exposing services outside a Kubernetes cluster. It acts as a reverse proxy, routing incoming traffic to different Services.

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0/deploy/static/provider/baremetal/deploy.yaml
   ```
Once deployed, configure ingress.yaml to expose Grafana externally.

## Notes
- Ensure Prometheus is deployed and accessible at the specified service URL.
- Modify `configMap.yaml` as needed to customize dashboards and data sources.
- Consider exposing Grafana externally via an Ingress for easier access.
- **Health Check:** The Ingress configuration includes a `/healthz` endpoint for checking Grafana's health. Ensure that the Ingress controller is correctly set up to handle this path. You can test the health check with:


## Contributing
Feel free to modify the deployment configurations to improve monitoring. Pull requests are welcome!

