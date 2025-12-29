# Core Metrics (The 4 Golden Signals)

1. **Latency (Duration)**: Time to serve a request (avg, p95, p99).
2. **RPS Traffic**: How many requests per second (RPS).
3. **Error Rate**: Percentage of failed requests (HTTP 5xx, exceptions).
4. **Saturation (Resource Usage)**: How full the service is (CPU, Memory, Disk, Queue Depth).


Also:

- Distributed **Tracing**: Follow a requestID/correlationID across services to pinpoint latency bottlenecks (Jaeger, Zipkin).
- **Log** Aggregation & Analysis: Centralized logs for detailed debugging.
- **Alerting** & Thresholds: Set alerts for deviations in these metrics