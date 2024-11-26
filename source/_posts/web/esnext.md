---
title: ESNext
date: 2024-11-25 23:22:49
tags: js
---

此为记录新的 js 语法糖以及一些新的方法。

## 深度克隆 [structuredClone](https://developer.mozilla.org/zh-CN/docs/Web/API/structuredClone)

```js
structuredClone(value);
structuredClone(value, { transfer });
```

> 全局的 structuredClone() 方法使用结构化克隆算法将给定的值进行深拷贝。

## Array.toSorted 不改变原数组

```js
const array = [1, 2, 4, -5, 0, -1];
const array2 = array.toSorted((a, b) => a - b);

console.log(array2); // [-5, -1, 0, 1, 2, 4]
console.log(array); // [ 1, 2, 4, -5, 0, -1 ]
```

## Array.with 替换数组

```js
const array = ["a", "b", "c"];
const withArray = array.with(1, "fatfish");

console.log(array); // ['a', 'b', 'c']
console.log(withArray); // ['a', 'fatfish', 'c']
```

## 小结

尽管现代浏览器都支持了这些 api,但一些老旧的浏览器还未更新，暂时先观看，或许一两年后就可以了。

## 参考链接

- https://mp.weixin.qq.com/s/oCxIGsTMEl6AMzhgNXi6wQ
