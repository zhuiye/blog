---

## You might be tempted to use an item’s index in the array as its key. In fact, that’s what React will use if you don’t specify a key at all. But the order in which you render items will change over time if an item is inserted, deleted, or if the array gets reordered. Index as a key often leads to subtle and confusing bugs.

最好要用唯一 key,不要用数组的索引，如果要实现列表增删改查，排序的功能，用数组索引，会有 bug

# ReactNode 与 jsx.element 类型区别

https://juejin.cn/post/6933572604594913293#comment

https://github.com/typescript-cheatsheets/react/issues/129

## 如果要构建自己的前端现代化 react 项目 ，应该采用什么库呢？

- next.js
- react-hook-form
- react-from-milk

- [swr](https://swr.vercel.app/zh-CN/docs/advanced/devtools) 请求库

# 自定义 hook

如果发现有太多相同的逻辑重复使用，是时候构建一个 hook 了
