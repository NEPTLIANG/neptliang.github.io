---
layout:       post
title:        "JS 对象属性判断与遍历"
subtitle:     "undefined"
date:         2022-06-29 17:13:21
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


API                                     | 原型链属性 | 不可枚举属性 | `Symbol`属性名属性 | 总结
----------------------------------------|----------|------------|------------------|-
**判断：**
`in`                                    | ✅        | ✅         | ✅                | 最宽松，原型链、不可枚举、`Symbol`属性**都返回`true`**
`Object.property.hasOwnProperty`        |           | ✅        | ✅                 | 只有原型链属性会返回`false`，**自身的**不可枚举、`Symbol`属性都返回`true`
`Object.property.propertyIsEnumerable`  |           |           |                    | 最严格，不可枚举、原型链、`Symbol`属性都返回`false`，只有**可枚举的自身非`Symbol`属性**会返回`true`
**遍历：**（全都不返回`Symbol`属性名的属性）
`for...in`                              | ✅        |           |                    | **可枚举**非`Symbol`属性都会返回，不论是自身属性还是原型链属性
`Object.keys()`                         |           |           |                    | 最严格，只返回**可枚举的自身非`Symbol`属性**，不可枚举、原型链、`Symbol`属性都不返回
`Object.getOwnPropertyNames()`          |           | ✅        |                   | **自身的**非`Symbol`属性都会返回，不论是否可枚举

例：

```js
// 声明父类
function parent() {
    // 父类实例属性
    this.parentProp = 'Parent property value';
}
// 父类实例方法
parent.prototype.parentMethod = function() {
    console.log('Parent method called');
};

// 声明子类
function child() {
    // 子类实例方法
    this.childMethod = function() {
        console.log('Child method called');
    };
}
// 设置子类原型
child.prototype = new parent();
// 设置子类构造函数
child.prototype.constructor = child;

let chi = new child();
// 子类实例属性
chi.childProp = 'Child property value';

// 子类实例不可遍历属性
Object.defineProperty(chi, 'notEnumerable', {
    value: 'notEnumerable',
    enumerable: false
})

// 子类实例Symbol属性名属性
let symbol = Symbol('SymbolNameProp');
chi[symbol] = 'Symbol name property value'

// 判断测试
console.log({
    parentIn:           'parentProp' in chi,
    notEnumerableIn:    'notEnumerable' in chi,
    SymbolIn:           symbol in chi
});     // { parentIn: true, notEnumerableIn: true, SymbolIn: true }

console.log({
    parentHasOwnProperty:           Object.prototype.hasOwnProperty.call(chi, 'parentProp'),
    notEnumerableHasOwnProperty:    Object.prototype.hasOwnProperty.call(chi, 'notEnumerable'),
    SymbolHasOwnProperty:           Object.prototype.hasOwnProperty.call(chi, symbol)
});     // { parentHasOwnProperty: false, notEnumerableHasOwnProperty: true, SymbolHasOwnProperty: true }

console.log({
    parentPropertyIsEnumerable: Object.propertyIsEnumerable(chi, 'parentProp'),
    notEnumerableIsEnumerable:  Object.propertyIsEnumerable(chi, 'notEnumerable'),
    SymbolIsEnumerable:         Object.propertyIsEnumerable(chi, symbol)
});     // { parentPropertyIsEnumerable: false, notEnumerableIsEnumerable: false, SymbolIsEnumerable: false }

// 遍历测试
let forInProps = [];
for (prop in chi) { forInProps.push(prop); }

console.log({
    forInProps,
    ObjectDotKeysProps:     Object.keys(chi),
    getOwnPropertyNames:    Object.getOwnPropertyNames(chi)
});     // { ObjectDotKeysProps: [ "childMethod", "childProp" ], forInProps: [ "childMethod", "childProp", "parentProp", "constructor", "parentMethod" ], getOwnPropertyNames: [ 'childMethod', 'childProp', 'notEnumerable' ] }
```


# 属性的可枚举性和所有权 - 内置的判断、访问和迭代方法

## 整理自[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)

<table>
    <tbody>
        <tr>
            <th>作用</th>
            <th colspan="3">自身对象</th>
            <th colspan="3">自身对象及其原型链</th>
            <th>仅原型链</th>
        <tr>
            <td rowspan="2">判断</td>
            <th scope="col">可枚举属性</th>
            <th scope="col">不可枚举属性</th>
            <th scope="col">可枚举属性及不可枚举属性</th>
            <th scope="col">可枚举属性</th>
            <th scope="col">不可枚举属性</th>
            <th scope="col">可枚举属性及不可枚举属性</th>
            <td rowspan="2">需要额外代码实现</td>
        </tr>
        <tr>
            <td>
                <code>
                    <a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable">propertyIsEnumerable</a>
                </code><br>
                <code>
                    <a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty">hasOwnProperty</a>
                </code>
            </td>
            <td>
                <code>
                    <a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty">hasOwnProperty</a>
                </code>&nbsp;获取属性后使用&nbsp;
                <code>
                    <a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable">propertyIsEnumerable</a>
                </code> 过滤可枚举属性
            </td>
            <td>
                <code>
                    <a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty">hasOwnProperty</a>
                </code>
            </td>
            <td>需要额外代码实现</td>
            <td>需要额外代码实现</td>
            <td><code><a href="/zh-CN/docs/Web/JavaScript/Reference/Operators/in">in</a></code></td>
        </tr>
        </td>
        </tr>
        <tr>
            <td rowspan="2">访问</td>
            <th scope="col">可枚举属性</th>
            <th scope="col">不可枚举属性</th>
            <th scope="col">可枚举属性及不可枚举属性</th>
            <td rowspan="2" colspan="3">需要额外代码实现</td>
            <td rowspan="2">需要额外代码实现</td>
        </tr>
        <tr>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys">Object.keys</a></code><br>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames">getOwnPropertyNames</a></code><br>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols">getOwnPropertySymbols</a></code>
            </td>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames">getOwnPropertyNames</a></code>、<code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols">getOwnPropertySymbols</a> </code>获取属性后使用
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable">propertyIsEnumerable</a></code>
                过滤可枚举属性
            </td>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames">getOwnPropertyNames</a></code><br>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols">getOwnPropertySymbols</a></code>
            </td>
        </tr>
        <tr>
        <tr>
            <td rowspan="2">迭代</td>
            <th scope="col">可枚举属性</th>
            <th scope="col">不可枚举属性</th>
            <th scope="col">可枚举属性及不可枚举属性</th>
            <th scope="col">可枚举属性</th>
            <th scope="col">不可枚举属性</th>
            <th scope="col">可枚举属性及不可枚举属性</th>
            <td rowspan="2">需要额外代码实现</td>
        </tr>
        <tr>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys">Object.keys</a></code><br>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames">getOwnPropertyNames</a></code><br>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols">getOwnPropertySymbols</a></code>
            </td>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames">getOwnPropertyNames</a></code>、<code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols">getOwnPropertySymbols</a></code>
                获取属性后使用&nbsp;<code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable">propertyIsEnumerable</a></code>
                过滤可枚举属性
            </td>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames">getOwnPropertyNames</a></code><br>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols">getOwnPropertySymbols</a></code>
            </td>
            <td>
                <code><a href="/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in">for..in</a></code><br>
                （同时会排除 Symbol）
            </td>
            <td>需要额外代码实现</td>
            <td>需要额外代码实现</td>
        </tr>
    </tbody>
</table>


# 通过可枚举性和所有权获取对象的属性

## 整理自[https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)

注：以下实现并非适用于所有情况的最优算法，但可以快捷地展示语言特性。

```js
const SimplePropertyRetriever = {
  /**
   * 根据是否可枚举判断是否保留属性
   * @param param0 {
   *  obj,
   *  prop,
   *  includeByEnumerable: true（只获取可枚举属性）
   *    | false（只获取不可枚举属性）
   *    | undefined（可枚举、不可枚举属性都获取）
   * }
   * @returns Boolean 是否保留该属性
   */
  judgeByEnumerable({ obj, prop, includeByEnumerable }) {
    if (typeof includeByEnumerable === 'undefined') { return true; }
    const enumerable = Object.prototype.propertyIsEnumerable.call(obj, prop);
    return includeByEnumerable ? enumerable : !enumerable;
  },

  /**
   * 获取当前对象及按需递归遍历完原型对象后的对应属性名
   * @param param0 {
   *  obj,
   *  prevProps = [],
   *  iterateSelfBool,
   *  iteratePrototypeBool,
   *  iterateEnumerableBool: true（只获取可枚举属性）
   *    | false（只获取不可枚举属性）
   *    | undefined（可枚举、不可枚举属性都获取）,
   * }
   * @returns Array 当前对象及按需递归遍历完原型对象后的对应属性名
   */
  getProp({
    obj,
    prevProps = [],
    iterateSelfBool,
    iteratePrototypeBool,
    iterateEnumerableBool,
  }) {
    const propsToConcat = [];
    if (iterateSelfBool) {
      Object.getOwnPropertyNames(obj).forEach((prop) => {
        if (prevProps.indexOf(prop) === -1 && this.judgeByEnumerable({
          obj,
          prop,
          includeByEnumerable: iterateEnumerableBool,
        })) {
          propsToConcat.push(prop);
        }
      });
    }
    const prototype = Object.getPrototypeOf(obj);
    if (!iteratePrototypeBool || !prototype) {
      return propsToConcat;
    }
    return propsToConcat.concat(this.getProp({
      obj: prototype,
      pervProps: prevProps.concat(propsToConcat),
      iterateSelfBool: true,
      iteratePrototypeBool,
      iterateEnumerableBool,
    }));
  },

  /**
   * 通过可枚举性和所有权获取对象的属性
   * @param param0 {
   *  obj,
   *  prevProps = [],
   *  iterateSelfBool,
   *  iteratePrototypeBool,
   *  iterateEnumerableBool: true（只获取可枚举属性）
   *    | false（只获取不可枚举属性）
   *    | undefined（可枚举、不可枚举属性都获取）,
   * }
   * @returns Array 通过可枚举性和所有权获取到的对象的属性
   */
  // Inspired by http://stackoverflow.com/a/8024294/271577
  getPropertyNames({
    obj,
    iterateSelfBool,
    iteratePrototypeBool,
    iterateEnumerableBool,
  }) {
    const props = [];
    return this.getProp({
      obj,
      pervProps: props,
      iterateSelfBool,
      iteratePrototypeBool,
      iterateEnumerableBool,
    });
  },
};
```


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)