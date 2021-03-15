---
layout:       post
title:        "深克隆与React组件不更新问题"
subtitle:     "null"
date:         2021-01-29 16:46:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


前些天尝试给一个列表添加打开某行中的开关时禁用该行中的链接的功能，但是写完后，调整开关状态时，`console.log`可知存储状态的数组中状态改变了，**界面中的链接状态却没有变化**。项目用的React + MobX，存储状态的数组是类组件内被标记`@observable`的数组属性

请教了前辈，前辈回复道

> 值是发生变化了，视图没有变化，原因是数组是引用方式，对于react来说，它的值都是地址，因为没有被重新赋值（地址没有改变）

他在我修改数组中状态的语句后面，加了一句

```js
this.arrayName = [...this.arrayName]
```

并告诉我

> 这种就是**deep clone**，接下来可以百度，看看深克隆

于是我谷歌了一下，在[小强730730@segmentfault的《javascript深拷贝(deepClone)》（segmentfault.com/a/1190000006687581）](https://segmentfault.com/a/1190000006687581)中了解到

> 实现deepClone时，对于非引用值类型的数值，直接赋值，而**对于引用值类型（object）还需要再次遍历，递归赋值**
> 
> 对于function类型，博主这里是直接赋值的，还是共享一个内存值。这是因为函数更多的是完成某些功能，有个输入值和返回值，而且对于上层业务而言更多的是完成业务功能，并不需要真正将函数深拷贝
>
> 但是function类型要怎么拷贝呢？
>
> 其实博主只想到了用new来操作一下，但是function就会执行一遍，不敢想象会有什么执行结果哦！o(╯□╰)o！其它暂时还没有什么好的想法，欢迎大家指导哦！
>
> 有时候保存了dom元素， 一不小心进行深拷贝，判断Element元素要使用instanceof来判断。因为对于不同的标签，tostring会返回对应不同的标签的构造函数

也就是说，对象直接赋给变量就是浅拷贝，而新建一个地址不同的数组/对象并把元素/属性赋过去是深拷贝


---

***`//End of Article`***

---


# 参考文献

* [小强730730@segmentfault《javascript深拷贝(deepClone)》（segmentfault.com/a/1190000006687581）](https://segmentfault.com/a/1190000006687581)

![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)