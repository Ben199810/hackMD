---
title: hpa custom metrics (客製指標)
tags: [k8s, metrics, hpa]

---

# hpa custom metrics (客製指標)

k8s 內建的 metrices 支援 cpu 以及 memory :accept: 

:::info 

如果今天要用到自定義的指標讓 hpa 決定怎麼擴展或收縮呢？

:::

以下連結為參考資源：

[在 GKE/kubernetes 上使用自定義指標擴展 PHP-FPM](https://www.ashsmith.io/scaling-phpfpm-with-custom-metrics-gke)

[使用Cloud Monitoring 指標自動擴縮部署](https://cloud.google.com/kubernetes-engine/docs/tutorials/autoscaling-metrics#custom-prometheus_1)

[php-fpm_exporter](https://github.com/hipages/php-fpm_exporter)

[Custom Metrics - Stackdriver Adapter](https://github.com/GoogleCloudPlatform/k8s-stackdriver/tree/master/custom-metrics-stackdriver-adapter)

## 工作原理

1. 先了解 HPA 是怎麼取得自訂義的指標的，又是跟誰拿的！ :palms_up_together: 

從下圖可以看到 HPA 訪問了 k8s 的 API 從上面取得資料 :ok_hand:

所以代表今天搜集到的指標也要傳給 API 這樣 HPA 才有資訊可以工作！:male-construction-worker:

<br>

![](https://i.imgur.com/sVC8mHc.png)

<br>

下面這張圖可以知道 hpa 如何去擴縮 pod 的架構，另外圖中也有提到也可以使用 prometheus 去擴縮 pod。

<br>

![](https://i.imgur.com/O4nNPBg.png)

<br>

下圖是有各樣的 Adapter 可以去獲得第三方的指標工具參考 :construction_worker:

<br>

![](https://i.imgur.com/BvMmmsx.png)

<br>

---

# 實作講解

1. 先建立自訂義的 Adapter 這裡我們使用 google官方提供的 Stackdriver  Adapter 來使用。

[k8s-stackdriver github](https://github.com/GoogleCloudPlatform/k8s-stackdriver/tree/master/custom-metrics-stackdriver-adapter)

```
kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/k8s-stackdriver/master/custom-metrics-stackdriver-adapter/deploy/production/adapter_new_resource_model.yaml
```

<br>

2. 建立 configmap.yml

:::info

這裡我們會設定 nginx 以及 php-fpm 的設定擋

:::

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: php-fpm-conf
  namespace: test
data:
  www.conf: |
    [www]
    user = 900
    group = 900
    listen = 9000
    listen.owner = 900
    listen.group = 900
    listen.mode = 0660
    pm = dynamic
    pm.max_children = 150
    pm.max_requests = 300
    pm.start_servers = 24
    pm.min_spare_servers = 24
    pm.max_spare_servers = 126
    pm.status_path = /status
    ping.path = /ping
    ping.response = OK
    catch_workers_output = yes
    request_terminate_timeout = 300
    clear_env = no
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-conf
  namespace: test
data:
  nginx.conf: |
    server {
      listen 80;
      listen [::]:80;

      server_name _;
      root /var/www/service/;
      index index.php;

      location / {
        try_files $uri $uri/ /index.php$is_args$args;
      }      

      location ~ ^/(status|ping)$ {
        fastcgi_pass                  127.0.0.1:9000;
        fastcgi_index                 index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include                       fastcgi_params;
      }
    }
```

<br>

3. 撰寫 deployment.yaml

:::warning

在 deployment.yaml 有使用一個叫做 php-fpm exporter 的工具是用來將 php-fpm 的各種狀態先導出來，接著透過 prometheus-to-sd 的工具轉換成 Stackdriver Adapter 指標

:::

各種導出器可以參考 [Prometheus 各類的 exporter 參考](https://prometheus.io/docs/instrumenting/exporters/#exporters-and-integrations)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: phpfpm-demo
  namespace: test
  labels:
    app: phpfpm-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: phpfpm-demo
  template:
    metadata:
      labels:
        app: phpfpm-demo
    spec:
      containers:
        - name: php-fpm
          image: php:fpm
          workingDir: /var/www/service
          ports:
            - containerPort: 9000
          resources:
            requests:
              cpu: 100m
              memory: 1G
            limits:
              cpu: 100m
              memory: 1G
          volumeMounts:
            - name: application
              mountPath: /var/www/service/
            - name: php-fpm-config
              mountPath: /usr/local/etc/php-fpm.d/www.conf
              subPath: www.conf

        - name: nginx
          image: nginx:alpine
          workingDir: /var/www/service
          ports:
            - containerPort: 80
          volumeMounts:
            - name: application
              mountPath: /var/www/service/
            - name: nginx-config
              mountPath: /etc/nginx/conf.d/

        - name: phpfpm-exporter
          image: gcr.io/rd6-project/php-fpm_exporter:1.0
          env:
            - name: PHP_FPM_SCRAPE_URI
              value: "tcp://localhost:9000/status"
            - name: PHP_FPM_FIX_PROCESS_COUNT
              value: "true"
          resources:
            requests:
              cpu: 10m
            limits:
              cpu: 10m

        - name: prometheus-to-sd
          image: gcr.io/rd6-project/prometheus-to-sd:v0.9.0
          ports:
            - containerPort: 6060
              protocol: TCP
          command:
            - /monitor
            - --stackdriver-prefix=custom.googleapis.com
            - --monitored-resource-type-prefix=k8s_
            - --source=:http://localhost:9253
            - --pod-id=$(POD_NAME)
            - --namespace-id=$(POD_NAMESPACE)
          resources:
            requests:
              cpu: 10m
            limits:
              cpu: 10m
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
      volumes:
        - name: application
          emptyDir: {}
        - name: php-fpm-config
          configMap:
            name: php-fpm-conf
        - name: nginx-config
          configMap:
            name: nginx-conf
```

<br>

4. 撰寫 hpa.yaml

:::warning

注意: apiVersion: autoscaling/beta1 跟 beta2 的撰寫格式會有些許不同！

:::

```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: phpfpm-demo-hpa
  namespace: test
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: phpfpm-demo
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Pods
      pods:
        metric:
          name: phpfpm_active_processes
        target:
          averageValue: 50
          type: AverageValue 
    - type: Pods
      pods:
        metric:
          name: phpfpm_accepted_connections
        target:
          averageValue: 50
          type: AverageValue

```

###### tags: `k8s` `metrics` `hpa`