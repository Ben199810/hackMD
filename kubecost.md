---
title: kubecost
tags: [k8s]

---

# kubecost

kubecost 是一個用於==分析 GKE 群集成本==的工具

1. 使用 kubecost 提供的 Helm Chart 安裝 kubecost
```
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
```
```
helm install kubecost kubecost/cost-analyzer -- namespace kubecost -- set kubecostToken=”YmhhdmlzaHlhZ3VwdGE1MUBnbWFpbC5jb20=xm343yadf98”
```

###### tags: `k8s`