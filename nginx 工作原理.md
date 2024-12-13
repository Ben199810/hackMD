---
title: nginx 工作原理
tags: [nginx]

---

# nginx 工作原理
- 正向代理
    1. cient 透過跳板機或 proxy 連到原始伺服器。client 傳送一個 request 並指定目標

- 反向代理
    1. client 透過網域發出存取 web server 的 request。透過 dns server 解析
    2. 反向代理在本地快取尋找請求內容，找到後直接把內容傳送給使用者

###### tags: `nginx`