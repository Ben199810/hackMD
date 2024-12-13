---
title: Kafka 介紹
tags: [kafka]

---

# Kafka 介紹
:::info
kafka 是為了解決 data pipeline 的問題
:::

kafka 是一種分散式的 `streaming` 平台，提供以下功能：

- message system：提供發布與訂閱資料串接資料流
- streaming processing：kafka 提供 streaming API 直接在 kafka 上撰寫 streaming 資料處理

kafka 主要有幾個核心名詞：

1. Topics
在 kafka 中 topic 就像是 database 中的 table，為不同的資料的類別名稱

2. Partitions
Topic 內切分為數個 Partitions，在 Kafka 中每個最小單位的訊息 (message) 或記錄 (record) 以序列 append 的方式存放在在每個 Partition 中，Partition 可以分散式的存放在不同機器中，以防止單台機器故障

3. Produces and Consumers
Producers 與 Consumers 分別對應 publish/subscribe 系統的概念，Producers 可以新增資料到 topic，Consumers 負責從資料讀取 topic

4. Brokers and Clusters
    - 一個單台 Kafka server 稱為 broker，每個 broker 接收來自 producers 的訊並寫入至磁碟中，同時回應來自 Consumers 的資料請求
    - 多個 Brokers 連接在一起稱之為 Cluster，Cluster 中存在一個 controller （動態建立），負責分配 partitions 與監控 brokers 狀態

5. Retention
Retention 是指 Kafka 可以設定的存放在磁碟中的一段時間，預設是 7 天或是資料量大於 1 GB 就會自動刪除一些資料。Kafka 可以針對不同 topic 設定不同 Retention

6. Multiple Clusters
Kafka 支援 Multiple Clusters，主要是為了可以提高可用性與安全性，如建立cluster 於不同的資料中心，並提供了稱之為 MirrorMaker 的工具讓你輕易在 clusters 之間複製資料

## 安裝與設定
kafka 本身並沒有特別強制的硬體要求，但以下指標主要會影響其效能：
1. Disk Throughput
因為 Kafka 是將資料寫入磁碟，故其效能主要影響是 disk 的 IO，所以使用 SSD 將可以大幅提升 throughput 與 latency

2. Disk Capacity
根據希望最少資料保留時間，會需要預留一定的磁碟空間大小，如果你預期每天會產生 1 GB 的數據，且需要保存 7 天，那就需要最少 7 GB 的 free space

3. Memory
在 Kafka 中記憶體主要影響 consumer client 的效率，因為 broker 會從 disk 中讀取並 cache 在記憶體中優化 consumer 讀取

4. Networking
網路的 throughput 會決定整個 kafka throughput 上限

5. CPU
CPU 相較其他指標較為不那麼重要，主要只用於解壓縮的任務

## 參考文件
[Apache Kafka 介紹](https://medium.com/@chihsuan/introduction-to-apache-kafka-1cae693aa85e)

###### tags: `kafka`