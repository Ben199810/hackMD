---
title: helm values 拆解與規範
tags: [helm]

---

# helm values 拆解與規範
⭐️ Chart 名稱必須是小寫和數字，單詞之間可以用橫杠分隔(-)

helm 對 values 的使用，提供一些使用跟建構的建議，讓團隊專注於設計 chart 的 values.yaml 文件

⭐️ 命名規範：小寫字母開頭，駝峰式命名
```yaml=
chicken: true
chickenNoodleSoup: true
```
🛑 所有 Helm 內置的變數都以大寫是字母開頭
`.Release.Name`、`.Capabilities.KubeVersion`

Yaml 是很靈活的格式，可以嵌套的很深，也可以是扁平的。
大多數的場景中，扁平優於嵌套。因為對模板開發者跟用戶來說更加簡單。

🚨 為了最佳的安全性，嵌套值得每一層都必須進行存在性檢查，對於扁平的配置模板更易閱讀和使用。因此檢查可以跳過。

🚨 當有大量相關變量時，至少有一個是 bool，嵌套的值可以改善可讀性。

盡可能保持扁平化，如何做到？
細分到 container，每個 container 都是一個獨立的 block

```yaml=
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
# --- deployment 👆
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
# --- replicaSet 👆
  template:
    metadata:
      labels:
        app: nginx
# --- pod 👆
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
# --- container 👆
```

```yaml=
deployment:
  name: { . Release.name }
  namespace: { .Release.namespace }
  labels:
    app: app
  annotations:
    reloader.stakater.com/auto: "true"
# 如果該值是嵌套，且下一層值有 bool 判斷，可以不用對此值做存在檢查
replicaSet:
  rollingUpdateEnabled: true
pod:
  labels: {}
php:
  initEnabled: true
  initCommand: {}
  labels: {}
  image: php
  # command arg 有兩種寫法，以 k8s 文件為主
  command: {}
  # 如果 template 有寫好的屬性，控制需要再屬性名稱後面冠上Enabled
  postStartEnabled: true
  preStop: {}
  resources: {}
  volumeMounts: {}
volumes: {}
        
fluentBit:
  enabled: true
  resources: {}
  
logrotate:
  resources: {}
          
          
          
    
```

## 參考文件
[helm 最佳實踐](https://helm.sh/zh/docs/chart_best_practices/conventions/)

###### tags: `helm`