# Prometheus Metric Types

<!-- TOC depthFrom:2 -->
- [Gauges](#gauges)
- [Counters](#counters)
- [Summaries](#summaries)
- [Histograms](#histograms)
<!-- /TOC -->

<a name="gauges"></a>
## Gauges

A **Gauge** represents a single numerical value that **can arbitrarily go up and down**. Gauges are used to measure values that can fluctuate over time, such as:

- Current memory usage
- Queue length
- Disk space
- Temperature readings
- Number of active sessions

Gauges are ideal for metrics that need to track the current state or value of something, allowing for both increases and decreases.

![](img/gauges.png "Graph with Prometheus Metric Gauge ")

### Gauge Instrumentation Methods

#### `set()`  
Sets the Gauge to any arbitrary value.
```java
queueLength.set(0);
```

#### `inc()` and `dec()`
Increases or decreases the current value.
```java
queueLength.inc(); // Increment by 1
queueLength.dec(); // Decrement by 1

queueLength.inc(42); // Increment by 42
queueLength.dec(12); // Decrement by 12
```

#### `setToCurrentTime()`
Sets the Gauge to the current time, useful for tracking events like boot time, process start time, or the last run of a job.
```java
aTimestamp.setToCurrentTime();
```

#### Summary

- **`set()`**: Directly sets the gauge to a specific value.
- **`inc()` and `dec()`**: Increment or decrement the gauge by 1 or a specified value.
- **`setToCurrentTime()`**: Sets the gauge to the current time, ideal for monitoring the timing of specific events. 


### Gauge Exposition

A Gauge will be displayed as a single time series like:

```promql
# HELP disk_free_bytes Usable space for path
# TYPE disk_free_bytes gauge
disk_free_bytes 1.0634604544E11
```

### Querying Gauges

#### Current Value
To get the current value of the `queue_length` gauge, use:
```promql
queue_length
```

#### Querying Over Time
To see how the value of the gauge has changed over the past 5 minutes, use:
```promql
queue_length[5m]
```

#### Applying Functions

To get the average value of the gauge over the past 5 minutes, use:
```promql
avg_over_time(queue_length[5m])
```

To determine how long ago an event occurred using a timestamp gauge, use:
```promql
time() - process_start_time_seconds
```

These queries help monitor and analyze gauge metrics effectively in Prometheus.

<a name="counters"></a>
## Counters

A **Counter** is a cumulative metric that represents a single **numerical value that only ever goes up**. It is used to measure values that only increase over time, such as:

- The number of requests served
- The number of errors encountered
- The number of tasks completed

Counters are ideal for metrics that **represent discrete events and are reset to zero only when the application restarts**. They are not suitable for values that can decrease, such as memory usage or the number of active sessions.

![](img/counters.png "Graph with Prometheus Metric Counter ")

![](img/counter-reset.png "Graph with resets on Prometheus Metric Counter ")

![](img/counter-absolute-values.png "Graph with Prometheus Metric Counter absolute values ")

### Counter Instrumentation Methods

Counter metrics have only two methods to update their values:

#### `inc()`
Increments the counter by 1.
```java
totalRequests.inc(); // Increment by 1
```

#### `add()`
Increments the counter by an arbitrary value.
```java
totalRequests.add(23); // Increment by 23
```

These methods ensure that the counter only increases, making it ideal for tracking cumulative metrics such as the number of requests or errors.


### Counter Exposition

```promql
# HELP http_requests_total The total number of handled HTTP requests.
# TYPE http_requests_total counter
http_requests_total 666
```

When using counters, **focus on the rate at which the counter increases over time** rather than its absolute value.  
This helps determine trends such as the number of requests per second.

#### Counter Rate of Increase

- **`rate()`**: Calculates the per-second average rate of increase over a specified time window.
- **`irate()`**: Computes the per-second instantaneous rate of increase over the last two data points.
- **`increase()`**: Calculates the increase in the counter's value over a specified time window.

These functions are useful for analyzing the rate of change and identifying performance trends.

<a name="summaries"></a>
## Summaries

A **Summary** captures individual observations and provides a total count of observations and a sum of all observed values.  
Summaries can also calculate configurable quantiles over a sliding time window. **They are useful for measuring things like request durations or response sizes**.

#### Key Features:
- **Quantiles**: Calculates specific quantiles (e.g., 95th percentile) over a sliding time window.
- **Total Count**: Keeps a running count of observations.
- **Sum**: Keeps a running total of all observed values.

Summaries are suitable for tracking metrics where you need detailed statistical information about the distribution of the observed values.

![](img/summaries.png "Graph with Prometheus Metric Summary ")

### Summary Instrumentation Methods

#### `observeDuration()`
Tracking the latency of HTTP requests.

```java
Summary requestLatency = Summary.build()
        .name("http_request_latency_seconds")
        .help("Request latency in seconds.")
        .register();

Summary.Timer requestTimer = requestLatency.startTimer();

// simulate the request
simulateCall();

requestTimer.observeDuration();
```

### Summary Exposition

```promql
# HELP http_server_requests_seconds Duration of HTTP server request handling
# TYPE http_server_requests_seconds summary
http_server_requests_seconds{quantile="0.5"} 9.8304E-4
http_server_requests_seconds{quantile="0.85"} 0.001212416
http_server_requests_seconds{quantile="0.95"} 0.001212416
http_server_requests_seconds{quantile="0.99"} 0.001212416
http_server_requests_seconds_count 3020.0
http_server_requests_seconds_sum 4.124702425
```

The output of a Summary is a collection of Gauge and Counter metrics. However, it's important not to try averaging or aggregating percentiles from multiple service instances or other label dimensions, as there is no statistically valid way to average percentiles.

If you need to aggregate data, consider using a Histogram metric instead.

<a name="histograms"></a>
## Histograms

A **Histogram** samples observations and counts them in configurable buckets. It is useful for measuring the distribution of values, such as request durations or response sizes.

#### Key Features:
- **Buckets**: Predefined ranges that group observations.
- **Count**: Total number of observations.
- **Sum**: Sum of all observed values.
- **Quantiles**: Can approximate quantiles from bucket data.

Histograms are ideal for monitoring the distribution of events over time and understanding the range and frequency of observed values. They provide more detailed insight into the data compared to counters and gauges, especially for latency or size measurements.

![](img/histograms.png "Graph with Prometheus Metric Histograms ")

![](img/histograms-cumulative.png "Graph with Prometheus Metric Cumulative Histograms ")


### Histogram Instrumentation Methods

#### `observeDuration()`
Track the latency of HTTP requests.

```java
Histogram requestLatency = Histogram.build()
    .name("http_request_latency_seconds")
    .help("Request latency in seconds.")
    .register();

Histogram.Timer requestTimer = requestLatency.startTimer();

// simulate the request
simulateCall();

requestTimer.observeDuration();
```

### Histogram Exposition

```promql
# HELP lettuce_command_firstresponse_seconds Latency between command send and first response (first response received)
# TYPE lettuce_command_firstresponse_seconds histogram
lettuce_command_firstresponse_seconds_bucket{le="0.05"} 170741
lettuce_command_firstresponse_seconds_bucket{le="0.1"} 170757
lettuce_command_firstresponse_seconds_bucket{le="0.25"} 170890
lettuce_command_firstresponse_seconds_bucket{le="0.5"} 170961
lettuce_command_firstresponse_seconds_bucket{le="1"} 171019
lettuce_command_firstresponse_seconds_bucket{le="2.5"} 171061
lettuce_command_firstresponse_seconds_bucket{le="5"} 171109
lettuce_command_firstresponse_seconds_bucket{le="+Inf"} 201109
lettuce_command_firstresponse_seconds_count 171458
lettuce_command_firstresponse_seconds_sum 28.27548449
```

#### Quantiles from Histograms

90th Percentile latency, averaged over the last 5 minutes:

```promql
histogram_quantile(
  0.9,
  rate(http_request_duration_seconds_bucket[5m])
)
```

#### Aggregated Histogram Quantiles
90th Percentile latency for each path/method combination, averaged over the last 5 minutes:

```promql
histogram_quantile(
  0.9,
  sum by (path, method, le) (
    rate(http_request_duration_seconds_bucket[5m])
  )
)
```

#### Average Latencies
Average request duration over the last 5 minutes:

```promql
rate(http_request_duration_seconds_sum[5m])
/
rate(http_request_duration_seconds_count[5m])
```

Aggregated per-path/method average request duration:

```promql
sum by (path, method) (rate(http_request_duration_seconds_sum[5m]))
/
sum by (path, method) (rate(http_request_duration_seconds_count[5m]))
```