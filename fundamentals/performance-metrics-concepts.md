# Fundamental Performance Metrics Concepts

## Key Metrics
- **Latency**  
  The time taken to complete a single request or operation (e.g., from initiation to response completion, typically measured in milliseconds).
- **Throughput**  
  The number of operations completed per unit of time (e.g., requests/sec, transactions/sec), often averaged over a specific period.
- **P95 / P99**  
  Percentile metrics representing the latency experienced by 5% (P95) or 1% (P99) of the slowest requests, indicating worst-case performance for a subset of users.
- **Tail Latency**  
  The latency of the slowest requests (e.g., 99th percentile), critical for real-world user experience as it affects the slowest users.

## System Startup
- **Cold Start**  
  The initial delay when a system (e.g., serverless functions like AWS Lambda) starts from an uninitialized state, including resource provisioning.
- **Warm Start**  
  The reduced latency when reusing a previously initialized service or instance, minimizing startup overhead.
- **TTFB (Time to First Byte)**  
  The time from sending a request to receiving the first byte of the response, often used to assess server responsiveness.

## Throughput & Load
- **RPS (Requests Per Second)**  
  A common measure of server or API throughput, representing the number of requests processed per second.
- **QPS (Queries Per Second)**  
  Similar to RPS, but specifically used for databases and search systems, measuring query processing rate.
- **Error Rate**  
  The percentage of requests resulting in errors (e.g., HTTP 4xx or 5xx status codes), typically calculated over a time window.
- **Apdex Score**  
  A user satisfaction score (0 to 1) based on the proportion of requests classified as satisfactory, tolerable, or frustrating, using defined latency thresholds.

## Service Agreements
- **SLA (Service Level Agreement)**  
  A formal commitment by a provider on uptime (e.g., 99.9%) or performance levels, enforceable via contract.
- **SLO (Service Level Objective)**  
  An internal performance target (e.g., 99.95% request success rate) set to meet or exceed the SLA.
- **SLI (Service Level Indicator)**  
  A measurable value (e.g., "99.95% of requests under 500ms") used to track adherence to the SLO.

## Resource & Performance
- **Resource Utilization**  
  Measures the usage of CPU, memory, disk, and network resources under load, often expressed as a percentage of capacity.
- **GC Pause Time**  
  The duration an application is paused for garbage collection (relevant in JVM, Go, etc.), impacting latency and throughput.
- **Throughput vs Latency Tradeoff**  
  The balance where increasing throughput may reduce latency up to a saturation point, beyond which latency increases.

## User Experience
- **Jank**  
  Visual stuttering or lag in frontend rendering, often caused by long tasks, reflows, or inefficient rendering pipelines.
- **CPU Throttling**  
  The intentional slowing of CPU-intensive applications by limits in containers, cloud settings, or hardware thermal controls.
- **I/O Wait Time**  
  The time the CPU spends idle waiting for I/O operations (disk or network) to complete, affecting overall system efficiency.
- **TTI (Time to Interactive)**  
  The time from page load initiation until the page is fully interactive, a key web performance metric.
- **CLS (Cumulative Layout Shift)**  
  A metric quantifying unexpected layout shifts during page load, scored from 0 to 1, with higher values indicating worse UX.
- **FPS (Frames Per Second)**  
  The rate at which frames are rendered, a key metric for visual performance in games and frontend animations (typically 30-60 FPS for smooth experience).

## System Efficiency
- **Memory Footprint**  
  The total memory consumed by a process or system under typical or peak load, including heap and stack usage.
- **Throttling & Backpressure**  
  Mechanisms to regulate client or system load (e.g., rate limiting, queue management) to maintain stability under high demand.