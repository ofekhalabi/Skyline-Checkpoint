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

## Notes
- Ensure Prometheus is deployed and accessible at the specified service URL.
- Modify `configMap.yaml` as needed to customize dashboards and data sources.
- Consider exposing Grafana externally via an Ingress for easier access.
- **Health Check:** The Ingress configuration includes a `/healthz` endpoint for checking Grafana's health. Ensure that the Ingress controller is correctly set up to handle this path. You can test the health check with:


## Contributing
Feel free to modify the deployment configurations to improve monitoring. Pull requests are welcome!

