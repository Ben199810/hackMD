---
title: GMP
tags: [gcp]

---

# GMP
1. gmp 是 google 代管的 promethues 服務收集監控指標儲存在 Monarch 中

2. 代管的 prometheus 使用 `deamonset` 運行

3. promethues 是透過==拉取==的方式收集 metrics，不是透過推送

4. 指標收集需要透過 PodMonitoring 或 ClusterPodMonitoring 才能正確抓取指標端點

gmp 會建立以下組件：
- gmp-operator

:::info
Deployment，用於為 Managed Service for Prometheus 部署 Kubernetes Operator
:::

- rule-evaluator

:::info
Deployment，用於配置並執行提醒和記錄規則
:::

- collector

:::info
DaemonSet，用於從與收集器所在節點上運行的 Pod 爬取指標來橫向擴縮收集
:::

- alertmanager

:::info
StatefulSet，配置為將觸發的提醒傳送至首選通知頻道
:::

## 建立 PodMonitoring
:::warning
PodMonitoring 只能抓取同一個 namespace 的目標。
:::

Yaml 範例
```yaml=
apiVersion: monitoring.googleapis.com/v1
kind: PodMonitoring
metadata:
  name: prom-example
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: prom-example
  endpoints:
  - port: metrics
    interval: 30s
```

## 代管式收集的其他設定
- 啟用目標狀態功能以方便調整
- 過濾您匯出到代管式服務的資料

### 啟用目標狀態
您可以透過在 OperatorConfig 資源中將 `features.targetStatus.enabled` 值設為 true 來檢查 PodMonitoring 或 ClusterPodMonitoring 資源中的目標狀態

```yaml=
apiVersion: monitoring.googleapis.com/v1
kind: OperatorConfig
metadata:
  namespace: gmp-public
  name: config
features:
  targetStatus:
    enabled: true
```

配置完成後檢查 PodMonitoring

```yaml=
kubectl -n NAMESPACE_NAME describe podmonitorings/SOURCENAME
```

輸出範例

```yaml=
API Version:  monitoring.googleapis.com/v1
Kind:         PodMonitoring
...
Status:
  Conditions:
    ...
    Status:                True
    Type:                  ConfigurationCreateSuccess
  Endpoint Statuses:
    Active Targets:       3
    Collectors Fraction:  1
    Last Update Time:     2023-08-02T12:24:26Z
    Name:                 PodMonitoring/custom/prom-example/metrics
    Sample Groups:
      Count:  3
      Sample Targets:
        Health:  up
        Labels:
          Cluster:                     CLUSTER_NAME
          Container:                   prom-example
          Instance:                    prom-example-589ddf7f7f-hcnpt:metrics
          Job:                         prom-example
          Location:                    REGION
          Namespace:                   NAMESPACE_NAME
          Pod:                         prom-example-589ddf7f7f-hcnpt
          project_id:                  PROJECT_ID
        Last Scrape Duration Seconds:  0.020206416
        Health:                        up
        Labels:
          ...
        Last Scrape Duration Seconds:  0.054189485
        Health:                        up
        Labels:
          ...
        Last Scape Duration Seconds:  0.006224887
```

### 過濾導出的指標

如果您收集大量數據，則可能需要防止將一些時序發送到 Managed Service for Prometheus，以降低費用。為此，您可以使用包含 keep 操作的 Prometheus 重新新增標籤規則作為許可名單，或使用包含 drop 操作的 Prometheus 重新新增標籤規則作為拒絕名單。對於代管式收集，此規則位於 `PodMonitoring` 或 `ClusterPodMonitoring` 資源的 metricRelabeling 部分。

例如，以下指標重新新增標籤規則將過濾掉以 foo_bar_、foo_baz_ 或 foo_qux_ 開頭的所有指標：

```yaml=
metricRelabeling:
- action: drop
  regex: foo_(bar|baz|qux)_.+
  sourceLabels: [__name__]
```


## 參考文件
[代管式收集使用入門](https://cloud.google.com/stackdriver/docs/managed-prometheus/setup-managed?hl=zh-cn)


###### tags: `gcp`