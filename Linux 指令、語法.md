---
title: Linux 指令、語法
tags: ["linux"]

---

## 快速搜尋

- 快速搜尋執行過的指令
ctrl + R

快速清除(clear)
command + R

## alias

:::success
alias ll='ls -al'
:::

<br>

## ls 列出檔案

:::success
* --full-time：列出完整時間 
* -lrt 按照時間排序(從舊到新)
* -lt 按照時間排序(從新到舊)
* -Sl 依照檔案大小排序(從大到小)
* -Slr 依照檔案大小排序(從小到大)
:::

```
ls
ls -al //顯示詳細資訊
```

<br>

## grep 匹配文字

:star: grep 亦可搭配萬用字元（*）同時搜尋多個檔案

:::info
* -i：不分大小寫的方式搜尋
* -n：標示匹配文字的行號
* -v：反向匹配，匹配的資料排除
* -r：遞迴搜尋
:::

```
grep [關鍵字] [檔案1] [檔案2]
```
:star: 想要從特定的檔案中尋找關鍵字，可以使用 `-r` 搭配 `--include` 指定檔案類型:

```
# 在所有 *.conf 中尋找 ubuntu
grep -r --include="*.conf" ubuntu /etc/
```

:dango: 常用：`grep -rns "keyword"`

[更多內容參閱](https://blog.gtwang.org/linux/linux-grep-command-tutorial-examples/)
<br>

## df 磁碟、硬碟
```
df
```
:::info
-a 包含所有的具有 0 Blocks 的檔案系統
-h 使用人類可讀的格式
-i 列出 inode 資訊
-P 使用 POSIX 輸出格式
:::
<br>

## du 檔案大小
```
du
du -sh * //常用
```

:::info
* -a 顯示目錄中個別檔案的大小
* -b 顯示目錄或檔案大小時，以byte為單位
* -c 除了顯示個別目錄或檔案的大小外，同時也顯示所有目錄或檔案的總和
* -k 以KB(1024bytes)為單位輸出
* -m 以MB為單位輸出
* -s 僅顯示總計，只列出最後加總的值
* -h 以K，M，G為單位，提高資訊的可讀性
* -x 以一開始處理時的檔案系統為準，若遇上其它不同的檔案系統目錄則略過
* -L<符號連結> 顯示選項中所指定符號連結的原始檔大小
* -S 顯示個別目錄的大小時，並不含其子目錄的大小
* -X<檔案> 在<檔案>指定目錄或檔案
* –exclude=<目錄或檔案> 略過指定的目錄或檔案
* -D 顯示指定符號連結的原始檔大小
* -H 與-h引數相同，但是K，M，G是以1000為換算單位
* -l 重複計算硬體連結的檔案
:::

<br>

## TCP 連線數

:::info
* -a：全部
* -au：UDP
:::

```
netstat -at | grep ESTABLISHED | wc -l
```

<br>

## swap 

:star: sync 強制將 swap 內容寫入磁碟，防止資料遺失

:::success
稱為交換空間，給Linux系統在主記憶體RAM不夠用的時候，將記憶體內比較久沒用到的資料塞進去用的。
:::

```
sync
swapoff -a && swapon -a
```

<br>

## free 記憶體

:star: 查閱系統負載(loading)與行程(process)最常用的就是 **ps** 與  **top** 這兩個指令。

### 查看

```
free -h
```

### ps

:::info
-e : 表示輸出所有行程的一些基本資訊。
-sort : 排序(預設值由小到大)，若要大到小在前面再加上一個負號。
:::

:star: 針對指令顯示說明：

:::success
pid : 行程ID(process ID)
ppid : 父行程ID(parent process ID)
cmd : 程式名稱
％mem : 記憶體使用量(百分比)
%cpu : CPU使用量(百分比)
:::

```
ps -eo pid, ppid, cmd, %mem, %cpu --sort=-%mem | head
```

### top

:::info
* -b : 指 batch 模式的意思。
* -o : 以記憶體用量來排序行程。
* head -n 17 : 保留前面17行
:::

### ==補充介紹==

:::info
htop : 可以即時反應CPU與MEM的使用狀況的工具 :construction_worker: 
:::

```
top -b -o +%MEM | head -n 17
```
<br>

## kill 終止進程
==註:== 要強制終止使用 **kill -9**

```
kill [pid]
```
<br>

## mkdir 建立目錄 :file_folder: 
==指令參數設定==

-v、 –verbose : 印出建立目錄的講細資訊。
-Z、–context=CTX : 對新建目錄進行 SELinux 安全設定。
<font color="c90f0f">-p : 建立多層目錄。(常用)</font>

```
mkdir <目錄名稱>
```
<br>

## 系統帳號檢視 
cat /etc/passwd，/etc/passwd儲存了系統帳號的資訊。
如果單純要列出使用者帳號，增強==可讀性==可以用awk以" : "字符做分隔。除了用 awk 外， 也可以用 cut 指令，會得出相同的結果:

```
awk -F’:’ ‘{ print $1}’ /etc/passwd
cut -d: -f1 /etc/passwd
```

<br>

## gzip 解壓縮檔案、壓縮檔案 :file_folder: 
==補充說明==

特別注意的是,,用 <font color="c90f0f">gzip</font> 壓縮檔案時預設會把原始的檔案刪除。
解壓縮用選項 <font color="c90f0f">〝-d〞</font> ,解壓縮完會自動把壓縮的〝.gz〞檔刪除。

```
gzip [filename].gz #解壓縮
gzip [filename]
```

<br>

## scp 傳送檔案 or 目錄 :file_folder: 
指令：<font color="#160CF9">scp</font> -參數 -自訂埠號參數 自訂埠號 -速率參數 速率 [帳號@來源主機IP]:來源檔案 [帳號@目的主機IP]:目的檔案

:::info
主要參數：
-p：保留原本檔案權限資料
<font color="#c90f0f">-r：連同整個目錄夾複製，否則只會傳輸當前目錄底下的檔案(重要)</font>
-C：將資料先壓縮後再傳送，可節省傳輸量(常用)
-l：限制傳輸時使用頻寬大小(單位為 Kbit/s–除1024則為MB/s)
-v：顯示傳輸進度與資訊(常用，本地端複製無法顯示進度)
-P：自訂埠號(一般預設埠號為22，如果遠端ssh埠號改為自訂，則透過此指令自訂)
-4：強制使用IPv4來傳輸
-6：強制使用IPv6來傳輸
:::

實際範例：
將本地端的目錄1複製到遠端目錄2
```
scp -r /path/directory1  user@192.168.1.1:/path/directory2
```
:warning: **如果目錄2以先被建立，則複製的目錄1會存放在遠端目錄2底下，而非改名為目錄2**

複製本地端檔案至遠端，並顯示進度
```
scp -v /path/file user@192.168.1.1:/path/file 
```
複製本地端檔案至遠端，透過壓縮並限速800 kbit/s
```
scp -C -l 800 /path/file user@192.168.1.1:/path/file
```

<br>

## 查詢 Prot 是否開啟
以下有幾種的檢測工具以及指令 :construction_worker: 
**nc(netcat)** 
安裝指令：`yum install nc`
==參數設定說明==
-z : 只進行掃描，不進行任何資料傳輸
-v : 只顯示掃描訊息
-v3 : 只等待3秒
```
nc -z <IP:Port>
```
**namp(Network Mapper)**
安裝指令：`yum install nmap`
```
nmap <IP> -p <Port>
```
**Telnet**
安裝指令：`yum install telnet`
```
telnet <IP> <Port>
```

<br>

## Find 查詢

<br>

[在 Linux 下使用 find 指令查詢目錄與檔案的速查筆記](https://https://blog.miniasp.com/post/2010/08/27/Linux-find-command-tips-and-notice)

<br>

使用 find 查詢目錄或檔案

:::info
-name :point_right: 查詢的檔案名稱
-iname :point_right: 查詢的檔案名稱(不區分大小寫)
-type :point_right: 類型
```
補充說明：-type可用參數如下
b block (buffered) special

c character (unbuffered) special

d directory ( 一般目錄 )

p named pipe (FIFO)

f regular file ( 一般檔案 )

l symbolic link

s socket

D door (Solaris)
```
-size :point_right: 檔案大小
-print0 :point_right: 使輸出結果不以「斷行字元」分隔
:::

<br>

## xargs 切割

[Linux xargs 命令](https://www.runoob.com/linux/linux-comm-xargs.html)

讀取資料之後，以空白字元或換行做分隔，切割成多個字串(基本用法)

<br>

## sed 替代

主要修飾文字檔
```
sed [option] [add1][add2] [command] [/pattern][/replacement] / [flag][file]
```
:::info
a : 新增
i : 插入

:::
<br>

## tr 轉換

用於轉換或刪除文件中的字符
<br>

## dig 解析網址

:star: 相似指令：`nslookup`

使用方式：
```
dig [網址]
```
:::success
+short :point_right: 簡易的輸出格式
@8.8.8.8 :point_right: 指定DNS伺服器
AAAA :point_right: ipv6 網址解析
:::
<br>

## EOF
EOF 的意思： End of file

使用 cat + EOF 輸入多行文本
cat 能夠配合重新定向生成文件並追加操作。

---

![](https://i.imgur.com/Kb0ROG9.png)

<br>

```
<<EOF  //開始
...
EOF  //結束
```

###### tags: `linux`