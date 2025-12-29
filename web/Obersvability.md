# Observability 



```
                           ┌────────────────────────┐
                           │        User / API      │
                           └───────────┬────────────┘
                                       │ HTTP
                                       ▼
                         ┌────────────────────────────┐
                         │   Ingress / API Gateway    │
                         └───────────┬────────────────┘
                                     │
        ┌────────────────────────────┼────────────────────────────┐
        │                            │                            │
        ▼                            ▼                            ▼
┌──────────────┐           ┌──────────────┐           ┌──────────────┐
│ REST Service │           │ REST Service │           │ REST Service │
│   (Pod)      │           │   (Pod)      │           │   (Pod)      │
│              │           │              │           │              │
│  Logs        │ stdout    │  Logs        │ stdout    │  Logs        │
│  Metrics     │ /metrics  │  Metrics     │ /metrics  │  Metrics     │
│  Traces      │ OTLP      │  Traces      │ OTLP      │  Traces      │
└───────┬──────┘           └───────┬──────┘           └───────┬──────┘
        │                          │                          │
        └──────────────┬───────────┴───────────┬──────────────┘
                       │                       │
                       ▼                       ▼
            ┌─────────────────────┐   ┌─────────────────────┐
            │ Log Agent           │   │ OTel SDK / Exporter │
            │ (DaemonSet)         │   │ (in-process)        │
            │ FluentBit/Promtail  │   └─────────┬───────────┘
            └─────────┬───────────┘             │
                      │                         ▼
                      │              ┌────────────────────────┐
                      │              │ OpenTelemetry Collector│
                      │              │ (Gateway Deployment)   │
                      │              │  - batching            │
                      │              │  - sampling            │
                      │              │  - enrichment          │
                      │              └───────┬────────────────┘
                      │                      │
        ┌─────────────▼─────────────┬────────┼───────────┐
        │                           │        │           │
        ▼                           ▼        ▼           ▼
┌──────────────┐         ┌──────────────┐  ┌──────────────┐
│ Logs Backend │         │ Metrics TSDB │  │ Traces Store │
│ Loki / ES    │         │ Prometheus   │  │ Tempo/Jaeger │
└───────┬──────┘         └───────┬──────┘  └───────┬──────┘
        │                        │                 │
        └──────────────┬─────────┴─────────┬───────┘
                       ▼                   ▼
                 ┌────────────────────────────────┐
                 │            Grafana             │
                 │  Dashboards • Logs • Traces    │
                 │  Metrics ↔ Traces ↔ Logs       │
                 └──────────────┬─────────────────┘
                                ▼
                       ┌─────────────────┐
                       │ Alertmanager    │
                       │ (SLOs, rules)   │
                       └───────┬─────────┘
                               ▼
                    Slack • PagerDuty • Email


```

## Instrumentation
```
┌──────────────────────────────────────┐
│ Application Code                     │
│                                      │
│  ┌──────── Instrumentation ───────┐  │
│  │  OpenTelemetry SDK             │  │
│  │  Logging framework             │  │
│  │  Metrics libraries             │  │
│  └──────────────┬─────────────────┘  │
│                 │ telemetry          │
└─────────────────┼────────────────────┘
                  ▼
        Collection / Export (OTel Collector)
                  ▼
        Storage (Prometheus / Loki / Tempo)
                  ▼
        Visualization & Alerting (Grafana)
```
