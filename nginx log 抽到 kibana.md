---
title: nginx log 抽到 kibana
tags: [nginx, kibana, flunet, docker]

---

# nginx log 抽到 kibana
:::success
案子執行的過程⚙️⚙️⚙️
:::
### 案件需求
將nginx的log抽出來到kibana上面。
```
1. log 已經有掛出來了，可以平日動作
2. host: 10.249.53.11  kbn7.platform  kbn7.php.prod   kbn7.out interface.kbn7.com
3. 機器:
172.21.46.202  out-wagers-proxy1 owp1
172.21.146.43  out-wagers-proxy2 owp2
4. elasticsearch 目的可以參考 ocp 上的 fluentd
5. 正式站需求
```
### 了解架構
:::spoiler
<font color="028E2D">1.先找到fluent-client
2.找到fluent-server
3.找到F5 VIP
4.找到HAProxy機
5.找到ES</font>
:::
### 加入Host
先依照需求單加入內網的domain。
```
host: 10.249.53.11  kbn7.platform  kbn7.php.prod   kbn7.out interface.kbn7.com
```
可以先從現有的專案了解系統架構，可以從 [kibana](http://kbn7.out) 上面看到 <font color="EC1C12">cloud-service-nginx-access</font> 以這個 index 為例。

![](https://i.imgur.com/qw8r4ug.png)

尋找cloud-service(OCP)。

![](https://i.imgur.com/QnxEUCP.png)

先用指令登入OCP(3)。
```
oc login oc-master.pid.prod:8443
```
![](https://i.imgur.com/qhsPqaM.png)


下指令觀察pods，除了kubectl 也可以用 <font color="EC1C12">k9s</font> 。
```
kubectl get pod | grep cloud-service-api
```
:::info
k9s的指令畫面，詳細指令參考 [用K9s加速K8s集群管理](https://www.jianshu.com/p/3c105d1f5b6a)
:::
![](https://i.imgur.com/x3qikgU.jpg)
可以看到下面的 cloud-service-api 就是我們尋找的 pod 。
```
kubectl get pods cloud-service-api-0 -o jsonpath='{.spec.containers[*].name}'
```
接著可以透過上面的指令知道 pod 裡面有什麼 container 。

![](https://i.imgur.com/8whz9Rh.png)


看到 <font color="EC1C12">fluentd-client</font> 了，接下來透過下面指令進入 fluent-client 找尋設定檔。當然我們也可以使用OCP的管理平台快速的找到。
```
kubectl exec -it cloud-service-api-1 -c fluentd-client bash

cat /etc/fluentd/fluentd-client.conf
```
從下圖資訊中，可以看到與 fluent-client 連接的 fluent-server 。

![](https://i.imgur.com/Bx0UtCo.png)
![](https://i.imgur.com/r3UoYWP.png)

再來進入到 service 裡面找設定檔。

![](https://i.imgur.com/U1IOQrx.png)

可以從上圖資訊知道 <font color="EC1C12">172.17.117.23</font> 是與 fluent-server 連接的機器(F5 VIP)。

![](https://i.imgur.com/Vpgm43F.png)

#### <font color="048212">過程中遇到的問題</font>
:::spoiler
1.fluent-client 沒有抓到 log。
<font color="048212">要掛載外部的卷宗。</font>
2.無法連fluent-server。
<font color="048212">直接連到F5 VIP。</font>
3.不知道F5-->haproxy-->es的過程。
<font color="048212">DC負責，先不用知道。</font>
4.如何取得Quay上面的image。
<font color="048212">先在跳板機上面pull再丟到後面的機器</font>
5.nginx log 與 es log 不一致。
<font color="048212">time-key , keep-time-key</font>
:::
###### tags: `docker` `nginx` `kibana` `flunet`