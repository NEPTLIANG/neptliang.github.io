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
# 0x01 函数名大小写不敏感
变量名大小写敏感，但函数名不是

# 0x02 函数不能重名
故在声明函数前往往先判断是否已存在同名函数：
```php
if(!function_exists('test')) {
    function test() {
        echo "test";
    }
}
```

# 0x03 函数调用可以在声明之前
因为PHP先把声明的函数加载到内存中再逐行执行文件中的语句

# 0x04 函数声明语句被执行才会声明函数
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
_//参考：慕课网《PHP进阶篇——函数》_

_//End of Article_
