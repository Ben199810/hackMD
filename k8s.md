---
title: k8s
tags: [k8s]

---

# k8s

## k8s 地圖

```markmap
- k8s
  - 部署
    - HPA
    - Deployment
      - matchLabel
      - rollingUpdate
      - Replicaset
        - annotation
        - Label
        - pod
          - container
            - port
            - image
          - volume
            - emptyDir
            - hostPath
            - subPath
            - pvc & pv
    - Statefulset
    - Daemonset
  - 網路
    - Ingresses
      - ingresses-controller
    - Service 
      - port
      - targetPort
      - loadbalance
```

<br>

---

## Volume 的種類及介紹

[Kubernetes Volume Overview](https://godleon.github.io/blog/Kubernetes/k8s-Volume-Overview/)

### emptyDir

emptyDir 有以下幾點特性：

* 會在 pod 被分配到特定的 node 後產生

* 生命週期是跟著 pod + node 一起走，只要該 pod 持續的在原本的 node 上運行，就會保留著

* 每個在 pod 中的 container，可以將 emptyDir 掛載在不同的路徑

* 目前無法限制使用量

<br>

### hostPath

若是希望 volume 新增後裏面存放一些 node 上已經存在的資料，可以使用 hostPath。

<br>

### subPath

有時候我們要掛載的目錄中可能包含了多個子目錄，而這些子目錄恰巧又分別被多個不同的 container 使用，此時就可以透過 subPath 的方式來簡化 volume 的設定

```
apiVersion: v1
kind: Pod
metadata:
  name: my-lamp-site
spec:
    containers:
    - name: mysql
      image: mysql
      env:
      - name: MYSQL_ROOT_PASSWORD
        value: "rootpasswd"
      # 掛載 volume 中的 "mysql" subPath
      volumeMounts:
      - mountPath: /var/lib/mysql
        name: site-data
        subPath: mysql
    - name: php
      image: php:7.0-apache
      # 掛載 volume 中的 "html" subPath
      volumeMounts:
      - mountPath: /var/www/html
        name: site-data
        subPath: html
        
    # 包含多個目錄(mysql & html)的 volume 設定
    volumes:
    - name: site-data
      persistentVolumeClaim:
        claimName: my-lamp-site-data
```

<br>

### PV & PVC

k8s 使用 vloumes 掛載文件的方式，缺點是必須寫出具體的儲存位置，才能夠做耦合，如果要遷移就要修改文件。這部分我們就可以使用 PV/PVC 進行解耦，意味著不必強制的將儲存位置綁死。

當建立 Pod 服務使用到 PVC，這時會自動找一個符合的 PV 進行批配，若有批配到就直接綁定(「靜態」批配)，如果沒有符合的 PV，則會透過 StorageClass 建立一個新的 PV 再和 PVC 綁定(「動態」批配)。

<br>

![](https://i.imgur.com/6tKbkAc.png)

<br>

1.解耦 讓 Volume 跟具體儲存位置解耦提供靈活性與可移植性

2.職責分離 Volume 和 PVC 由開發人員定義， 提出存儲需求就好

實作範例:

#### pv.yml

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
spec:
  storageClassName: stardard
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/tmp/data02"
    type: "DirectoryOrCreate"
```

#### pvc.yml

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 250Mi
  storageClassName: stardard
```

#### deployment.yml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  minReadySeconds: 10 #表示Port起來要等10秒 有延遲方便查看效果
  selector:
    matchLabels:
      app: mysql #標籤要和template label裡面一樣
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: petclinic
            - name: MYSQL_DATABASE
              value: petclinic
          volumeMounts:
            - name: mysql-persistent-volume #要與volumes 裡面name一樣
              mountPath: /var/lib/mysql
      volumes: 
        - name: mysql-persistent-volume
          persistentVolumeClaim:
            claimName: mysql-pvc
              
--- 
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
    - name: tcp
      port: 3306
      targetPort: 3306
  selector:
    app: mysql
  type: ClusterIP
```

<br>

---

## Pod 水平自動擴縮

在 k8s 裡面 HPA 稱為自動更新工作負載資源，自動擴縮不適用於無法擴縮的對象 ex.DaemonSet

圖解 HorizontalPodAutoscaler 是如何工作的:

<br>

![](https://i.imgur.com/IzcnuIs.png "簡圖")

<br>

![](https://i.imgur.com/SMn2TAc.png)

<br>

:::info

scaleTargetRef : 選擇要綁定的耦合對象

metrics : 指「資源度量指標」，默認情況下，目前唯一支持的資源度量指標為內存。

type : 你可以指定資源度量指標使用絕對數值，而不是百分比。Utilization(百分比) 替換成 AverageValue(絕對值)，同時設置 target.averageValue 而非 target.averageUtilization 的值。

:::

範例:

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:

  # 要指向觀察的 deployment 或 StatefulSet
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  
  # 資源度量指標設定
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
      
        # 使用絕對數值或百分比
        type: Utilization
        averageUtilization: 50
status:
  observedGeneration: 1
  lastScaleTime: <some-time>
  currentReplicas: 1
  desiredReplicas: 1
  currentMetrics:
  - type: Resource
    resource:
      name: cpu
      current:
        averageUtilization: 0
        averageValue: 0
```

:::success

還有兩種其他類型的度量指標，他們被認為是 custom metrics（自定義度量指標）： 即 Pod 度量指標和 Object 度量指標。這些度量指標具有特定於集群的名稱，並且需要更高級的集群監控設置。

Pod : 僅支持 target 類型為 AverageValue

Object : 支持的 target 類型包括 Value 和 AverageValue 。如果是 Value 類型，target 值將直接與API 返回的度量指標比較， 而對於 AverageValue 類型，API 返回的度量值將按照Pod 數量拆分， 然後再與 target 值比較。

:::

<br>

###### tags: `k8s`