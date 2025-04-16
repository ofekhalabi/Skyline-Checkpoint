# Grafana Configuration for Skyline-Checkpoint

## Overview
This directory contains the necessary configurations for deploying Grafana in a Kubernetes environment using ArgoCD. Grafana is used for visualizing metrics collected from Check Point security appliances and other monitored components.

## Directory Structure
```
├── configMap.yaml          # Configuration for dashboards and data sources
├── deployment.yaml         # Kubernetes deployment file for Grafana
├── grafana_pv.yaml         # PersistentVolume (PV) configuration for Grafana storage
├── grafana_pvc.yaml        # PersistentVolumeClaim (PVC) for Grafana data storage
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
#### Environment Variables
Grafana supports environment variables to customize its behavior. The following environment variables can be set in deployment.yaml:
- **GF_SECURITY_ADMIN_USER**: Specifies the admin username for Grafana.
- **GF_SECURITY_ADMIN_PASSWORD**: Defines the admin password for Grafana.

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

## Exposing Grafana via an Ingress Controller (Optional)
To expose Grafana externally, use an Nginx Ingress Controller.
Nginx Ingress Controller is one of the most widely used options for exposing services outside a Kubernetes cluster. It acts as a reverse proxy, routing incoming traffic to different Services.

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0/deploy/static/provider/baremetal/deploy.yaml
   ```
Once deployed, configure ingress.yaml to expose Grafana externally.

If you are using AWS Elastic Load Balancer (ELB):

To discover the ports .... : 

```console
$ kubectl describe service ingress-nginx-controller -n ingress-nginx

Name:                     ingress-nginx-controller
Namespace:                ingress-nginx
Labels:                   app.kubernetes.io/component=controller
                          app.kubernetes.io/instance=ingress-nginx
                          app.kubernetes.io/name=ingress-nginx
                          app.kubernetes.io/part-of=ingress-nginx
                          app.kubernetes.io/version=1.12.0
Annotations:              <none>
Selector:                 app.kubernetes.io/component=controller,app.kubernetes.io/instance=ingress-nginx,app.kubernetes.io/name=ingress-nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.103.240.159
IPs:                      10.103.240.159
Port:                     http  80/TCP
TargetPort:               http/TCP
NodePort:                 http  31867/TCP      <---- Node port for HTTP traffic
Endpoints:                192.168.51.196:80
Port:                     https  443/TCP
TargetPort:               https/TCP
NodePort:                 https  30683/TCP     <---- Node port for HTTPS traffic
Endpoints:                192.168.51.196:443
Session Affinity:         None
External Traffic Policy:  Cluster
Internal Traffic Policy:  Cluster
Events:                   <none>
```

In your Application Load Balancer, create 2 target groups and 2 listeners, as follows:

   1. Target group that routes traffic to your HTTP NodePort (`31867` in the above example output). The health checks endpoint can be `/healthz`.
   2. In your ELB, create a corresponding listener on port 80.
   3. Similarly, another target group for the HTTPS NodePort (`30683` in the above example output).
   4. create a corresponding listener on port 443. 

## Resource Requirements & Probes
To ensure optimal performance and stability for Grafana, we configure resource requests and limits to match exactly. This gives the pod a Guaranteed Quality of Service (QoS) in Kubernetes, meaning it receives the highest scheduling priority and is the least likely to be evicted under resource pressure.
### ⚙️ Guaranteed QoS Resource Configuration
```
resources:
  requests:
    memory: "512Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "250m"

```

## 🩺 Liveness & Readiness Probes
Grafana exposes a `/api/health` endpoint that can be used to monitor both liveness and readiness of the application.

```
livenessProbe:
  httpGet:
    path: /api/health
    port: 3000
  initialDelaySeconds: 10
  periodSeconds: 10
  failureThreshold: 3

readinessProbe:
  httpGet:
    path: /api/health
    port: 3000
  initialDelaySeconds: 5
  periodSeconds: 10
  failureThreshold: 3

```
- **Liveness Probe** restarts the pod if Grafana becomes unresponsive or enters a bad state.
- **Readiness Probe** ensures that traffic is only routed to the pod when it is fully initialized and ready to handle requests.

Together, these probes help Kubernetes manage the Grafana pod lifecycle with zero downtime during deploys or upgrades.

## Notes
- Ensure Prometheus is deployed and accessible at the specified service URL.
- Modify `configMap.yaml` as needed to customize dashboards and data sources.
- Consider exposing Grafana externally via an Ingress for easier access.
- **Health Check:** The Ingress configuration includes a `/healthz` endpoint for checking Grafana's health. Ensure that the Ingress controller is correctly set up to handle this path. You can test the health check with:


## Contributing
Feel free to modify the deployment configurations to improve monitoring. Pull requests are welcome!

