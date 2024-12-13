---
title: ip-masq-agent
tags: [ip-masq, k8s]

---

# ip-masq-agent
ip-masq-agent 設定 iptables 規則以隱藏位於叢集節點位置後方 Pod 的 IP 位置。
通常在將流量傳送到叢集的 Pod CIDR 範圍之外的目的地時使用

- 關鍵術語：
    - NAT(網路位置轉換)
    - 偽裝
    - CIDR(無類別域間路由)
    - 本地鏈路

###### tags: `k8s` `ip-masq`