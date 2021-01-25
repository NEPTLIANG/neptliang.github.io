---
layout:       post
title:        "关于斐讯K1刷LEDE的几个小问题"
subtitle:     "迁移自WordPress"
date:         2017-10-28 08:24:56
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - Router
---


听说斐讯原厂固件带后门，所以上车后就想着刷固件，遇到了种种困难，比如各种云盘链接失效。。。断断续续折腾几天后终于搞定，下面来总结一下一些问题与解决方法，供与我一样的小白参考，请大神勿喷

![头图](https://neptliang.files.wordpress.com/2017/10/wp-image-291994055.png?w=551&zoom=2)

# 0x01 关于刷Breed时SSH端口错误

听说（出厂自带新版官方固件的）新的斐讯k1是因为原厂固件的原因无法SSH连接刷Breed，所以请教大神@八寳粥后得到建议：通过恩山大神@tianbaoha的自动工具刷Breed。链接：

[[2017-03-14] K1 K2 傻瓜刷机、自动刷入Breed 华硕Padavan 辅助工具 (v2.1) http://www.right.com.cn/forum/forum.php?mod=viewthread&tid=209423](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=209423)

# 0x02 关于拨号问题

根据教程

[https://www.dreamsafari.info/2016/11/phicomm_k2_with_lede/](https://www.dreamsafari.info/2016/11/phicomm_k2_with_lede/)

拨号上网时，输入以下五条命令执行：

```shell
uci set network.wan.proto=pppoe
uci set network.wan.username=宽带账号
uci set network.wan.password=宽带密码
uci commit
/etc/init.d/network restart
```

执行第五条命令时，不知道是不是我姿势不对，报错：`/etc/init.d/network: not found`

![拨号](https://neptliang.files.wordpress.com/2017/10/wp-image-130585873.jpg?w=551&zoom=2)

据Linux知识，我们知道，这条命令可能是执行该程序。要执行程序，还可以打开所在目录，并执行

```sh
./network restart
```

然后就可以了，可以`ping http://www.baidu.com`检查是否拨号成功

# 0x03 汉化管理页面的问题

据恩山大神的教程：

[http://www.right.com.cn/FORUM/thread-215179-1-1.html](http://www.right.com.cn/FORUM/thread-215179-1-1.html)

可知，要设置管理页面为中文，可SSH执行命令

```bash
opkg install luci-i18n-base-zh-cn
```

或在管理页面的包安装界面搜索并安装luci-i18n-base-zh-cn。但是，我执行的时候报错：`Unknown package`，管理页也搜索不到

![](https://neptliang.files.wordpress.com/2017/10/wp-image-2080771478.png?w=551&zoom=2)

![](https://neptliang.files.wordpress.com/2017/10/wp-image-1717414927.png?w=551&zoom=2)

在@八寳粥大神的指点下，执行：

```bash
opkg update
```

或者在管理页包管理页面点击”Update lists”以更新可用包列表，再安装就可以了。

# 未完待 续


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)