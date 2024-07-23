---
title: 永久性磁碟與 PV 耦合
tags: ["gcp","k8s","pv"]
---
# 介紹
有兩種常見場景使用現有的永久性磁碟

- 手動建立 PVC 和 PV，將其綁定在一起，並在pod 規範中引用 PVC
- 使用 StatefulSet 自動產生 PVC，這些綁定會綁定到與一系列現有永久性磁碟相對應的手動產生的 PV

## 創建 PV 與 PVC

:::info
注意事項
:::

- 將 PV 綁定到 PVC，兩個資源的 `storageClassName` 以及 `capacity`、`accessModes` 和 `volumeMode` 必須相符
- 可以省略 storageClassName，但必須指定 "" 以防止 Kubernetes 使用預設的 StorageClass

:::danger
ReadWriteMany mode 由 Compute Engine 永久性磁碟支援的 PV 資源不支援此存取模式
:::

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: $PV_NAME
spec:
  storageClassName: "$STORAGE_CLASS_NAME"
  capacity:
    storage: $DISK_SIZE
  accessModes:
    - ReadWriteOnce
  claimRef:
    namespace: default
    name: $PV_CLAIM_NAME
  csi:
    driver: pd.csi.storage.gke.io
    volumeHandle: $DISK_ID
    fsType: $FS_TYPE
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: default
  name: $PV_CLAIM_NAME
spec:
  storageClassName: "$STORAGE_CLASS_NAME"
  volumeName: $PV_NAME
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: $DISK_SIZE
```

請替換以下內容：

- PV_NAME：PV 的名稱
- STORAGE_CLASS_NAME：StorageClass 的名稱
- DISK_SIZE：您的原有永久性磁碟的大小
- PV_CLAIM_NAME：PVC 的名稱
- DISK_ID：現有永久性磁碟的標識符
- FS_TYPE：檔案系統類型

:::info
DISK_ID 標識符的格式
:::

- projects/{project_id}/zones/{zone_name}/disks/{disk_name}
- projects/{project_id}/regions/{region_name}/disks/{disk_name}


# 參考
[將原有永久性磁碟用作 PersistentVolume](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/preexisting-pd?hl=zh-cn)
[永久性捲和動態預配](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes?hl=zh-cn)
[磁碟定價](https://cloud.google.com/compute/all-pricing?hl=zh_TW&_gl=1*1p485yg*_ga*OTg2MTg4NDc1LjE2NzI3MTgyNDk.*_ga_WH2QY8WWF5*MTcyMTY5NDI0MC42MTEuMS4xNzIxNzAyODUyLjU4LjAuMA..#disk)