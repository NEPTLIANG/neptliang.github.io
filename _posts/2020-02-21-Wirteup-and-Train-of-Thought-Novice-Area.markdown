---
layout:       post
title:        "Web新手区Writeup及思路总结"
subtitle:     "Null"
date:         2020-02-21 0:15:43
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - sec
    - writeup
---


# 1. view_source

**题目描述**：X老师让小宁同学查看一个网页的源代码，但小宁同学发现鼠标右键好像不管用了。

**WP**:
> **[原理]**  
> 浏览器开发者工具  
> 
> **[目地]**  
> 掌握查看源代码的方式  
>
> **[环境]**  
> windows  
>
> **[工具]**  
> firefox或chrome  
>
> **[步骤]**  
> 1. 用firefox打开页面，摁下F12，打开查看器可以得到flag  
> 2. 或用chrome打开页面，摁下F12，在Elements栏可以得到flag

**总结**：审查元素查看源码寻找线索。2020.2.16.Sun

---

# 2. robots

**题目描述**：X老师上课讲了Robots协议，小宁同学却上课打了瞌睡，赶紧来教教小宁Robots协议是什么吧。

**WP**:
> **[原理]**  
> robots.txt是搜索引擎中访问网站的时候要查看的第一个文件。当一个搜索蜘蛛访问一个站点时，它会首先检查该站点根目录下是否存在robots.txt，如果存在，搜索机器人就会按照该文件中的内容来确定访问的范围；如果该文件不存在，所有的搜索蜘蛛将能够访问网站上所有没有被口令保护的页面。  
>
> **[目标]**  
> 掌握robots协议的知识
>
> **[环境]**  
> windows
>
> **[工具]**  
> 扫目录脚本dirsearch(项目地址：https://github.com/maurosoria/dirsearch)
> 
> **[步骤]**  
> 1. 根据提示robots,可以直接想到robots.txt,  
> 2. 或通过扫目录也可以扫到: python dirsearch.py -u http://10.10.10.175:32793/ -e *  
> 3. 访问http://111.198.29.45:33982/robots.txt发现f1ag_1s_h3re.php  
> 4. 访问http://111.198.29.45:33982/f1ag_1s_h3re.php得到flag

**总结**：从```robots.txt```或扫目录寻找线索。2020.2.16.Sun

---

# 3. backup

**题目描述**：X老师忘记删除备份文件，他派小宁同学去把备份文件找出来,一起来帮小宁同学吧！

**WP**:
> **[目标]**  
> 掌握有关备份文件的知识  
> 常见的备份文件后缀名有: .git .svn .swp .svn .~ .bak .bash_history
> 
> **[环境]**  
> 无
> 
> **[工具]**  
> 扫目录脚本dirsearch(项目地址：https://github.com/maurosoria/dirsearch(https://github.com/maurosoria/dirsearch))
> 
> **[步骤]**  
> 1. 可以手动猜测,也可以使用扫目录脚本/软件,扫一下,这里使用的是github上的脚本dirsearch,命令行下: py python3 dirsearch.py -u http://10.10.10.175:32770 -e *  
> 2. 看到存在备份文件index.php.bak访问 http://10.10.10.175:32770/index.php.bak  
> 3. 保存到本地打开，即可看到flag

**总结**：扫目录，从.git .svn .swp .svn .~ .bak .bash_history等备份文件寻找线索。2020.2.16.Sun

---

# 4. cookie
**题目描述**：X老师告诉小宁他在cookie里放了些东西，小宁疑惑地想：‘这是夹心饼干的意思吗？’

**WP**:
> **[原理]**  
> ​ Cookie是当主机访问Web服务器时，由 Web 服务器创建的，将信息存储在用户计算机上的文件。一般网络用户习惯用其复数形式 Cookies，指某些网站为了辨别用户身份、进行 Session 跟踪而存储在用户本地终端上的数据，而这些数据通常会经过加密处理。
> 
> **[目的]**  
> 掌握有关cookie的知识，了解cookie所在位置  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox  
>   
> **[步骤]**
> 1. 浏览器按下F12键打开开发者工具，刷新后，在存储一栏，可看到名为look-here的cookie的值为cookie.php  
> 2. 访问http://111.198.29.45:47911/cookie.php，提示查看http响应包，在网络一栏，可看到访问cookie.php的数据包  
> 3. 点击查看数据包，在消息头内可发现flag

**总结**：在cookie、数据包头部寻找线索。2020.2.16.Sun

---

# 5. disabled_button
**题目描述**：X老师今天上课讲了前端知识，然后给了大家一个不能按的按钮，小宁惊奇地发现这个按钮按不下去，到底怎么才能按下去呢？

**WP**:
> **[目标]**  
> 初步了解前端知识  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox  
>   
> **[步骤]**  
> 1. 打开开发者工具，在查看器窗口审查元素，发现存在disabled=""字段，  
> 2. 将disabled=""删除后，按钮可按，按下后得到flag  
> 3. 或审计from表单代码，使用hackbar，用post方式传递auth=flag，同样可以获得flag

**总结**：Chrome审查元素栏中双击可进行编辑；表单提交不了的话可以手动发送请求。2020.2.16.Sun

---

# 6. weak_auth
**题目描述**：小宁写了一个登陆验证页面，随手就设了一个密码。

**WP**:
> **[目标]**  
> 了解弱口令，掌握爆破方法  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> burpsuite  
> 字典<https://github.com/rootphantomer/Blasting_dictionary/blob/master/%E5%B8%B8%E7%94%A8%E5%AF%86%E7%A0%81.txt>
> 
> **[步骤]**  
> 1. 随便输入下用户名和密码,提示要用admin用户登入,然后跳转到了check.php,查看下源代码提示要用字典。  
> 2. 用burpsuite截下登录的数据包,把数据包发送到intruder爆破
> 2. 设置爆破点为password
> 3. 加载字典
> 4. 开始攻击，查看响应包列表，发现密码为123456时，响应包的长度和别的不一样.
> 5. 点进去查看响应包，获得flag

**总结**：要常备字典；爆破时找长度和别的响应包不一样的响应。2020.2.20.Thu

---

# 7. simple_php
**题目描述**：小宁听说php是最好的语言,于是她简单学习之后写了几行php代码。

**WP**:
> **[原理]**  
> php中有两种比较符号  
> === 会同时比较字符串的值和类型  
> == 会先将字符串换成相同类型，再作比较，属于弱类型比较  
>   
> **[目地]**  
> 掌握php的弱类型比较  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox  
>   
> **[步骤]**  
> 1. 打开页面，进行代码审计，发现同时满足 $a==0 和 $a 时，显示flag1。
> 2. php中的弱类型比较会使'abc' == 0为真，所以输入a=abc时，可得到flag1。（abc可换成任意字符）。
> 3. is_numeric() 函数会判断如果是数字和数字字符串则返回 TRUE，否则返回 FALSE,且php中弱类型比较时，会使('1234a' == 1234)为真，所以当输入a=abc&b=1235a，可得到flag2。

**总结**：注意PHP的弱类型比较，如
```php
'string'==0
```
为```true```，
```php
'123a'==123
```
为```true```。2020.2.20.Thu

---

# 8. get_post
**题目描述**：X老师告诉小宁同学HTTP通常使用两种请求方法，你知道是哪两种吗？

**WP**: 
> **[原理]**  
> HTTP工作原理  
>   
> **[目的]**  
> 掌握常用http请求方式  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox和插件hackbar  
>   
> **[步骤]**  
> 1. 打开hackbar，用get方式传递a=1，如图所示  
> 2. 勾选hackbar上的Enable post data，用post方式传递b=2，可获得flag，如图所示  

**总结**：如果自己写js脚本的话注意还要
```js
setRequestHeader("Content-Type", "application/x-www-form-urlencoded")
```
否则```Content-Type```的值为```text/plain;charset=UTF-8```。2020.2.21.Fir

---

# 9. xff_referer
**题目描述**：X老师告诉小宁其实xff和referer是可以伪造的。

**WP**:
> **[原理]**  
> X-Forwarded-For:简称XFF头，它代表客户端，也就是HTTP的请求端真实的IP，只有在通过了HTTP 代理或者负载均衡服务器时才会添加该项  
> HTTP Referer是header的一部分，当浏览器向web服务器发送请求的时候，一般会带上Referer，告诉服务器我是从哪个页面链接过来的
> 
> **[目的]**  
> 掌握有关X-Forwarded-For和Referer的知识  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox、burpsuite  
>   
> **[步骤]**  
> 1. 打开firefox和burp，使用burp对firefox进行代理拦截，在请求头添加X-Forwarded-For: 123.123.123.123，然后放包
> 2. 接着继续在请求头内添加Referer: https://www.google.com，可获得flag

**总结**：通过伪造```X-Forwarded-For```伪造客户端IP，通过伪造```Referer```伪造来源。2020.2.21.Fri

---

# 10. webshell
**题目描述**：小宁百度了php一句话,觉着很有意思,并且把它放在index.php里。

**WP**:
> **[目标]**  
> 了解php一句话木马、如何使用webshell  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox、hackbar  
> 蚁剑下载地址[https://github.com/AntSwordProject/antSword/releases](https://github.com/AntSwordProject/antSword/releases)
> 
> **[步骤]**
> 1. 直接提示给了php一句话，可以用菜刀或蚁剑连接
> 2. 连接后在网站目录下发现了flag.txt文件，查看文件可获得flag
> 3. 也可以使用hackbar，使用post方式传递shell=system('cat flag.txt'); 获得flag

**总结**：用curl的话
```sh
curl -d "shell=system('ls');" -X POST http://111.198.29.45:44142/
```
中的```POST```须为大写；PHP命令后的分号不能少。2020.2.22.Sat

---

# 11. command_execution

**题目描述**：小宁写了个ping功能,但没有写waf,X老师告诉她这是非常危险的，你知道为什么吗。

**WP**:
> **[原理]**  
> | 的作用为将前一个命令的结果传递给后一个命令作为输入  
> &&的作用是前一条命令执行成功时，才执行后一条命令  
>   
> **[目地]**  
> 掌握命令拼接的方法  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox  
>   
> **[步骤]**  
> 1. 打开浏览器，在文本框内输入127.0.0.1 | find / -name "flag.txt" （将 | 替换成 & 或 && 都可以）,查找flag所在位置。
> 2. 在文本框内输入 127.0.0.1 | cat /home/flag.txt 可得到flag。

**总结**：原来```find -name```不支持模糊查询。。。
```sh
127.0.0.1;find / -name flag
```
搜不出来，
```sh
127.0.0.1;find / -name flag.txt
```
却可以。2020.2.23.Sun

---

# 12. simple_js

**题目描述**：小宁发现了一个网页，但却一直输不对密码。(Flag格式为 Cyberpeace{xxxxxxxxx} )

**WP**:
> **[原理]**  
> javascript的代码审计  
>   
> **[目地]**  
> 掌握简单的javascript函数  
>   
> **[环境]**  
> windows  
>   
> **[工具]**  
> firefox  
>   
> **[步骤]**  
> 1. 打开页面，查看源代码，可以发现js代码。  
> 2. 进行代码审计，发现不论输入什么都会跳到假密码，真密码位于 fromCharCode 。
> 3. 先将字符串用python处理一下，得到数组[55,56,54,79,115,69,114,116,107,49,50]，exp如下。
s="\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30"  
print (s)
> 4. 将得到的数字分别进行ascii处理，可得到字符串786OsErtk12，exp如下。
> ```
> a = [55,56,54,79,115,69,114,116,107,49,50]
> c = ""
> for i in a:
>     b = chr(i)
>     c = c + b
> print(c)
> ```
> 5. 规范flag格式，可得到Cyberpeace{786OsErtk12}

**总结**：格式化并删除无用变量后的源码如下
```js
function dechiffre(pass_enc) {
    var pass = "\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30";
    var tab = pass_enc.split(',');
    var tab2 = pass.split(',');
    var i, j, k, l = 0, n, p = "";
    i = 0;
    j = tab.length;
    k = j + (l) + (n = 0);
    n = tab2.length;
    for (i = (o = 0); i < (k = j = n); i++) {
        p += String.fromCharCode((o = tab2[i]));
        if (i == 5) break;
    }
    for (i = (o = 0); i < (k = j = n); i++) {
        if (i > 5 && i < k - 1)
            p += String.fromCharCode((o = tab2[i]));
    }
    p += String.fromCharCode(tab2[17]);
    pass = p;
    return pass;
}

String["fromCharCode"](dechiffre("\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30"));

h = window.prompt('Enter password');
alert(dechiffre(h));
```
不是很懂源码中的```String["fromCharCode"]()```是个什么写法；如果是直接改js代码的话要记得把下面的```p += String.fromCharCode(tab2[17])```改成```p += String.fromCharCode(tab2[n-1])```。2020.2.23.Sun