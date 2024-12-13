---
title: Prometheus 指標說明
tags: [prometheus]

---

# Prometheus 指標說明
**常用指標**

- 記憶體相關

|          指標名稱          | 類型 |           用途與說明           | 單位  |
|:--------------------------:|:----:|:------------------------------:|:-----:|
|                            |      |                                |       |
|    container_memory_rss    |  量  |           RSS 的大小           | bytes |
| container_oom_events_total | 計次 | 觀察到的容器記憶體不足事件計數 |       |

- 網路封包相關

|                     指標名稱                     | 類型 |          用途與說明          | 單位 |
|:------------------------------------------------:|:----:|:----------------------------:|:----:|
| container_network_receive_packets_dropped_total  | 計次 | 接收時丟棄的資料包的累積數量 |      |
| container_network_transmit_packets_dropped_total | 計次 | 傳輸時丟棄的資料包的累積計數 |      |

- Pod 狀態相關



| 指標名稱              | 類型 |         用途與說明          | 單位 |
| --------------------- | ---- |:---------------------------:| ---- |
| kube_pod_status_ready | 量   | 描述 Pod 是否準備好處理請求 |      |
| kube_pod_status_phase | 量   |        Pod 當前階段         |      |




## 參考文件
[pormetheus.md](https://github.com/google/cadvisor/blob/master/docs/storage/prometheus.md)

###### tags: `prometheus`