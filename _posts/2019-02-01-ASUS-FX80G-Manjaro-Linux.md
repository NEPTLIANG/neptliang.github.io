---
layout:       post
title:        "奸若磐石阿修斯坠机堡垒FX80G安装Manjaro Linux问题总结"
subtitle:     "迁移自WordPress"
date:         2019-02-01 22:12:34
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
	- LinuxInstall
---


*/\*最近整理Chrome书签发现很多以前收藏的书签链接失效了，感觉是时候在博客总结一些问题了*

*给fx80g安装Linux的时候踩到了各种坑，一会显卡驱动不行，一会网卡驱动不行，一会启动不了，一会启动黑屏，一会启动卡死……刚开始想装Arch，启动了才想起没有GUI没法认证校园网（<span style="color: red; font-weight: bold; font-size: 32px; font-style:normal">此处要对寰创GiWiFi讲一句\*nm</span>），遂放弃；又想装回用了一年的Debian，显卡和无线网卡驱动都不行，无线网卡驱动编译安装报错，放弃；改装KDE版Manjaro，装好后开机卡死，百度前几页的Bumblebee等办法试了个遍（谷歌的话……英语不好），无解；又试了Mint等好几个发行版，启动不了等各种问题；最后装了XFCE版Manjaro，干脆禁用独显，才能勉强正常使用，不过用键盘快捷键调屏幕亮度的话XFCE还是会卡死……凡此种种，前后折腾了一个多星期*

*时间也有点久远了，很多细节记得不大清楚，估计会有不少错误之处*

*因为<span style="text-decoration: line-through">懒</span>没必要造轮子，所以很多内容是直接转大佬们的博客的，侵删\*/*

# 0x00 FX80G U盘启动问题

刚开始用旧启动盘启动不了，百度了半天说是要在BIOS里禁用Security-Secure Boot Control并在重启进入BIOS后启用Boot选项卡里的Launch CSM，找了半天找不到，又百度了半天才知道FX80G的新版BIOS里取消了这个选项，只能用UEFI启动盘什么的，重新做了个UEFI启动盘就可以了

*//找不到来源了*
 
# 0x01 刻录镜像方式

刚开始用UltraISO用默认方式刻录U盘，启动后提示`unknow filesystem`，看了贴吧大佬的介绍，改用UltraISO的RAW方式，解决

*//来源：[http://tieba.baidu.com/p/5219191569](http://tieba.baidu.com/p/5219191569)【Manjaro启动盘引导不进什么鬼-Linux吧】2楼[@pengweipro](http://tieba.baidu.com/home/main?un=pengweipro&ie=utf-8&id=c1ee70656e6777656970726fe101&fr=pb&ie=utf-8)*

# 0x02 U盘启动卡死

最开始也是最蛋疼的问题，安装的时候就会卡住，CPU陷入死锁，然后无限循环，这都是显卡驱动的锅。

你可能会遇到以下问题：

```
watchdog: BUG: soft lockup -CPU#1 stuck for 23s![kworker/1:2:239]
Started TLP system startup/shutdown
A start job is running for livemedia mhwd scripe(xxxx)
```

解决办法：首先还是要进入安装界面：

1. 引导盘开机看到启动菜单的时候，用方向键移到BOOT那一栏。
1. 按`E`进入编辑，将 `driver=free` 改成 `driver=intel`
1. 并在其后面加上 `xdriver=mesa acpi_osi=! acpi_osi=”Windows 2009″`
1. 然后按 `Ctrl`+`X` 或者 `F10` 启动。

一般情况下这样就可以进入安装界面了，继续安装，安装好后会重启，但此时也有大概率进不了系统，陷入死锁，解决方法如下：

1. 进入grub界面后，移动到Manjaro的启动项，按`E`，在大概倒数第二行，找到 `quite` ，在其后面添加 `acpi_osi=! acpi_osi=’Windows 2009′` 按`F10`就可以顺利进入系统了。
1. 进入系统后修改grub文件，就可以选择初步解决显卡问题了（最终还是要安装nvidia或intel的驱动）.
1. 打开终端,`sudo nano /boot/grub/grub.cfg`
1. 找到 `linux /boot/vmlinuz-linux root=UUID=38bd539c-692f-44ea-85d6-2155f06f09fc rw quiet` 这一行，大体相同，可能会有点不一样。
1. 在`quite`后面添加 `xdriver=mesa acpi_osi=! acpi_osi=”Windows 2009″`
1. 添加之后的样子 `linux /boot/vmlinuz-linux root=UUID=38bd539c-692f-44ea-85d6-2155f06f09fc rw quiet xdriver=mesa acpi_osi=! acpi_osi=”Windows 2009″`
1. 重启就OK了。

*//来源：[https://blog.csdn.net/Umbrella2B/article/details/84258951](https://blog.csdn.net/Umbrella2B/article/details/84258951)【Manjaro开机黑屏卡住_显卡驱动问题解决及配置源和搜狗输入法安装-CSDN】[@Umbrella2B](https://me.csdn.net/Umbrella2B)*

# 0x03 安装卡在“`Misc postinstall configurations`”

断开网络即可

*//来源：[https://www.manjaro.cn/bbs/topic/%E5%AE%89%E8%A3%85%E5%8D%A1%E5%9C%A893%E4%B8%8D%E5%8A%A8%E4%BA%86](https://www.manjaro.cn/bbs/topic/%E5%AE%89%E8%A3%85%E5%8D%A1%E5%9C%A893%E4%B8%8D%E5%8A%A8%E4%BA%86)【主题：安装卡在93%不动了 | Manjaro Linux 乐于简单】@rainweic*
 
# 0x04 更新软件源提示“无法升级 core (无法锁定数据库)”

*//百度Manjaro找不到解决办法，转而搜Arch的找到了*

更新软件源出现以下故障：

```sh
$ sudo pacman -Syy
[sudo] ivan 的密码：
:: 正在同步软件包数据库...
错误：无法升级 core (无法锁定数据库)
错误：无法升级 extra (无法锁定数据库)
错误：无法升级 community (无法锁定数据库)
错误：无法升级 archlinuxcn (无法锁定数据库)
错误：同步所有数据库失败
```

解决办法
删除文件：/var/lib/pacman/db.lck

```sh
sudo pacman -S sl
# 提示可以删除 /var/lib/pacman/db.lck
sudo rm -rf /var/lib/pacman/db.lck
```

*//来源：[https://segmentfault.com/a/1190000016398451](https://segmentfault.com/a/1190000016398451)【Arch无法更新和安装软件 – 个人文章 – SegmentFault 思否】@lingjiu*


---

***`//未完待Xu`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)