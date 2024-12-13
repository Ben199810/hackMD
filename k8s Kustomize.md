---
title: k8s Kustomize
tags: [k8s]

---

# k8s Kustomize
<br>

## 介紹
參考網站 : [【雲端技能學習】Kubernetes 簡單學 : 領進 kustomize 修行在個人](https://www.netron.asia/tw/newsDetail/34)

如果需要使用差不多的程式碼部署到各種環境上面，常常會有不同的設定檔案需要管理。這時候可以使用 <font color="red">Kustomize</font> 來定義 <font color="blue">base</font> 以及想要客製化的資訊。
<br>

## 範例
首先在包含 yaml 檔案的位置，建立 `kustomization` 檔案檔案會包含要定義 `resourse` 與你想要客製化的資訊。
檔案結構如下圖所示：
```
~/example
├── kustomization.yaml
├── deployment.yaml
└── service.yaml
```
這時可以使用 kustomize build 出簡單的成果了，使用方法如下：

`kustomize build ~/example`

使用 kustomize build 後，只會將 yaml 用 stdout 的方式印出，，再搭配kubectl就能夠直接建立資源了。

`kustomize build ~/example | kubectl apply -f -`

==補充說明== : 也可以很單純的使用 `kubectl apply -k`的方式來達成。
<br>

## 使用 overlays 資料夾建立多環境

當今天我們有多個環境需要配置不同的設定時，kustomize 也能夠幫我們達成。
先建立出 <font color="blue">base</font> 與 <font color="blue">overlays</font> 的資料夾，將剛剛的 YAML 檔案放進去 base，再根據需要的環境在 overlays下分別建立出資料夾。
```
~/example
├── base
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays
    ├── development
    │   ├── cpu_count.yaml
    │   ├── kustomization.yaml
    │   └── replica_count.yaml
    └── production
        ├── cpu_count.yaml
        ├── kustomization.yaml
        └── replica_count.yaml
```
要部署到k8s上，與 base 的方式相同，搭配著 kubectl。

`kustomize build ~/example/overlays/production | kubectl apply -f -`

###### tags: `k8s`