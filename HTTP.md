---
title: HTTP
tags: [http, API, RESTful API, 網路]

---

# HTTP 

<br>

## 狀態碼
[HTTP 狀態碼 (Status Codes)](https://notfalse.net/48/http-status-codes#426-Upgrade-Required-8212)
狀態碼依照首位數字，大致上可以分成五類！
* <font color="1BCFA4">1xx (Informational) — 資訊</font>
* <font color="0f0fc9">2xx (Successful) — 成功</font>
* <font color="gf6e7">3xx (Redirection) — 重新導向</font>
* <font color="F11706">4xx (Client Error) — 客戶端 錯誤</font>
* <font color="B91FEC">5xx (Server Error) — 伺服端 錯誤</font>

常見的例子：
* <font color="0f0fc9">200 (OK)</font>，代表請求成功。
* <font color="gf6e7">302 (Found)</font>，代表 目標資源 (target resource) 暫時存在於不同的 URI。
* <font color="F11706">404 (Not Found)</font>，代表在 源伺服器 (Origin Server)，
找不到目標資源現有的表示 (或其實有，不願透露存在)。

<br>

## HTTPS
* SSL 加密
間單來說透過加密演算法以混淆輸送中的資料，防止駭客資料在連線發送時被攔截破解。
[什麼是 SSL、TLS 以及 HTTPS？](https://www.websecurity.digicert.com/zh/tw/security-topics/what-is-ssl-tls-https)


---

[如何在 nginx 下實作第一個 php 留言板 Restful API](https://pin-yi.me/php-restful-api/)
# Restful API
## 定義

---

# http header
[HTTP Header & Status Code 心得](https://ithelp.ithome.com.tw/articles/10212102)
什麼是 HTTP Headers? HTTP Headers 是 HTTP 請求和相應的核心，它承載了關于客戶端瀏覽器，請求頁面，服務器等相關的信息。
有幾個特性要注意一下：
* 一定放在內容前面
* 內容格式 (圖檔、文字)
* 內容大小 可用來推估下載要耗多久時間
* 多久過期 快取機制


###### tags: `網路` `http` `API` `RESTful API`