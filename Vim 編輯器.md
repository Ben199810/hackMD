---
title: Vim 編輯器
tags: [vim, linux]

---

# Vim 編輯器
:::success
:star: 三種模式：一般模式、插入模式、命令行模式
:::

## 一般

### 游標
h: 左
j: 下
k: 上
l: 右

### 游標移動
:::success
:accept: 數字 ＋ 字母

10j 向下移動 10 行

10k 向上移動 10 行

5l 向右移動 5 字元

5h 向左移動 5 字元

"+" :point_right: 游標移動到非空白字元的下一列

"-" :point_right: 游標移動到非空白字元的上一列

0 行首 :point_right: 這是數字『 0 』：移動到這一列的最前面字元處 

$ 行尾 :point_right: 移動到這一列的最後面字元處

G :point_right: 移動到這個檔案的最後一列

nG :point_right: n 為數字。移動到這個檔案的第 n 列。例如 20G 則會移動到這個檔案的第 20 列

gg :point_right: 移動到這個檔案的第一列，相當於 1G 啊！

:::

### 翻頁
:::info
[Ctrl] + [f] :point_right: 螢幕『向下』移動一頁，相當於 [Page Down]按鍵

[Ctrl] + [b] :point_right: 螢幕『向上』移動一頁，相當於 [Page Up] 按鍵
:::

### 復原、重做
:::info
u :point_right: 復原前一個動作。

[Ctrl]+r :point_right: 重做上一個動作。

. :point_right: 不要懷疑！這就是小數點！意思是重複前一個動作的意思。 如果你想要重複刪除、重複貼上等等動作，按下小數點『.』就好了！
:::


## 命令行

:::info

:w (write)存檔

:q (quit)離開

:e 檔案名稱 :point_right: 開啟其他檔案

:tabe 檔案名稱 :point_right: 分頁命令

:gt 下一頁 

:gT 上一頁

:close 關閉分頁

:::



### 頁面定位
:::info
G 頁面底端
gg 頁面頂端
:::


###### tags: `linux` `vim`