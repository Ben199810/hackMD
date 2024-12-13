---
title: ServiceMonitor 與 Promethues
tags: [k8s]

---

# ServiceMonitor 與 Promethues

## 架構圖

```mermaid
flowchart LR

服務(container/pod)
Monitor(ServiceMonitor)
監控(promethues)
告警(Alertmanager)
service(svc)
grafana(grafana)

服務 -.export metrics.-> Monitor --> 監控 --> 告警
Monitor --setting--> service --> 服務
監控 --> grafana
```

###### tags: `k8s`