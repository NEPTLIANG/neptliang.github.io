---
layout:       post
title:        "Raspberry Pi Zero W USB SSH连接与WiFi配置"
subtitle:     "Null"
date:         2019-08-05 18:19:00
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - RaspberryPi
---
# 0x00 前言

近段时间买了个树莓派zero w，没想到资料如此匮乏，网上大部分教程都是针对3b+等有网口的版本的，或者是用usb转ttl弄的，好不容易找到几个针对zero w的教程我这里却都用不了，由于不想买usb转ttl，肝了好多天、谷歌+百度了几十个教程、查了几十个疑难杂症，最后到学校图书馆借了本《树莓派用户指南》才配置好，现在记录一下

**刷入系统和配置USB SSH根据的是这个教程：**[shumeipai.nxez.com/2018/02/20/raspberry-pi-zero-usb-ethernet-gadget-tutorial.html?variant=zh-cn 【树莓派 Zero USB/以太网方式连接配置教程】](http://shumeipai.nxez.com/2018/02/20/raspberry-pi-zero-usb-ethernet-gadget-tutorial.html?variant=zh-cn)，默认用户名：pi，默认密码：raspberry，默认主机名：raspberrypi.local

原来**数据线和充电线是不一样的**，之前弄了一天，试了三根线，配置改来改去，插电脑就是一点反应都没有，最后换了一根手机数据线，设备管理器里终于出现了（虽然识别成了COM设备）。。。原来是因为我之前用的三根usb线都只是耳机的充电线。。。还有Windows的Linux子系统也有点问题，查了半天看到有位大佬说了才知道**WSL识别不出raspberrypi.local**，可以先用cmd来ping出ip再ssh其ip（出处忘记记了找不到了。。。）
# 0x01 WiFi配置
_//不知道为甚么，我电脑通过usb共享网络给zero却还是上不了网，只好先把wifi配置好_

**使用iwlist扫描周边的无线接入点**，从而检查USB无线网卡是否正常工作（需要root权限）：

```shell
iwlist scan
```

_//如果显示错误信息，例如提示网络或接口已关闭，则需要检查是否安装了正确的固件，或者USB无线网卡连接的是否是供电的USB集线器_

要将树莓派连入无线网络，需要**在/etc/network/interfaces文件的最后加入（需要root权限）：**

```
auto wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa.conf
```

_/*提示：在树莓派上的无线网卡如果是第一个网卡，则名称通常是wlan0，否则最后的数字可能有所不同。使用iwconfig可以查看所有无线网卡，并根据给出的无线网卡信息调整上例中的输入文字_

_上述interfaces文件的最后一行指向配置文件wpa.conf，该文件目前尚不存在。该文件是被wpasupplicant这一Linux下的专用无线网络安全工具所使用的。该工具向Linux提供了一种简单的方式来使用WPA（Wireless Protected Access）加密标准安全接入网络。使用wpasupplicant，你可以让树莓派接入几乎所有的无线网络，不管无线网络是使用WPA还是WPA2也无论是使用AES或TKIP模式，你还可以接入早期使用WEP加密的网络（尽管该工具以wpa开头）。*/_

wpasupplicant创建的wpa.conf文件存放在/etc目录下，配置树莓派的无线接入前，我们**首先新建一个空白文件/etc/wpa.conf（需要root权限），然后输入以下两行，注意替换其中的Your_SSID为无线网络中你实际上要连接的路由器SSID，要加双引号：**

```
network={
    ssid="Your_SSID"
```

**接下来的操作分三种情况：**

(1) 无线网络**不加密时**，再加入下述两行并保存：

```
    key_mgmt=NONE
}
```

(2) 无线网络**使用WEP加密时**，再加入如下几行并保存（请注意将下面的Your_WEP_Key替换成你自己的无线网络WEP加密的ASCII密钥）：

```
    key_mgmt=NONE
    wep_key0="Your_WEP_Key"
}
```

_//提示：WEP加密不安全，易遭破解，不建议使用_

(3) 无线网络**使用WPA/WPA2加密时**，再加入如下几行并保存（WPA2也是写WPA-PSK而不是WPA2-PSK；注意将下面的Your_WPA_Key替换成你自己所在的网络的密码短语口令，要加双引号）：

```
    key_mgmt=WPA-PSK
    psk="Your_WPA_Key"
}
```

现在树莓派无线网络已经配置完毕，**但要到树莓派重启后才能成功启用，不想重启可以使用下述命令（我执行报错，不知道怎么解决，只好重启树莓派；需要root权限）：**

```shell
ifup wlan0
```

几分钟后我的zero连上了wifi

_//参考来源：树莓派项目创立者Eben Upton与Gareth Halfacree所著《树莓派用户指南》5.4_

# 0x02 配置USB SSH
按照上一节配置之后，我又没法通过usb ssh zero了，查了一下看到了这篇教程：[https://www.cnblogs.com/mind000761/p/9413624.html](https://www.cnblogs.com/mind000761/p/9413624.html)，感觉也许是指定了wpa-conf却没有配置usb的网络的问题，只好把内存卡拔下来插到读卡器，启动Manjaro修改配置（我这里Windows大部分情况下认不出rootfs分区，偶尔认出了修改完配置之后也弹不出设备，直接拔读卡器则保存不了修改，而WSL根本认不到内存卡，Manjaro则装了那啥守护进程却仍ssh不上zero）

**在/etc/network/interfaces添加如下几行并保存（需要root权限）：**

```
allow-hotplug usb0
auto usb0
iface usb0 inet dhcp
```

把内存卡插回到zero，接上usb开好机我就又可以ssh了

_//参考来源：[cnblogs.com/mind000761/p/9413624.html 【树莓派Raspberry Pi zero w无线联网实测】 3.2.3](http://www.cnblogs.com/mind000761/p/9413624.html)_

_//End of Article_
