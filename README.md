# Skyline Checkpoint
Skyline quickly and efficiently monitors your Check Point servers with industry-standard software and protocols (OpenTelemetry, Prometheus, and Grafana).
## The Skyline component
![Skyline component](https://raw.githubusercontent.com/ofekhalabi/Skyline-Checkpoint/main/skyline%20component.png)

## The Skyline Logical Diagram
![Skyline component](https://raw.githubusercontent.com/ofekhalabi/Skyline-Checkpoint/main/skyline%20logical%20diagram.png)

## Requirements
| Component   | Description                    | Status  |
|------------|--------------------------------|--------|
| Check Point R80.40 and higher
(for Security Gateways, Management Servers, Log Servers, SmartEvent Servers)    | With these minimal Jumbo versions:

Jumbo Hotfix Accumulator for R81.20 - Take 8
Jumbo Hotfix Accumulator for R81.10 - Take 79
Jumbo Hotfix Accumulator for R81 - Take 77
Jumbo Hotfix Accumulator for R80.40 - Take 190
Notes:

When enabled, the Skyline agent consumes approximately 50MB of RAM.

The Jumbo Hotfix Accumulator Takes mentioned above are the minimal required Takes.

Best Practice - Use the Recommended Takes. | ✅ Active |
| Prometheus | A third-party software that collects, stores, and queries metrics with a dedicated Timeseries Database.

The Prometheus Server exposes a Remote Write endpoint to which data can be pushed and stores the data in its local database.

Check Point supports Prometheus version 2.37.1 and higher.           | ✅ Active |
| Grafana    | A third-party software that connects to multiple data sources/databases (such as Prometheus) and visualizes the data, builds graphs, dashboards, and alerts.

Check Point supports Grafana version 9 and higher.        | ⚠️ Config Needed |
