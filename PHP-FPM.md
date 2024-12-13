---
title: PHP-FPM
tags: [php]

---

# PHP-FPM
## php process
[PHP Process Tuning](https://clhjoe.github.io/post/php-process-tuning/)
[Tuning dynamic php-fpm settings](https://cmorrell.com/php-fpm/)
通常 php-fpm 會有 nginx 一起配合來高效能的處理靜態檔案，但是如果是動態頁面就必須交由後端例如GO、Python 或是PHP來進行了。
現行的 nginx 與 php 都是通過 fast-cgi 來進行，==FastCGI 透過一個統一管理的Process管理背後的Worker避免過度頻繁建立、關閉Process。==

![](https://i.imgur.com/7mdWoW2.jpg)

### php manager(PM)
php 有三種模式，分別是 static 、 dynamic 、 ondemand
詳細介紹參考之前的[Linux 下 php-fpm 子程序過多導致記憶體耗盡](https://hackmd.io/hOk-GwXvTe6OR4unyxiDyg) :bookmark_tabs: 
### 公式與配置優化
==公式== : 
最大 process 數 = (系統記憶體 - 被服務使用的記憶體 - buffer)/每個 process 使用的記憶體

計算記憶體 一個PHP-FPM 使用了多少記憶體？
```
ps --no-headers -o "rss,cmd" -C php-fpm | awk '{ sum+=$1 } END { printf ("%d%s\n", sum/NR/1024,"Mb") }'
```
額外閱讀文章 ：
[Linux-Nginx-Mysql-Php](https://www.twblogs.net/a/5ea2eb466052e10444d7259b)
###### tags: `php` 