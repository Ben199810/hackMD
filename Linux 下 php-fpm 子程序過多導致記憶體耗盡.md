---
title: Linux 下 php-fpm 子程序過多導致記憶體耗盡
tags: [php, linux]

---

# Linux 下 php-fpm 子程序過多導致記憶體耗盡
最近在工作上遇到MEM記憶體使用率衝高的問題，為了找出問題我們先透過 `free -h` 查看。
首先如果是 <font color="0f0fc9">buff/cache 記憶體使用過高</font>的話，我們可以先採取清理 buff/cache 的方式來降低記憶體的 total。
==補充說明==
控制碼種類共有 3 種 : <font color="c90f0f">
1 : 清除 Page Cache
2 : 清除 Dentries 和 Inodes
3 : 清除以上所有的種類 </font>
Page Cache 就是一般文件資料的 Cache。而 Dentries 和 Inodes 則是像資料夾、文件的編號一般。

==根據理解，清理記憶體的時候要小心清除掉 Inodes。畢竟目前看到 Linux 上不小心刪除檔案的『還原工具』都仰賴著透過 Inodes 找回檔案。==
```
# 先將資料寫入硬碟保存
sync 
# 輸入控制碼，這項指令需要 root 的權限
echo 1 > /proc/sys/vm/drop_caches
```
但這麼做的效果還是有限，因此用 top -b -o +%MEM | head -n 20 指令或者 htop 這項小工具來查看目前佔用記憶體最多的子程序。

檢查發現記憶體幾乎<font color="c90f0f">被 php-fpm : pool 佔滿</font>，所以我們要從 php 去做改善。我們可以透過設定==控制 php-fpm 子程序最大數量==。

編輯 php-fpm 配置檔案 `vi /etc/php-fpm.d/www.conf` <font color="c90f0f">設定 pm.max_children 屬性</font>。
==補充:==
pm.max_spare_servers : 該值表示空閒程序數最大值，如果大於此值，此進行清理 pm.min_spare_servers : 空閒程序數最小值，如果小於此值，則建立新的子程序。

:warning:這兩個值均不能不能大於 pm.max_children 值:warning:，通常設定 pm.max_spare_servers 值為 pm.max_children 值的60%-80%。

<font color="0fc90f">php-fpm.conf 幾個重要的參數</font>
:::info
pm = dynamic #指定進程管理方式，有3種可供選擇：static、dynamic和ondemand。
pm.max_children = 16 #static模式下創建的子進程數或dynamic模式下同一時刻允許最大的php-fpm子進程數量。
pm.start_servers = 10 #動態方式下的起始php-fpm進程數量。
pm.min_spare_servers = 8 #動態方式下服務器空閒時最小php-fpm進程數量。
pm.max_spare_servers = 16 #動態方式下服務器空閒時最大php-fpm進程數量。
pm.max_requests = 2000 #php-fpm子進程能處理的最大請求數。
pm.process_idle_timeout = 10s
request_terminate_timeout = 120
:::
**管理模式 ：**
<font color="0fc90f">static管理模式適合比較大內存的服務器</font>，而<font color="0fc90f">dynamic則適合小內存的服務器。</font><font color="c90f0f">ondemand 與 dynamic 相反</font>，內存放在第一位。==每個閒置進程在持續閒置了pm.process_idle_timeout秒後就會被殺掉==，如果服務器長時間沒有請求，就只會有一個php-fpm主進程。或者如果pm.process_idle_timeout的值太短的話，容易出現504 Gateway Time-out錯誤

###### tags: `linux` `php`
