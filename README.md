# Skyline Checkpoint
## What is Check Point Skyline?

Skyline is an advanced monitoring solution based on **Prometheus and Grafana**, designed to provide **real-time insights, monitoring, and alerts** on the performance of Check Point Security Gateways and Management Servers.

### Advantages of Skyline over CPView:
1. **Advanced Data Collection** – Uses Prometheus to gather and store historical data.  
2. **Interactive Visual Interface** – Displays data via Grafana dashboards.  
3. **Integration with External Monitoring Systems** – Supports integrations with **ELK, Splunk, DataDog, etc.**  
4. **Automation Support** – Enables advanced data analysis using **PromQL queries, smart rules, and alerting mechanisms**.  
5. **Long-Term Data Retention** – Allows access to historical data over an extended period, unlike CPView.  

---

## Skyline vs. CPView: Comparison  

| Feature            | Skyline | CPView |
|--------------------|---------|--------|
| **Interface**      | Grafana (Web UI) | CLI |
| **Data Retention** | Long-term | Short-term |
| **Automation & Alerts** | Yes | No |
| **Third-Party Integration** | Yes (Prometheus, ELK, Splunk) | No |
| **Data Granularity** | Advanced (PromQL queries) | Basic |
| **Real-Time Monitoring** | Yes, but depends on polling intervals | Yes, instant |
| **Usability** | Best for DevOps/SecOps teams | Best for Network Admins |


## The Skyline Logical Diagram
![Skyline component](https://raw.githubusercontent.com/ofekhalabi/Skyline-Checkpoint/main/skyline%20logical%20diagram.png)

## Requirements
| Component   | Description                    |
|------------|--------------------------------|
| **Check Point R80.40 and higher**<br>(for Security Gateways, Management Servers, Log Servers, SmartEvent Servers) | With these minimal Jumbo versions:<br><br>Jumbo Hotfix Accumulator for R81.20 - Take 8<br>Jumbo Hotfix Accumulator for R81.10 - Take 79<br>Jumbo Hotfix Accumulator for R81 - Take 77<br>Jumbo Hotfix Accumulator for R80.40 - Take 190<br><br>**Notes:**<br>• When enabled, the Skyline agent consumes approximately **50MB** of RAM.<br>• The Jumbo Hotfix Accumulator Takes mentioned above are the minimal required Takes.<br>• **Best Practice** - Use the Recommended Takes. |
| **Prometheus** | A third-party software that collects, stores, and queries metrics with a dedicated Timeseries Database.<br><br>The Prometheus Server exposes a **Remote Write** endpoint to which data can be pushed and stores the data in its local database.<br><br>Check Point supports **Prometheus version 2.37.1** and higher. |
| **Grafana** | A third-party software that connects to multiple data sources/databases (such as Prometheus) and visualizes the data, builds graphs, dashboards, and alerts.<br><br>Check Point supports **Grafana version 9** and higher. |
| **ArgoCD** | ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. It enables the management and automation of Kubernetes cluster deployments directly from a Git repository. |


## Work Steps

### Step 1: Install Prometheus Server
- Use Kubernetes to set up the Prometheus server.
- The configuration files for Prometheus are available in this repository.

### Step 2: Install Grafana Server
- Use Kubernetes to set up the Grafana server.
- The configuration files for Grafana are available in this repository.

### Step 3: Install ArgoCD 
1. Run the following command:
   ```sh
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```
2. The username is admin, the initial password can be retrieved by:
   ```sh
   kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
   ```

### Step 4: Install OpenTelemetry Agent and Collector on Check Point Server
- In Check Point R81 and higher (with the required minimum Jumbo Hotfix Accumulator), the OpenTelemetry Agent and OpenTelemetry Collector are installed as part of the Jumbo Hotfix Accumulator installation.
| **Major Version** | **Minimum JHF Take** |
|-------------------|----------------------|
| R81.20            | Take 8               |
| R81.10            | Take 79              |
| R81               | Take 77              |
| R80.40            | Take 190             |
You can find the documentation for the minimum supported versions/jumbo hotfix takes of Gaia at the main Skyline SK article [sk178566](https://support.checkpoint.com/results/sk/sk178566) under the Configuration subsection D.

### Step 5: Configure OpenTelemetry Collector to Work with Prometheus Server
1. Configure the payload (with TLS or without it). The payload file is available in this repository.
2. Save the JSON payload to a file (e.g., `/home/admin/payload.json`).
3. Run the following command on each Security Gateway / Cluster Member / Management Server:
   ```sh
   sklnctl export --set "$(cat /home/admin/payload.json)"
   ```

### Step 6: Configure the filter for the OpenTelemetry Collector exported metrics
- The sklnctl tool configures the OpenTelemetry Collector.
- The OpenTelemetry Collector filter works on the allow-list basis.
1. Show the currently exported metrics:
   ```sh
   sklnctl otelcol metrics --show > /var/log/metrics.txt
   ```
2. Edit the file to keep only the desired metrics:
   ```sh
   vi /var/log/metrics.txt
   ```
3. Add the desired metrics to the allow-list:
   ```sh
   sklnctl otelcol metrics --add $(cat /var/log/metrics.txt | tr '\n' ' ')
   ```

### Step 7: Configure Access Control Policy
- If you configured Skyline on a Security Gateway, ClusterXL, or Scalable Platform Security Group, then you must make sure your Access Control Policy allows the connection to the Prometheus Server to send the exported metrics.

# Documentation

### Prometheus and Grafana Configuration Guide
For detailed instructions on configuring Prometheus with Grafana on Check Point Servers running Gaia OS, refer to the official guide:

[Skyline Configuration on Check Point Servers: Prometheus with Grafana](https://sc1.checkpoint.com/documents/Appliances/Skyline/Content/Topics-AG/Configuration-on-Servers-Gaia-OS-Prometheus-with-Grafana.htm)

### Skyline Deployment
[Skyline Deployment](https://support.checkpoint.com/results/sk/sk178566)

