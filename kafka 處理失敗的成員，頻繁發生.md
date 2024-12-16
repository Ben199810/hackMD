---
title: kafka 處理失敗的成員，頻繁發生
tags: [kafka, probalm]

---

# 前言
RD 同仁反映 golang 出現以下錯誤訊息。

:::danger
read tcp 10.164.4.112:56960->10.164.3.176:9092: i/o timeout
:::

經過盤查 10.164.3.176 是 kafka 的 controller，接著查詢 kafka 的 log。

:::info
[2024-12-12 08:38:21,189] INFO [GroupCoordinator 1]: Member watermill-9cb21d68-e225-4121-8d00-4ba361282180 in group pi-schedule-betlog-fetching-listener has failed, removing it from the group (kafka.coordinator.group.GroupCoordinator)
:::

發現 kafka 頻繁的將 Consumer 移出 Group。可能導致前面 log 所提到的 timeout 問題。

:::warning
如果消費者處理邏輯耗時過長，可能導致無法及時發送 heartBeat
:::

:::success
增加消費者會話超時時間
```yaml
session.timeout.ms=30000
```
確保 heartBeat 間隔合理
```yaml
heartbeat.interval.ms=10000

```
:::

# 參考
[Understanding Apache Kafka
](https://www.redpanda.com/guides/kafka-tutorial-kafka-consumer-config)