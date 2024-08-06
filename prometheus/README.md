# Prometheus

Prometheus is an open-source monitoring and alerting toolkit designed for reliability and scalability.  
It collects and stores metrics as time series data, recording information with a timestamp. Key features include:

- **Data Collection**: Pulls metrics from configured targets at specified intervals.
- **Powerful Query Language (PromQL)**: Allows slicing and dicing of collected time series data to generate meaningful insights.
- **Alerting**: Supports defining alerts based on PromQL expressions, with flexible routing and notification options.
- **Visualization**: Integrates with Grafana for creating dashboards and visualizing metrics.
- **Service Discovery**: Dynamically discovers targets from various sources like Kubernetes, Consul, and more.
- **Exporters**: Extends monitoring capabilities to various systems and services by using exporters that translate their metrics into Prometheus format.

Prometheus is widely used in cloud-native environments and is part of the Cloud Native Computing Foundation (CNCF).

## [Metric Types](metric-types.md)
### [Gauges](metric-types.md#gauges)
### [Counters](metric-types.md#counters)
### [Summaries](metric-types.md#summaries)
### [Histograms](metric-types.md#histograms)
