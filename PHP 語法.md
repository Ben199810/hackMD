---
title: PHP 語法
tags: [php]

---

# PHP 語法
:star: php 是弱型別的語言

:star: php 腳本以`<?php`為開頭，`?>`為結尾

## 註釋

單行註釋 `//`
多行註釋 `/**/`

<br>

## 變量

var_dump($var) :point_right: 回傳變數的型態跟值

php 的變量跟 shell 很像，用 `$變數名稱` 表示

ex. $x , $y , $z

:star: 在函式內想要引用全域變數，要加上`global`

```php=
<?php
$x=5;
$y=10;
 
function myTest()
{
    global $x,$y;
    $y=$x+$y;
}
 
myTest();
echo $y; // 输出 15
?>
```

---

:star: 在某個函式結束時，不希望變數被刪除加上`static`

```php=
<?php
function myTest()
{
    static $x=0;
    echo $x;
    $x++;
    echo PHP_EOL;    // 换行符
}
 
myTest();
myTest();
myTest();
?>
```

<br>

## 輸出

echo 或者 print

###### tags: `php`