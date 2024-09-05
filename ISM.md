---
title: ISM
tags: ["openSearch"]

---

# 介紹
ISM 翻譯是索引狀態管理，根據索引的期限、大小、文件數量，觸發這些定期管理的操作。

# States
索引可以處於的狀態，除了預設狀態，可以把狀態命名成 "hot"、"warm "、"delete" 等。

# Actions
索引進入特定狀態時執行的步驟。

## Retry
操作的重試配置。

- count 重試次數
- backoff 重試時退回策略類型，Exponential(指數)、Constant(常數)、Linear(線性)
- delay 重試之間等待的時間，接受分鐘、小時和天的時間單位

## Shrink
減少索引中主分片的數量，透過此操作，可以指定。

- 目標索引的主分片數量
- 目標索引的主分片的最大分片大小
- 指定縮小目標索引的主分片數量百分比

## Close
關閉託管索引，關閉後的索引保留在磁碟上，但不消耗 CPU 或 Memory。

無法讀取、寫入或搜尋關閉後的索引。

## Open
開啟託管索引。

## Delete
刪除託管索引。

## Allocation
分配索引給具有特定屬性集的節點。

# Transitions
定義狀態改變時所需要滿足的條件。

索引在目前 states 下的所有操作完成之後，policy 開始檢查轉換條件。

- state_name 滿足條件時要轉換到的狀態名稱
- conditions 例出轉換的條件
  - min_index_age 轉換所需的索引的最小年齡
  - cron 如果沒有其他轉換先發生，則觸發轉換的cron作業

# 參考
[Index State Management](https://opensearch.org/docs/latest/im-plugin/ism/index/)
[Policies](https://opensearch.org/docs/latest/im-plugin/ism/policies/)