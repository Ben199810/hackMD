---
title: Cloud Storage FUSE CSI 驅動程式存取 Cloud Storage
tags: ["gcp","k8s","gcs"]
---

# 介紹
透過 Cloud Storage FUSE，您可以將 Cloud Storage 儲存桶作為檔案系統裝載，以便應用程式可以使用常用的檔案IO 操作（例如開啟、讀取、寫入、關閉）存取儲存桶內的對象，而不是使用雲端專用API。

原生支援使用下列方法來設定Cloud Storage 支援的磁碟區：

- CSI 暫存磁碟區：您可以依照 Pod 規格指定Cloud Storage 儲存桶
- 靜態預配：您將建立引用 Cloud Storage 儲存桶的 PV 資源。然後，您的 Pod 可以引用綁定到此 PV 的 PVC

:::warning
有什麼優勢？
:::

- Cloud Storage FUSE CSI 驅動程式會啟用該驅動程式的自動部署和管理
- Cloud Storage FUSE CSI 驅動程式不需要 FUSE 用戶端通常所需的特權存取權限
- 支援 `ReadWriteMany`、`ReadOnlyMany` 和 `ReadWriteOnce`
- 可以使用適用於 GKE 的 Workload Identity 管理身份驗證，同時精細控制 Pod 存取 Cloud Storage 物件的方式
- 在啟用檔案快取的情況下讀取 Cloud Storage 對象，以提高讀取效能
- 可以在 init container 中使用 Cloud Storage FUSE 磁碟區

:::success
前置作業
:::

- 運行 GKE 1.24 版或更高版本的 Linux 叢集
- 在 node pool 上啟用 GKE metaData 伺服器。
- 啟用 GKE 的 Workload Identity

## 啟用 Cloud Storage FUSE CSI 驅動程式

```shell
gcloud container clusters create CLUSTER_NAME \
    --addons GcsFuseCsiDriver \
    --cluster-version=VERSION \
    --location=LOCATION \
    --workload-pool=PROJECT_ID.svc.id.goog
```

## 準備裝載 Cloud Storage FUSE 儲存桶
CSI 驅動程式依賴 Pod annotations 來決定 Pod 是否使用受 Cloud Storage 支援的磁碟區，如果驅動程式偵測到 annotations。會將名為 gke-gcsfuse-sidecar 的 sidecar container 注入工作負載 Pod。

:::warning
預設的情況下，sidecar 會配置多少資源？
:::

- 250m CPU
- 256 MiB Memory
- 5 GiB ephemeral-storage

如果要覆寫設定，可以指定 annotations

```yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    gke-gcsfuse/volumes: "true"
    gke-gcsfuse/cpu-limit: "10"
    gke-gcsfuse/memory-limit: 10Gi
    gke-gcsfuse/ephemeral-storage-limit: 1Ti
    gke-gcsfuse/cpu-request: 500m
    gke-gcsfuse/memory-request: 1Gi
    gke-gcsfuse/ephemeral-storage-request: 50Gi
```

:::success
gke-gcsfuse/volumes: "true"
:::

# 創建 CSI 暫存 volumes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gcs-fuse-csi-example-ephemeral
  namespace: NAMESPACE
  annotations:
    gke-gcsfuse/volumes: "true"
spec:
  terminationGracePeriodSeconds: 60
  containers:
  - image: busybox
    name: busybox
    command: ["sleep"]
    args: ["infinity"]
    volumeMounts:
    - name: gcs-fuse-csi-ephemeral
      mountPath: /data
      readOnly: true
  serviceAccountName: KSA_NAME
  volumes:
  - name: gcs-fuse-csi-ephemeral
    csi:
      driver: gcsfuse.csi.storage.gke.io
      readOnly: true
      volumeAttributes:
        bucketName: BUCKET_NAME
        mountOptions: "implicit-dirs"
        gcsfuseLoggingSeverity: warning
```

# 使用靜態配置來創建 volumes

:::success
展示如何為 Cloud Storage 儲存桶定義 PersistentVolume
:::

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: gcs-fuse-csi-pv
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 5Gi
  storageClassName: example-storage-class
  claimRef:
    namespace: NAMESPACE
    name: gcs-fuse-csi-static-pvc
  mountOptions:
    - implicit-dirs
  csi:
    driver: gcsfuse.csi.storage.gke.io
    volumeHandle: BUCKET_NAME
    volumeAttributes:
      gcsfuseLoggingSeverity: warning
```

# 參考
[使用Cloud Storage FUSE CSI 驅動程式存取Cloud Storage 儲存桶](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/cloud-storage-fuse-csi-driver?hl=zh-cn)