# Skyline Checkpoint
Skyline quickly and efficiently monitors your Check Point servers with industry-standard software and protocols (OpenTelemetry, Prometheus, and Grafana).
## The Skyline component
![Skyline component](https://raw.githubusercontent.com/ofekhalabi/Skyline-Checkpoint/main/skyline%20component.png)

## The Skyline Logical Diagram
![Skyline component](https://raw.githubusercontent.com/ofekhalabi/Skyline-Checkpoint/main/skyline%20logical%20diagram.png)

## Requirements
| Component   | Description                    | Status  |
|------------|--------------------------------|--------|
| **Check Point R80.40 and higher**<br>(for Security Gateways, Management Servers, Log Servers, SmartEvent Servers) | With these minimal Jumbo versions:<br><br>Jumbo Hotfix Accumulator for R81.20 - Take 8<br>Jumbo Hotfix Accumulator for R81.10 - Take 79<br>Jumbo Hotfix Accumulator for R81 - Take 77<br>Jumbo Hotfix Accumulator for R80.40 - Take 190<br><br>**Notes:**<br>• When enabled, the Skyline agent consumes approximately **50MB** of RAM.<br>• The Jumbo Hotfix Accumulator Takes mentioned above are the minimal required Takes.<br>• **Best Practice** - Use the Recommended Takes. | ✅ Active |
| **Prometheus** | A third-party software that collects, stores, and queries metrics with a dedicated Timeseries Database.<br><br>The Prometheus Server exposes a **Remote Write** endpoint to which data can be pushed and stores the data in its local database.<br><br>Check Point supports **Prometheus version 2.37.1** and higher. | ✅ Active |
| **Grafana** | A third-party software that connects to multiple data sources/databases (such as Prometheus) and visualizes the data, builds graphs, dashboards, and alerts.<br><br>Check Point supports **Grafana version 9** and higher. | ⚠️ Config Needed |
| **ArgoCD** | ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes. It enables the management and automation of Kubernetes cluster deployments directly from a Git repository. | ⚠️ Config Needed |

# Prometheus and Grafana Configuration Guide

For detailed instructions on configuring Prometheus with Grafana on Check Point Servers running Gaia OS, refer to the official guide:

[Skyline Configuration on Check Point Servers: Prometheus with Grafana](https://sc1.checkpoint.com/documents/Appliances/Skyline/Content/Topics-AG/Configuration-on-Servers-Gaia-OS-Prometheus-with-Grafana.htm)
