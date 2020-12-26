---
layout:       post
title:        "Python的urllib.request常见用法总结"
subtitle:     "Null"
date:         2020-04-28 23:37:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - Py
---


# 0x00 前言

本文为urllib.request常见用法总结，非完整教程，摘自廖雪峰大佬的博客[liaoxuefeng.com/wiki/1016959663602400/1019223241745024](https://www.liaoxuefeng.com/wiki/1016959663602400/1019223241745024)

# 0x01 import

```py
from urllib import request
```

# 0x02 urlopen方法

打开并发送请求，返回对象

**使用示例**：
```py
f = request.urlopen(url)
```

# 0x03 status属性与reason属性

值分别为状态码和状态

**使用示例**：
```
print(f.status, f.reason)
```

**输出示例**：```200 OK```

# 0x04 getheaders方法

获取响应头，返回键值对列表

**使用示例**：
```py
for key, value in f.getheaders():
    print('%s: %s' % (key, value))
```

**输出示例**：
```
Server: nginx
Date: Tue, 26 May 2015 10:02:27 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 2049
```

# 0x05 read方法

返回响应体

**使用示例**：
```py
data = f.read()
print('Data:', data.decode('utf-8'))
```


_//未完待xu_