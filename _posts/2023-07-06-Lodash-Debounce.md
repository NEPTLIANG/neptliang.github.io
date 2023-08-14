---
layout:       post
title:        "lodash debounce 源码阅读笔记"
subtitle:     "undefined"
date:         2023-07-06 16:07:54
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - FrontEnd
---


# `0x00` 前言

复杂的地方主要是 `debounce` 的主流程部分，故 `cancel`、`flush`、`pending` 以及 `throttle` 部分大部分都略过

完整源码可参考[该 PR：https://github.com/NEPTLIANG/lodash-source-code-interpretation/pull/1/files](https://github.com/NEPTLIANG/lodash-source-code-interpretation/pull/1/files)


# `0x01` 源码及注释 

## 导入及文档注释

首先是引用了一个 `isObject` 函数和一个 `root` 常量：

```JS
import isObject from './isObject.js'
import root from './.internal/root.js'    //指向全局对象
```

`isObject` 自然是字面意思，`root` 是指向全局对象的。这两个最后再简析下

然后是文档注释，合起来机翻一手并分割对应到各行

```JS
/**
 * Creates a debounced function that delays invoking `func` until after `wait`
 * 创建一个debound函数，该函数将延迟调用' func '，直到上次调用debound函数后的' wait '毫秒之后，
 * milliseconds have elapsed since the last time the debounced function was
 * invoked, or until the next browser frame is drawn. The debounced function
 * 或者直到绘制下一个浏览器帧。debented函数
 * comes with a `cancel` method to cancel delayed `func` invocations and a
 * 带有一个“cancel”方法来取消延迟的“func”调用和一个
 * `flush` method to immediately invoke them. Provide `options` to indicate
 * “flush”方法来立即调用它们。提供' options '来指示
 * whether `func` should be invoked on the leading and/or trailing edge of the
 * ' func '是否应该在' wait '超时的前沿和/或后沿调用。
 * `wait` timeout. The `func` is invoked with the last arguments provided to the
 * ' func '被使用提供给debound函数的上一个参数来调用。
 * debounced function. Subsequent calls to the debounced function return the
 * 对 debounced 函数的后续调用将返回
 * result of the last `func` invocation.
 * 最后一次' func '调用的结果。
 *
 * **Note:** If `leading` and `trailing` options are `true`, `func` is
 * **注意:**如果' leading '和' trailing '选项为' true '，则' func '
 * invoked on the trailing edge of the timeout only if the debounced function
 * 只在' wait '超时期间多次调用debounced函数时才会在超时的后缘调用。
 * is invoked more than once during the `wait` timeout.
 *
 * If `wait` is `0` and `leading` is `false`, `func` invocation is deferred
 * 如果 wait 为 0 且 leading 为 false，则 ' func '调用将被延迟
 * until the next tick, similar to `setTimeout` with a timeout of `0`.
 * 到 the next tick，类似于 timeout 为 0 的 setTimeout。
 *
 * If `wait` is omitted in an environment with `requestAnimationFrame`, `func`
 * 如果在使用requestAnimationFrame的环境中忽略了' wait '， ' func '
 * invocation will be deferred until the next frame is drawn (typically about
 * 调用将被延迟到绘制下一帧(通常约16ms)。
 * 16ms).
 *
 * See [David Corbacho's article](https://css-tricks.com/debouncing-throttling-explained-examples/)
 * 参见[David Corbacho的文章](https://css-tricks.com/debouncing-throttling-explained-examples/)
 * for details over the differences between `debounce` and `throttle`.
 * ，了解' debounce '和' throttle '之间的区别。
 * 
 * 创建一个debound函数，该函数将延迟调用' func '，直到上次调用debound函数后的' wait '毫秒之后，
 * 或者直到绘制下一个浏览器帧。debented函数
 * 带有一个“cancel”方法来取消延迟的“func”调用和一个
 * “flush”方法来立即调用它们。提供' options '来指示
 * ' func '是否应该在' wait '超时的前沿和/或后沿调用。
 * ' func '使用提供给debound函数的最后一个参数来调用。
 * 对该函数的后续调用将返回
 * 最后一次' func '调用的结果。
 * 
 * **注意:**如果' leading '和' trailing '选项为' true '，则' func '
 * 只在' wait '超时期间多次调用debounced函数时才会在超时的后缘调用。
 * 
 * 如果' wait '为' 0 '、' leading '为' false '， ' func '调用将被延迟
 * 到下一次滴答，类似于' setTimeout '的超时为' 0 '。
 * 
 * 如果在使用requestAnimationFrame的环境中忽略了' wait '， ' func '
 * 调用将被延迟到绘制下一帧(通常约16ms)。
 * 
 * 参见[David Corbacho的文章](https://css-tricks.com/debouncing-throttling-explained-examples/)
 * ，了解' debounce '和' throttle '之间的区别。
 *
 * @since 0.1.0
 * @category Function
 * @param {Function} func The function to debounce.
 * @param {number} [wait=0]
 *  The number of milliseconds to delay; if omitted, `requestAnimationFrame` is
 *  used (if available).
 * @param {Object} [options={}] The options object.
 * @param {boolean} [options.leading=false]
 *  Specify invoking on the leading edge of the timeout.
 *  指定在超时的前沿上调用。
 * @param {number} [options.maxWait]
 *  The maximum time `func` is allowed to be delayed before it's invoked.
 *  ' func '被调用前允许延迟的最大时间。
 * @param {boolean} [options.trailing=true]
 *  Specify invoking on the trailing edge of the timeout.
 *  指定在超时后缘上调用。
 * @returns {Function} Returns the new debounced function.
 * @example
 *
 * // Avoid costly calculations while the window size is in flux.
 * // 避免在窗口大小变化时进行昂贵的计算。
 * jQuery(window).on('resize', debounce(calculateLayout, 150))
 *
 * // Invoke `sendMail` when clicked, debouncing subsequent calls.
 * // 单击时调用' sendMail '，debouncing 后续调用。
 * jQuery(element).on('click', debounce(sendMail, 300, {
 *   'leading': true,
 *   'trailing': false
 * }))
 *
 * // Ensure `batchLog` is invoked once after 1 second of debounced calls.
 * // 确保 `batchLog` 在 1 秒的 debounced 调用后被调用一次。
 * const debounced = debounce(batchLog, 250, { 'maxWait': 1000 })
 * const source = new EventSource('/stream')
 * jQuery(source).on('message', debounced)
 *
 * // Cancel the trailing debounced invocation.
 * // 取消尾随的 debounced 调用。
 * jQuery(window).on('popstate', debounced.cancel)
 *
 * // Check for pending invocations.
 * // 检查挂起的调用。
 * const status = debounced.pending() ? "Pending..." : "Ready"
 */
```

## 声明 `debounce` 函数及闭包需要的变量

```JS
function debounce(func, wait, options) {
  let lastArgs,   // 传给被封装函数的参数，debounced 每次触发都刷新，trailingEdge、invokeFunc 里清除
    // 封装后函数的 this 指向
    lastThis,
    // options.maxWait 与传入的 wait 中的较大值
    maxWait,
    // 传入的被封装函数 func 的调用结果，只有 invokeFunc 调用 func 后会刷新，不会清空
    result,
    // 后缘定时器，leadingEdge 每次执行都赋值，主流程只有 trailingEdge 执行时会清空
    timerId,
    // 返回的封装后函数 debounced 的上次触发时间，每次触发都刷新，只有第一次触发才为空，主流程不再清空
    lastCallTime

  // `maxWait` timer
  let lastInvokeTime = 0
  /* options.leading，时间戳已超时且定时器不存在时，封装后的函数 debounced 被触发后
    是否立即调用被封装函数 func */
  let leading = false
  // options.trailing
  let trailing = true
  // options 参数中是否指定了 maxWait 属性
  let maxing = false
```


## 校验并转换参数类型

```JS
  // Bypass `requestAnimationFrame` by explicitly setting `wait=0`.
  // 是否 wait 为空且 requestAnimationFrame 可用
  const useRAF = (!wait && wait !== 0 && typeof root.requestAnimationFrame === 'function')

  if (typeof func !== 'function') {
    throw new TypeError('Expected a function')
  }
  wait = +wait || 0
  // import isObject from './isObject.js'
  if (isObject(options)) {
    leading = !!options.leading
    maxing = 'maxWait' in options
    maxWait = maxing ? Math.max(+options.maxWait || 0, wait) : maxWait
    trailing = 'trailing' in options ? !!options.trailing : trailing
  }
```


## 声明用于调用 `func` 的 `invokeFunc`

```JS
  /**
   * 调用传入的被封装函数 func，清空传给被封装函数的参数、封装后函数的 this 指向
   * @param {number} time 
   * @returns 传入的被封装函数 func 的调用结果
   */
  function invokeFunc(time) {
    // 获取传给被封装函数的参数
    const args = lastArgs
    // 获取封装后函数的 this 指向
    const thisArg = lastThis

    // 清空传给被封装函数的参数、封装后函数的 this 指向
    lastArgs = lastThis = undefined
    lastInvokeTime = time
    // 使用传给被封装函数的参数调用传入的被封装函数 func
    result = func.apply(thisArg, args)
    return result
  }
```

## 声明用于设置计时器的 `startTimer`

```JS
  /**
   * setTimeout / requestAnimationFrame
   * @param {Function} pendingFunc 定时器到期后的回调
   * @param {number} wait 封装函数传入的 wait
   * @returns {number} timer ID
   */
  function startTimer(pendingFunc, wait) {
    // 没传 wait 且 requestAnimationFrame 可用时，采用requestAnimationFrame
    if (useRAF) {
      root.cancelAnimationFrame(timerId)
      return root.requestAnimationFrame(pendingFunc)
    }
    return setTimeout(pendingFunc, wait)
  }
```


## 声明重开计时器时用于计算剩余等待时长的 `remainingWait`

```JS
  /**
   * 定时器回调中重开定时器时计算剩余等待时长
   * @param {number} time 返回的封装后函数 debounced 的触发时间
   * @returns {Number} 剩余等待时长
   */
  function remainingWait(time) {
    const timeSinceLastCall = time - lastCallTime
    const timeSinceLastInvoke = time - lastInvokeTime
    const timeWaiting = wait - timeSinceLastCall

    return maxing   //如果 options 参数中指定了 maxWait 属性
      ? Math.min(timeWaiting, maxWait - timeSinceLastInvoke)
      : timeWaiting
  }
```


## 声明用于根据时间戳判断是否到时候调用 `func` 的 `shouldInvoke`

```JS
  /**
   * 判断时间戳是否到时候调用传入的被封装函数 func
   * @param {number} time 封装后函数 debounced 的触发时间
   * @returns {boolean} 是否到时候调用被封装函数 func
   */
  function shouldInvoke(time) {
    const timeSinceLastCall = time - lastCallTime
    const timeSinceLastInvoke = time - lastInvokeTime

    // Either this is the first call, activity has stopped and we're at the
    // 要么这是第一次调用，活动已经停止，我们处于
    // trailing edge, the system time has gone backwards and we're treating
    // 后缘，系统时间已经倒退，我们将其视为
    // it as the trailing edge, or we've hit the `maxWait` limit.
    // 后缘，要么我们已经达到了' maxWait '限制。
    return (
      lastCallTime === undefined    //若是首次触发封装后的函数 debounced，则直接根据 leading 判断是否调用被封装函数 func
        || (timeSinceLastCall >= wait)
        || (timeSinceLastCall < 0)
        || (maxing && timeSinceLastInvoke >= maxWait)
    )
  }
```


## 声明定时器到期后的回调 `timerExpired`

```JS
  /**
   * 定时器到期后的回调
   * @returns {unknown}
   */
  function timerExpired() {
    const time = Date.now()
    if (shouldInvoke(time)) {   //如果执行定时器回调时时间戳超时了（说明定时器等待期间 debounced 没有被触发），就有条件地调用传入的被封装函数 func
      return trailingEdge(time)
    }
    // 如果时间戳还没超时（说明定时器等待期间 debounced 还被触发过），就 setTimeout，等到时间戳超时了再有条件地调用被封装函数 func
    // Restart the timer.
    timerId = startTimer(timerExpired, remainingWait(time))
  }
```


## 声明防抖前沿的操作 `leadingEdge`

```JS
  /**
   * debounced 被触发时，如果时间戳到时间调用函数且定时器不存在，setTimeout 并根据 options.leading 
   * 判断是否立即调用传入的被封装函数 func
   * @param {number} time 返回的封装后函数 debounced 的触发时间
   * @returns `options.leading ? 调用函数返回的结果 : 上次调用返回的结果`
   */
  function leadingEdge(time) {
    // Reset any `maxWait` timer.
    lastInvokeTime = time   // /*?*/ 疑问：不明白为何要在 leadingEdge 里刷新 lastInvokeTime。 答：为了在 leading: false 的时候也能正确判断是否 shouldInvoke，如果只在 invokeFunc 里刷新，则 leading: false 时即使第二次触发还没到 maxWait，time - lastInvokeTime 也可认为必然 > maxWait，shouldInvoke 也会返回 true
    // Start the timer for the trailing edge.
    // 启动后缘定时器。
    timerId = startTimer(timerExpired, wait)
    // Invoke the leading edge.
    // 调用前缘。
    return leading ? invokeFunc(time) : result
  }
```


## 声明防抖后沿操作 `trailingEdge`

```JS
  /**
   * 定时器回调执行时时间戳超时的操作，清除定时器ID、保存的参数与 this 指向，
   * 有条件地调用传入的被封装函数 func，否则返回之前的调用结果
   * @param {number} time 定时器回调执行时的时间戳
   * @returns 传入的被封装函数 func 调用结果或之前的调用结果
   */
  function trailingEdge(time) {
    // 后缘清除定时器ID
    timerId = undefined

    // Only invoke if we have `lastArgs` which means `func` has been
    // debounced at least once.
    // 只有当我们有' lastArgs '时才调用，这意味着' func '已经至少被
    // debounced一次。
    if (trailing && lastArgs) {   //? 疑问：为何要判断 lastArgs
      return invokeFunc(time)
    }
    /* 如果 options.trailing 没被指定为 falsy 值，或 lastArgs 为空，则清空
      传给被封装函数的参数、封装后函数的 this 指向，并返回之前的被封装函数 func 的调用结果 */
    lastArgs = lastThis = undefined
    return result
  }
```


## 声明挂到 `debounced` 函数上的一些其他方法

1. `cancel`

    ```JS
      function cancelTimer(id) {
        if (useRAF) {
          return root.cancelAnimationFrame(id)
        }
        clearTimeout(id)
      }

      function cancel() {
        if (timerId !== undefined) {
          cancelTimer(timerId)
        }
        lastInvokeTime = 0
        lastArgs = lastCallTime = lastThis = timerId = undefined
      }
    ```

2. `flush`

    ```JS
      function flush() {
        return timerId === undefined ? result : trailingEdge(Date.now())
      }
    ```

3. `pending`

    ```JS
      function pending() {
        return timerId !== undefined
      }
    ```


## 声明封装操作返回的函数 `debounced`

其中可分为以下几个部分：

1. 根据时间戳与是否首次触发判断是否该调用 `func` 了，并刷新闭包里的各个变量

    ```JS
      /**
       * 封装操作返回的函数
      * @param  {...any} args 传给被封装函数的参数
      * @returns {Function} 返回封装好的函数
      */
      function debounced(...args) {
        // 封装后函数 debounced 的触发时间
        const time = Date.now()
        // 时间戳是否到时间调用被封装函数 func
        const isInvoking = shouldInvoke(time)

        // 获取传给被封装函数的参数
        lastArgs = args
        // 获取封装后函数的 this 指向
        lastThis = this
        // 刷新封装后函数 debounced 的上次触发时间
        lastCallTime = time

        if (isInvoking) {   
    ```

2. `leadingEdge` 部分的逻辑

    ```JS
          /* leadingEdge部分：
            如果时间戳到时间调用被封装函数 func，且不存在定时器（
              意味着：
                1. 封装返回的函数 debounced 首次被触发，
                2. 或 trailingEdge 执行过
            ），则 setTimeout 并根据 options.leading 判断是否立即调用传入的被封装函数 func */
          if (timerId === undefined) {    
            return leadingEdge(lastCallTime)
          }
    ```

3. 节流部分的逻辑

    ```JS      
          /* 节流部分：
            如果时间戳到时间调用被封装函数 func，但定时器还存在，（意味着是节流的判断结果为 true）
            且 options 中指定了 maxWait，
            则以 wait 时长 setTimeout 刷新定时器，并立即调用一次被封装函数 func */
          if (maxing) {   
            // Handle invocations in a tight loop.
            // 在一个紧密循环中处理调用。
            timerId = startTimer(timerExpired, wait)
            return invokeFunc(lastCallTime)
          }
          // 如果进了这个块但是没进上面两个块，则属于系统时间回拨情况，下面的 if 也不会符合，直接到 return
    ```

4. `debounced` 函数的 `cancel` 方法被调用过后再次触发 `debounced` 的情况处理

    ```JS
        }
        /* 如果时间戳还没到时间调用被封装函数 func，且不存在定时器，（
          意味着定时器回调调用过 trailingEdge，且封装返回的函数 debounced 不是首次被触发，
          即 trailing 被 cancel 掉了
        ）就 setTimeout */
        if (timerId === undefined) {    
          timerId = startTimer(timerExpired, wait)
        }
    ```

5. 还没到点调用 `func` 或系统时间回拨情况下都直接 `return result`

    ```JS    
        /* 如果时间戳还没到时间调用被封装函数 func 或（
          时间戳到了时间调用 func，且定时器存在且 options 中没指定 maxWait，即系统时间回拨
        ），就直接返回之前的调用结果  */
        return result   
      }
    ```

6. 把几个方法挂到 `debounced` 函数上，并返回之

    ```JS  
      debounced.cancel = cancel
      debounced.flush = flush
      debounced.pending = pending
      return debounced
    }

    export default debounce
    ```


# `0x02` 流程图

把源码读了一遍之后还是云里雾里的，于是照着源码大致画了个流程图

![流程图](https://neptliang.github.io/img/in-post/post-lodash-debounce/flow.png)

大概可以看出整个流程基本可以大致分为三个部分：

1. **`debounced` 函数被触发时**，对 **`leadingEdge`** 的调用（图中**蓝色**部分）
2. **计时器到期后**（`timerExpired` 被调用时），对 **`trailingEdge`** 的调用（图中**绿色**部分）
3. **以上两部分逻辑**对 **`invokeFunc` 等通用方法**的调用（图中**黄色**部分）


# `0x03` 时序图（大概

照着源码画了流程图后还是有点困惑，于是画了几个类似时序图的图，分别分析了 `leading` 与 `trailing` 的三种情况组合。（由于每次触发都会刷新 `lastCallTime` 和 `lastArgs`，所以图里没写）

1. `leading` 和 `trailing` 都为 `true`

    ![leading 和 trailing 都为 true](https://neptliang.github.io/img/in-post/post-lodash-debounce/2true.png)

    可知
    * **连续触发**的时候
      * **前沿和后沿都会调用 `func`**（蓝底部分）
      * 中间的触发全部略过、定时器到期回调也全是 `setTimeout` 到时间戳到期
    * **只触发一次**的时候，
      * **只有前沿会调用 `func`**
      * 后沿因为前沿清空了 `lastArgs` 不再调用，不知是何用意
      
2. `leading: false, trailing: true`：后沿调用，即默认行为

    ![leading: false, trailing: true](https://neptliang.github.io/img/in-post/post-lodash-debounce/trailing.png)

    可知无论 `leading` 是 `true` 还是 `false`
    * 前沿都会调用 `leadingEdge`，都会 `startTimer`
    * 只是为 `false` 时不调用 `func`

3. `leading: true, trailing: false`：前沿调用

    ![leading: true, trailing: false](https://neptliang.github.io/img/in-post/post-lodash-debounce/leading.png)

    可知无论 `trailing` 是 `true` 还是 `false`
    * 后沿也都会调用 `trailingEdge`，都会清空 `timerId`、`lastArgs`、`lastThis`
    * 只是为 `false` 时不调用 `func`

  
# `0x04` 思维导图（大概

画时序图分析过后又画了个脑图整理了下流程，因为有合并子节点的操作，所以采用表格形式：

![脑图](https://neptliang.github.io/img/in-post/post-lodash-debounce/mind-map.png)

黄的是条件的分支，红绿蓝的是各步操作，灰的是所属场景的总结


# `0x05` 源码的思维导图

最后画了个脑图整理了下源码的结构

![真·脑图](https://neptliang.github.io/img/in-post/post-lodash-debounce/mind-map-code.png)


# `0x06` 简析 `throttle` 

`debounce` 主流程理清之后，`throttle` 的逻辑也就比较清晰了，VS Code 中跟踪各个 `throttle` 相关的配置项可以知道，`throttle` 主要是增加了：

1. `leadingEdge` 与 `invokeFunc` 中刷新 `lastInvokeTime`
1. 判断 `shouldInvoke` 时候在对 `timeSinceLastCall` 与 `wait` 比较的基础上增加对 `timeSinceLastInvoke` 与 `maxWait` 的比较
1. 计算 `remainingWait` 时候取 `wait - timeSinceLastCall` 与 `maxWait - timeSinceLastInvoke` 中的较小者
4. `debounced` 中添加 `timeSinceLastInvoke` 超时情况对应的处理逻辑：`if (isInvoking)` 中的 `if (maxing)` 部分


# `0x07` 简析引入的 `isObject`、`root`

## `isObject`

1. 先排除 `null`
2. 然后判断 `typeof` 返回的是不是 `object` 或 `function`

```JS
function isObject(value) {
  const type = typeof value
  return value != null && (type === 'object' || type === 'function')
}
```

## `root`

1. .internal/root.js:

    ```JS
    const freeGlobal = typeof global === 'object' && global !== null && global.Object === Object && global

    export default freeGlobal
    ```

2.  .internal/root.js

    ```JS
    import freeGlobal from './freeGlobal.js'

    /** Detect free variable `globalThis` */
    const freeGlobalThis = typeof globalThis === 'object' && globalThis !== null && globalThis.Object == Object && globalThis

    /** Detect free variable `self`. */
    const freeSelf = typeof self === 'object' && self !== null && self.Object === Object && self

    /** Used as a reference to the global object. */
    const root = freeGlobalThis || freeGlobal || freeSelf || Function('return this')()
    ```

即依次对 

1. `globalThis`、
2. `global`、
3. `self` 

进行以下两步判断：

1. 是对象且不是 `null`
2. 其 `Object` 属性指向全局作用域下的对象构造函数 `Object`

一旦遇到满足条件的就返回之，否则返回一个新构建的函数里返回的 `this`


# `0x08` 存在的疑问

一行行读过源码、画图分析过流程与行为，问过 New Bing 和 ChatGPT 3.5，还是有几个问题整不明白，故先在此记录下：

1. 不是很明白为什么 `trailingEdge` 在 `invokeFunc` 前要判断 `lastArgs`
2. 不是很明白为什么 `trailingEdge` 一定要清除 `lastArgs` 而 `leadingEdge` 只有执行 `func` 才清除


# `0xff` 参考资料

1. lodash@GitHub《lodash》（[https://github.com/lodash/lodash](https://github.com/lodash/lodash)）
2. 梁王@掘金《每日源码分析 - lodash(debounce.js和throttle.js)》（[https://juejin.cn/post/6844903536157786120](https://juejin.cn/post/6844903536157786120)）
3. 鹤云云@掘金《lodash源代码解析—— debounce & throttle》（[https://juejin.cn/post/6934149265153343496](https://juejin.cn/post/6934149265153343496)）
4. HeftyKoo@GitHub《lodash源码分析之debounce》（[https://github.com/HeftyKoo/pocket-lodash/issues/174](https://github.com/HeftyKoo/pocket-lodash/issues/174)）


---

***`//End of Article`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)