---
layout:       post
title:        "Xorg 手动指定分辨率"
subtitle:     "undefined"
date:         2023-01-19 19:51:23
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - Linux
---


# # `0x00` 背景

*`/*` 在 VirtualBox 装了 Arch + KDE，但是不知道为什么 VBox 增强工具的根据窗口大小自动设置虚拟机分辨率的功能没有效果（虽然把 Machine-Settings-Display-Screen-Graphics Controller 改成另外两个选项之后可以，但是这么改又会闪屏），所以谷歌了半天怎么手动设置分辨率，然后官方维基的说明又有点简略，故在此整理补充一下官方维基的内容 `*/`*

# `0x01` 安装 xorg-xrandr

安装软件包 xorg-xrandr。

* *图形化操作程序：ARandR、LXRandR；命令行前端：xlayoutdisplay*

# `0x02` 测试配置

当没有添加任何选项直接运行时，`xrandr` 列出该系统可用的显示输出设备 (`VGA-1`, `HDMI-1` 等等) 和每一台设备可设置的分辨率，当前分辨率后面带有一个`*`号和一个`+`号:

```sh
xrandr
Screen 0: minimum 320 x 200, current 3200 x 1080, maximum 8192 x 8192
VGA-1 disconnected (normal left inverted right x axis y axis)
HDMI-1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 531mm x 299mm
   1920x1080     59.93 +  60.00*   50.00    59.94  
   1920x1080i    60.00    50.00    59.94  
   1680x1050     59.88  
…
```

# `0x03` 添加有效分辨率

首先，运行`gtf`或者`cvt`，查询某分辨率的有效扫描频率。

```sh
$ cvt 1280 1024

# 1280x1024 59.89 Hz (CVT 1.31M4) hsync: 63.67 kHz; pclk: 109.00 MHz
Modeline "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```

然后通过`--newmode`参数新建一种xrandr模式，输入上面所得到的查询结果，其中Modeline关键词自然需要被省略。

```sh
xrandr --newmode "1280x1024_60.00"  109.00  1280 1368 1496 1712  1024 1027 1034 1063 -hsync +vsync
```

新建模式后，我们需要把这模式添加到当前的输出设备（假定为VGA1）上。由于一些参数已经事先设置，只需输入模式名称即可，即1280x1024_60.00。

```sh
xrandr --addmode VGA1 1280x1024_60.00
```

最后，再把VGA1的分辨率指定为刚刚添加的新模式。

```sh
xrandr --output VGA1 --mode 1280x1024_60.00
```

注意，以上设置同样地只能在当前会话暂时生效。

如果您对所要添加的某分辨率感到不放心，您可以追加新命令“sleep 5”以及一条切换到已有有效分辨率的命令，以保证不会被困在实际无效的分辨率，示例：

```sh
   xrandr --output VGA1 --mode 1280x1024_60.00 && sleep 5 && xrandr --newmode "1024x768-safe" 65.00 1024 1048 1184 1344 768 771 777 806 -HSync -VSync && xrandr --addmode VGA1 1024x768-safe && xrandr --output VGA1 --mode 1024x768-safe
```

其他输出设备如法炮制：VGA1或DVI-I……

# `0x04` 使xrandr所更改的分辨率设置永久生效

使xrandr定制永久生效的方案有：

* `xorg.conf`（推荐）
* `.xprofile`
* kdm/gdm

## 在xorg.conf设置分辨率（推荐）

编辑 `/etc/X11/xorg.conf` 文件，把 `Section "Monitor"` 下的 `Modeline` 选项改为 `cvt` 查询出来的结果

* 添加完成后，如果在KDE中设置成了新分辨率但重启又恢复原样，可以在 `/etc/X11/xorg.conf` 文件 `Section "Monitor"`下的 `Modeline` 选项下新起一行，设置：

    ```conf
    Option          "PreferredMode" "1280x1024_60.00"
    ```

修改后的 `Section "Monitor"` 示例：

```conf
Section "Monitor"
    Identifier      "External DVI"
    Modeline        "1280x1024_60.00"  108.88  1280 1360 1496 1712  1024 1025 1028 1060  -HSync +Vsync
    Option          "PreferredMode" "1280x1024_60.00"
EndSection
```

> **选项“PreferredMode”** *"name"*
> 
> 此可选条目指定要标记为显示器的首选初始模式的模式。（仅限 RandR 1.2 支持的驱动程序）
> 
> ——xorg.conf文件格式手册	


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)