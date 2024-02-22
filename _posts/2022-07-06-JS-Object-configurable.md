---
layout:       post
title:        "JS对象可配置性配置"
subtitle:     "undefined"
date:         2022-07-06 19:23:45
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# 异

## `Object.preventExtensions()`

### 特性
    
* 仅**阻止添加**自身的属性。

### 影响范围
    
* 其对象类型的**原型**依然可以添加新的属性

* 该方法使得目标对象的 `[[prototype]]` 不可变；任何**重新赋值 `[[prototype]]` 操作**都会抛出 `TypeError` 。这种行为只针对内部的 `[[prototype]]` 属性， 目标对象的其它属性将保持可变

## `Object.seal()`

### 特性

* **阻止添加**新属性

* 并将所有现有属性标记为**不可配置**。

    属性不可配置的效果就是属性变得不可删除，以及一个数据属性不能被重新定义成为访问器属性，或者反之。

* 当前属性的值只要原来是可写的就**可以改变**。

### 影响范围

* 不会影响**从原型链上继承的属性**。
* 但 **`__proto__` 属性的值**也会不能修改

## `Object.freeze()`

### 特性

* 不能向这个对象**添加**新的属性
* 不能**删除**已有属性
* 不能**修改**该对象已有属性的可枚举性、可配置性、可写性，以及不能**修改**已有属性的值

### 影响范围

冻结一个对象后该对象的**原型**也不能被重新分配

## 总结

方法                         | 添加 | 修改 | 删除
-----------------------------|-----|------|----
`Object.preventExtensions()`
`Object.seal()`
`Object.freeze()`


# 同

* **返回值**：返回原对象的引用

* **反向操作时**：静默失败或抛出`TypeError`（最常见的情况是strict mode中，但不排除其他情况）

* **对原始类型参数的处理**：在ES5中，如果参数不是一个对象类型（而是原始类型），将抛出一个`TypeError`异常。在ES2015中，非对象参数将被视为一个不可扩展的普通对象，因此会被直接返回

* **通过`Object.defineProperty`添加属性时**：将会报错（不论是不是严格模式中）


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)