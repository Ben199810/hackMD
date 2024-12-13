---
title: linux 排程
tags: [crontab, linux]

---

# linux 排程

## 查看排程 
```
crontab -l
```
<br>

## 編輯排程
```
crontab -e
```
<br>

# 如何確認 crontab 正常執行
:::warning
CRON 不要打成小寫 :warning: 
:::
```
grep CRON /var/log/ [syslog*] < 這個位置是可變的，要看確切的 log 位置在哪
```
會看到類似以下輸出：
```
Jan 20 04:30:01 CODERTW CRON[25813]: (root) CMD (service apache2 restart)
```


###### tags: `linux` `crontab`
