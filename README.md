# Skyline-Checkpoint
Skyline quickly and efficiently monitors your Check Point servers with industry-standard software and protocols (OpenTelemetry, Prometheus, and Grafana).

# Simple Queue Service (SQS) and Simple Notification Service (SNS)

## Motivation for queueing systems

As you mat know, whenever a user opens the details window for a specific movie, the HTTP request is sent to the NetflixFrontend service with the activity information. This activity, let's call it **MovieDetailsViewedEvent** is stored as an object in S3, to be used in the future. 
