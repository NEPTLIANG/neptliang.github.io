---
layout:       post
title:        "PHP函数笔记"
subtitle:     "PHP函数方面的注意事项"
date:         2019-09-13 13:18:00
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - PHP
---
# 0x00 前言
_//本文是PHP函数相关笔记，非完整教程_

# 0x01 PHP中如何自定义函数及调用函数
### 函数名大小写不敏感
但 __变量名__ 大小写敏感；

### 函数不能重名
故在声明函数前往往先判断是否已存在同名函数：
```php
if(!function_exists('test')) {
    function test() {
        echo "test";
    }
}
```

### 函数调用可以在声明之前
因为PHP __先把声明的函数加载到内存__ 中再逐行执行文件中的语句

### 函数声明语句被执行才会声明函数
如果函数声明语句没有被执行，则不会声明函数，如：
```php
if(false) {  //条件不满足，下面的声明语句不执行
    function test2() {
        echo "test2";
    }
}
test2();  //报错

function test3() {
    function test4() {
        echo "test4";
    }
}
test4();  //test3()没有被调用，故test4()没有被声明，报错
```

# 0x02 PHP中函数的返回值
### 函数默认返回```null```

### 函数声明的前面建议写上注释
```php
/**
 * 打印"Hello"
 * @param number $a
 * @param number $b
 * @return string
 */
function test5($a, $b) {
    return "Hello";
}
```

# 0x03 PHP中函数参数详解
### 函数可以有“可选参数”
声明函数时，有默认值的参数叫 __“可选参数”__ ，没有默认值的参数叫 __“必选参数”__ 
```php
function test6($rows = 3, $cols = 5)
{
    $table = "<table>";
    for ($i = 0; $i < $rows; $i++) {
        $table .= "<tr>";
        for ($j = 0; $j < $cols; $j++) {
            $table .= "<td>{$i}*$j=" . $i * $j . "</td>";
        }
        $table .= "</tr>";
    }
    $table .= "</table>";
    echo $table;
}
test6();  //不传参调用
test6(5, 3);  //传参调用
```

### 函数可以既有“可选参数”又有“必选参数”
必选参数一定要在可选参数之前
```php
function test7($rows, $cols, $content = "Hello")
{
    $table = "<table>";
    for ($i = 0; $i < $rows; $i++) {
        $table .= "<tr>";
        for ($j = 0; $j < $cols; $j++) {
            $table .= "<td>$content, {$i}*{$j} = " . $i * $j . "</td>";
        }
        $table .= "</tr>";
    }
    $table .= "</table>";
    echo $table;
}
test7(3, 5);  //不指定可选参数的值
test7(5, 3, "Hi");  //指定可选参数的值
```

# 0x04 PHP中变量的作用域解析
### 变量分为：
1. __局部变量__：函数体内声明的变量
```php
function test9()
{
    $i = 1;
    $j = 2;
}
test9();
var_dump($i, $j);  //函数外无法访问局部变量，报错
```
2. __全局变量__：函数体外声明的变量，或在函数体内通过```global```关键字声明的变量

### 其中局部变量分为：
1. __动态变量__：函数执行完毕后立即释放
2. __静态变量__：通过```static```关键字声明的变量；第一次调用所在函数时初始化，所在函数执行完毕后不被释放而是保存在静态内存中，再次调用所在函数时从静态内存中取出该变量的值而不执行函数中的初始化语句
```php
function test10()
{
    static $i = 1;  //第一次调用test10()时初始化$i的值为1，再次调用test10()时不再执行该语句
    echo $i . "<br/>";
    $i++;  //修改$i的值
}
test10();  //$i == 1
test10();  //$i == 2
test10();  //$i == 3
test10();  //$i == 4
var_dump($i);  //$i是局部变量，报错
```

### 如何在函数体内使用全局变量：
1. __通过```global```关键字__
```php
$p = 1;
$q = 2;
function test11()
{
    var_dump($p, $q);  //函数内无法直接访问全局变量，报错
}
test11();
function test12()
{
    global $p;  //在函数内通过global关键字访问全局变量
    global $q;
    //或者直接写 global $p, $q;
    var_dump($p, $q);  //在函数内可以访问，正常输出
    $p = 3;
    $q = 4;  //修改全局变量的值
}
test12();
var_dump($p, $q);  //$p值为3，$q值为4
function test14()
{
    global $x, $y;  //在函数内通过global关键字声明全局变量
    $x = 1;
    $y = 2;
}
test14();
var_dump($x, $y);  //在函数外可以访问，正常输出
```

注意声明全局变量时不能初始化
```php
function test13()
{
    global $i = 1;  //报错
    /* 正确操作：
    global $i;
    $i = 1; */
}
test13();
```

2. __通过```$GLOBALS```超全局变量__
```php
$a = 1;
//print_r($GLOBALS);  //在页面上打印以查看$GLOBALS的内容
function test15()
{
    echo $GLOBALS['a'];  //在函数内通过$GLOBALS超全局变量访问全局变量，变量名即为键名
    $GLOBALS['a'] = 2;  //修改全局变量的值
}
test15();
var_dump($a);  //$a值为2
```

# 0xff 其他
设置HTML head内容：
```php
header("content-type:text/html;charset=utf-8");
```

将字符串中的字母转为小写：
```php
strtolower("Test");
```

获取文件名的拓展名：
```php
$fileInfo = "index.php";
pathinfo($fileInfo, PATHINFO_EXTENSION);
```

_//参考：慕课网《PHP进阶篇——函数》_

_//End of Article_
