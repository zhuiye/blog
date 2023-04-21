---
title: react-life-cycle
date: 2019-12-15 16:52:28
tags: React 
---

## 关于在 componentWillMount 中执行异步操作的争论

componentWillMount 永远执行在 render 函数之前，为什么不在此做一些异步操作呢和 setState，一是官方并不推荐，推荐在 componentDidMount 做一些异步 io 操作，原因是 js 是单线程，执行不可能完成在 render 之前，而且真实 dom 还没渲染完毕，而且在服务端渲染也可能多次调用 componentWillMount，而且现在的 react 版本已经弃用此 api 了，不再过多关注和讨论

### 项目中使用

一般在 constructor 中进行数据的初始化，以前订阅一些操作，而异步的请求则在 componentDidMount 中进行。

## 关于 react 的生命周期

### 先前版本

**mount：**

constructor->componentWillMount->render->componentDidMount

**setState 更新：**

shouldComponentUpdate->componentWillUpdate->render->componentDidUpdate

**父组件更新:**

componentWillReceiverProps->(setState)->
shouldComponentUpdate->componentWillUpdate->render->componentDidUpdate

### 如今版本

**mount：**

constructor->getDerivedStateFromProps->render->componentDidMount

**setState 更新：**

getDerivedStateFromProps->shouldComponentUpdate->render->componentDidUpdate

**父组件更新:**

getDerivedStateFromProps->(setState)->
shouldComponentUpdate->render->componentDidUpdate

## 参考

<https://zhuanlan.zhihu.com/p/38030418>

<https://github.com/reactjs/reactjs.org/issues/302>