---
title: AWS 每個網路介面的 IP 上限
tags: [aws, eks, network]

---

# 使用 describe-instance-types 顯示執行個體類型相關資訊

:::warning
不支援 IPv6 網址機器
- C1
- M1
- M2
- M3
- T1
:::

```shell
aws ec2 describe-instance-types \
    --filters "Name=instance-type,Values=c5.*" \
    --query "InstanceTypes[].{ \
        Type: InstanceType, \
        MaxENI: NetworkInfo.MaximumNetworkInterfaces, \
        IPv4addr: NetworkInfo.Ipv4AddressesPerInterface}" \
    --output table
```

如果要查詢的機器類型是 `t3` 👉 `--filters Name=instance-type,Values=t3.*`

最大 Pod 數量計算公式 👇

:::success
關鍵概念
----
- **ENI（Elastic Network Interface）:**
AWS 實例可以附加多個 ENI。第一個 ENI（主要網卡）自帶一個主 IP 地址，這個地址會用來與實例自身溝通，所以無法分配給 Pod。

- **私有 IP 地址數量**
每個 ENI 都有最大可分配的私有 IP 數量，這個數量取決於該 EC2 實例的類型。對於額外分配的 ENI，其所有 IP 都可用於 Pod 分配。
:::

假設：
- M = 每個 ENI 最大可分配的私有 IP 數量（包含主要 IP
- N = 可附加的 ENI 總數量

:::info
(N-1) * M + (M-1)
:::

# 參考

[每個網路介面的 IP 位址上限](https://docs.aws.amazon.com/zh_tw/AWSEC2/latest/UserGuide/AvailableIpPerENI.html)