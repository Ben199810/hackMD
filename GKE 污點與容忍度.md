---
title: GKE 污點與容忍度
tags: [k8s, GKE, 污點, 容忍度]

---

# GKE 污點與容忍度

## 污點

在節點（node）上可以建立==污點==，也有==節點標籤==

如果是在節點池應用設置，所以新建立的節點（node）都有自動獲取污點和標籤

:::success
污點效果：
NoExecute :point_right: GKE 會逐出在這些節點上運行且不容忍新污點的任何 pod 

:::

## 容忍度

在節點池設置污點之後，任何工作負載（pod）都無法在上面運行，除非對工作負載添加容忍度。

:star: 如果節點是添加標籤，則可以設置節點親和性規則。

<br>

:star2: 

![](https://i.imgur.com/TfgIbf3.png)

<br>

###### tags: `k8s` `GKE` `污點` `容忍度`