---
title: Compute Engine 永久性磁碟 CSI 驅動
tags: ["gcp","k8s","pvc","storageClass"]

---
# 介紹
GKE 在叢集中自動部署和管理 Compute Engine 永久性磁碟容器儲存介面(CSI)驅動程式，提供了一個簡單的方法

:::warning
有什麼優勢？
:::

- 自動部署和管理永久性磁碟驅動程序，不必手動進行設定
- 可以使用客戶管理的加密金鑰(CMEK)

## 創建 StorageClass
啟動 Compute Engine 永久性磁碟 CSI 驅動，GKE 會自動安裝以下 StorageClass

- standard-rwo (使用平衡永久性磁盘)
- premium-rwo (使用 SSD 永久性磁盘)

透過 provisioner 欄位中新增 `pd.csi.storage.gke.io` 來安裝其他使用 Compute Engine 永久性磁碟 CSI 驅動程式的 StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: pd-example
provisioner: pd.csi.storage.gke.io
volumeBindingMode: WaitForFirstConsumer
allowVolumeExpansion: true
parameters:
  type: pd-balanced
```

:::warning
type 有哪些類型？
:::

- pd-balanced
- pd-ssd
- pd-standard
- pd-extreme

:::warning
volumeBindingMode 如何使用？有什麼可選值？
:::

- WaitForFirstConsumer
  - Pod 引用 PVC 之前，系統不會預配 PV
- Immediate
  - 建立 PVC 之後，系統會預配 PV

## 創建 PVC
建立一個引用 Compute Engine 永久性磁碟 CSI 驅動程式的 StorageClass 的 PersistentVolumeClaim

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: podpvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: standard-rwo
  resources:
    requests:
      storage: 6Gi
```

## 創建使用該磁碟區的Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
   - name: web-server
     image: nginx
     volumeMounts:
       - mountPath: /var/lib/www/html
         name: mypvc
  volumes:
   - name: mypvc
     persistentVolumeClaim:
       claimName: podpvc
       readOnly: false
```

# 使用具有非預設檔案系統類型的 Compute Engine 永久性磁碟 CSI 驅動程式
GKE 中Compute Engine 永久性磁碟的預設檔案系統類型為 `ext4`。只要節點映像支援該類型，您也可以使用 `xfs` 儲存類型

如需查看節點映像支援的驅動程式列表，請參閱[儲存驅動程式支援](https://cloud.google.com/kubernetes-engine/docs/concepts/node-images?hl=zh-cn#storage_driver_support)

## 創建 StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: xfs-class
provisioner: pd.csi.storage.gke.io
parameters:
  type: pd-balanced
  csi.storage.k8s.io/fstype: xfs
volumeBindingMode: WaitForFirstConsumer
```



# 參考
[使用 Compute Engine 永久性磁碟 CSI 驅動程式](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/gce-pd-csi-driver?hl=zh-cn)
