---
layout:       post
title:        "《算法导论》重点总结与JS实现"
subtitle:     "undefined"
date:         2025-02-21 21:43:20
author:       "NeptLiang"
header-img:   "img/home-bg.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - Algorithm
---


# 第2章 算法基础

## 2.1 插入排序

对于少量元素的排序，它是一个有效的算法。

### 伪代码

```rust
INSERTION-SORT(A)
    for j = 2 to A.length
        key = A[j]
        // Insert A[j] into the sorted sequence A[1..j-1].
        i = j - 1
        while i > 0 and A[i] > key
            A[i+1] = A[i]
            i = i - 1
        A[i+1] = key
```

<!-- ### 复杂度

该算法原址排序输入的数 -->

### JS实现

见[本文《8.4 桶排序》小节的JS实现](#js实现-3)


# 第8章 线性时间排序

## 8.2 计数排序

### 伪代码

* 设输入元素中的每一个都是在0到**k**区间内的一个整数
* 设输入是一个数组 **A[1..n]**
* 我们还需要两个数组，**B[1..n]** 存放排序的输出，C[0..k] 提供临时存储空间

```rust
COUNTING-SORT(A, B, k)
    let C[0..k] be a new array
    for i = 0 to k
        C[i] = 0
    for j = 1 to A.length
        C[A[j]]=C[A[j]]+1
    // C[i] now contains the number of elements equal to i.
    for i = 1 to k
        C[i]=C[i]+C[i-1]
    // C[i] now contains the number of elements less than or equal to i.
    for j = A.length downto 1
        B[C[A[j]]]=A[j]
        C[A[j]]=C[A[j]]-1
```

### 复杂度

当 k=O(n) 时，我们一般会采用计数排序，这时的运行时间为 **Θ(n)**。

### 稳定性

稳定

### JS实现


```JavaScript
// 力扣274. H 指数
/**
 * 计数排序
 * @param {number[]} input 输入的数组
 * @param {object} param1 选项
 *  - {boolean} descending 是否降序排序
 * @returns {number[]} 排序后的数组
 */
const countingSort = (input, {
    descending
} = {}) => {
    const len = input.length;
    const map = new Array(len + 1).fill(0);
    const result = new Array(len + 1);
    input.forEach(item => typeof map[item] !== 'undefined' ? 
        map[item]++ 
        : 
        map[item] = 1
    );
    for (let index = 1, len = map.length; index < len; index++) {
        typeof map[index] !== 'undefined' ? 
            map[index] += map[index - 1]
            :
            map[index] = map[index - 1];
    }
    input.forEach(item => {
        const index = descending ? 
            len - (map[item] - 1)
            :
            map[item];
        result[index] = item;
        map[item]--;
    });
    result.shift();
    return result;
}
```

## 8.3 基数排序

### 伪代码

设n个**d**位的元素存放在数组**A**中，其中**第1位是最低位，第d位是最高位**

```rust
RADIX-SORT(A, d)
    for i = 1 to d
        use a stable sort to sort array A on digit i
```

### 复杂度

**引理 8.4** 给定n个**b**位数和任何正整数**r**<=b，如果RADIX-SORT使用的稳定排序算法对数据取值区间是0到k的输入进行排序耗时Θ(n+k)，那么它就可以在Θ((b/r)(n+2^r))时间内将这些数排好序。

通常情况，如果b=O(lgn)，而且我们选择r≈lgn，则基数排序的运行时间为**Θ(n)**。这一结果看上去要比快速排序的期望运行时间代价Θ(nlgn)更好一些。但是，在这两个表达式中，隐含在Θ符号背后的常数项因子是不同的。在处理的n个关键字时，尽管基数排序执行的循环轮数会比快速排序要少，但每一轮它所耗费的时间要长得多。哪一个排序算法更合适依赖于具体实现和底层硬件的特性（例如，快速排序通常可以比基数排序更有效地使用硬件的缓存），以及输入数据的特征。

利用计数排序作为中间稳定排序的基数排序不是原址排序，而很多Θ(nlgn)时间的比较排序是原址排序。因此，当主存的容量比较宝贵时，我们可能会更倾向于像快速排序这样的原址排序算法。

### JS实现

```JavaScript
// 力扣2343. 裁剪数字后查询第 K 小的数字
/**
 * 补零
 * @param {Array} list 列表
 * @param {object} options 选项
 *  - {function} getKey 如果有卫星数据则通过该函数获取关键词
 *  - {function} setKey 修改关键词
 * @returns {object} 填充后的列表与列表项最大长度
 */
const padStrings = (list, {
    getKey,
    setKey,
} = {}) => {
    if (!Array.isArray(list)) { return { list }; }
    const hasSatelliteData = typeof getKey === 'function'
        && typeof setKey === 'function';
    const keys = hasSatelliteData ?
        list.map(element => getKey(element))
        :
        list;
    const maxLen = keys[1].length;      //所有 nums[i].length 的长度相同，所以不逐个判断了。记得跳过 keys[0]
    return {
        list: hasSatelliteData ? 
            list.map((element, index) => {
                setKey(
                    element,
                    String(keys[index])
                        ?.padStart?.(maxLen, '0')
                );
                return element;
            })
            :
            list.map(string => string.padStart?.(maxLen, '0')),
        maxLen,
    };
};

/**
 * 计数排序
 * @param {Array} array 列表
 * @param {object} param1 选项
 *  - {function} getKey 如果有卫星数据则通过该函数获取关键词
 * @returns {Array} 排序后的数组
 */
const countingSort = (array, {
    getKey
} = {}) => {
    if (!Array.isArray(array)) { return array; }
    const hasSatelliteData = typeof getKey === 'function';
    const count = new Array(array.length + 1).fill(0);
    array.forEach(element => {
        const key = hasSatelliteData ?
            getKey(element) : element;
        return typeof count[key] !== 'undefined' ?
            count[key]++
            :
            count[key] = 1
    });
    for (let index = 1, length = count.length; index <= length; index++) {
        typeof count[index] !== 'undefined' ?
            count[index] += count[index - 1]
            :
            count[index] = count[index - 1];
    }
    const result = [];
    array.reduceRight((_, element) => {
        const key = hasSatelliteData ? getKey(element) : element;
        result[count[key]--] = element;
    }, 0);
    return result;
}

/**
 * 基数排序
 * @param {Array} array 列表
 * @param {object} options 选项
 *  - {function} getKey 如果有卫星数据则通过该函数获取关键词
 *  - {function} setKey 设置关键词
 *  - {function} getRoundResult 获取每轮按位排序的结果
 * @returns {Array} 排序后的数组
 */
const radixSort = (array, {
    getKey,
    setKey,
    getRoundResult,
}) => {
    const getDigitGetter = index => (
        num => typeof getKey === 'function' && typeof setKey === 'function' ?
            getKey(num)?.[index]
            :
            num[index]
    );
    const {
        list: paddedNums,
        maxLen,
    } = padStrings(array, {
        getKey,
        setKey,
    });
    let sortedNums = paddedNums;
    for (let index = maxLen - 1; index >= 0; index--) {
        const getKey = getDigitGetter(index);
        sortedNums = countingSort(sortedNums, getKey);
        getRoundResult?.(maxLen - 1 - index, sortedNums);
    }
    return sortedNums;
}
```

## 8.4 桶排序

### 伪代码

<!-- 设输入是由一个随机过程产生，该过程将元素均匀、独立地分布在[0, 1)区间上 -->

设输入是一个包含n个元素的数组**A**，且每个元素A[i]满足0<=A[i]<1。此外，算法还需要一个临时数组B[0..n-1]来存放链表（即桶）

```rust
BUCKET-SORT(A)
    n = A.length
    let B[0..n-1] be a new array
    for i = 0 to n-1
        make B[i] an empty list
    for i = 1 to n
        insert A[i] into list B[⌊nA[i]⌋]
    for i = 0 to n-1
        sort list B[i] with insertion sort
    concatenate the list B[0],B[1],...,B[n-1] together in order
```

### 复杂度

桶排序（bucket sort）设输入数据服从均匀分布，平均情况下它的时间代价为**O(n)**。

### JS实现

```JavaScript
// 力扣164. 最大间距
const MAX_BUCKET_COUNT = 100;

/**
 * 插入排序
 * @param {number[]} nums 待排序数组
 * @returns {number[]} 排序后的数组
 */
const insertionSort = nums => {
    if (!Array.isArray(nums)) { return nums; }
    const sorted = [nums.shift()];
    nums.forEach(item => {
        let target = 0;
        for (let index = sorted.length - 1; index >= 0; index--) {
            if (sorted[index] > item) {
                sorted[index + 1] = sorted[index];
                continue;
            }
            target = index + 1;
            break;
        };
        return sorted[target] = item;
    });
    return sorted;
}

/**
 * 桶排序
 * @param {number[]} nums 待排序数组
 * @returns {number[]} 排序后的数组
 */
const bucketSort = nums => {
    const { length } = nums
    if (!Array.isArray(nums) || length < 2) { return nums; }
    const bucketCount = typeof MAX_BUCKET_COUNT !== 'undefined' ?
        Math.min(length, MAX_BUCKET_COUNT)
        :
        length;
    const min = Math.min(...nums);
    const max = Math.max(...nums);
    const bucketSize = (max - min) / bucketCount || 1;
    const bucket = Array(bucketCount);
    nums.forEach(num => {
        if (Number.isNaN(+num)) { return; }
        const index = Math.floor((num - min) / bucketSize);
        if (!bucket[index]) {
            bucket[index] = [];
        }
        bucket[index].push?.(num);
    });
    return bucket.map(array => insertionSort(array))
        .flat();
};
```


---

***`//未完待续`***

---


![公众号二维码](https://neptliang.github.io/img/Article/WeChatBlog.png)