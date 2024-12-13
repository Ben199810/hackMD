---
title: Dockerfile 最佳實踐
tags: [docker, dockerfile]

---

# 鏡像
image 是一堆只讀層(read-only layer)重疊在一起的統一視角

除了最下面一層，其他層都有指標指向下一層，這些層是Docker 內部的實作細節

可以在主機檔案系統上找到關於這些層的檔案，在運作的容器內部。這些層是看不到的。可以在主機上發現它們存在於 `/var/lib/docker/overlay2` 目錄底下

# 鏡像分層結構
可以透過 `docker inspect [image]` 來查看映像包含哪些層

`GraphDriver` 負責鏡像本地的管理和儲存以及運行容器生成映像等工作，像是一個管理引擎

# 小技巧

:::success
盡可能使用官方鏡像作為基礎鏡像
:::

| 鏡像名稱 | 大小     | 說明和使用場景                                                                             |
| -------- | -------- | ------------------------------------------------------------------------------------------ |
| busybox  | 754.7 KB | 一個超級簡化版嵌入式Linux 系統，臨時測試用                                                 |
| alpine   | 2.68 MB  | 一個面向安全的、輕量級的Linux系統，基於musl libc 和busybox。主要用於測試，也可用於生產環境 |
| centos   | 79.65 MB | 主要用於生產環境，支援 CentOS/Red Hat，常用於追求穩定性的企業應用                          |
| debian   | 52.4 MB  | 主要用於生產環境                                                                           |



# 參考
[Dockerfile 的最佳實務｜ Dockerfile 你寫的都對麼？](https://juejin.cn/post/7099248266524983327)