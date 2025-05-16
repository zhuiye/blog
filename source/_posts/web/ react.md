---
title: React
date: 2023-11-6 16:00:46
tags: web
hide: true
---

## &&

这个需要特别的注意，0&&1, 返回 0，在 jsx 中并不返回一个 false ,所以特别要小心，然后

我需要掌握到什么程度

- 组件的编写,props vs state,跨组建传值
- useEffect 的使用
- 少见 hook 基本了解
- react 18,react 19
- 一些小型 react 系统的仿照实现

## useSyncExternalStore

⚠️ 如果你需要去集成已有的非 React 代码，useSyncExternalStore API 是很有用的。

有时一个组件需要从一些 React 之外的 store 读取一些随时间变化的数据

- 在 React 之外持有状态的第三方状态管理库
- 暴露出一个可变值及订阅其改变事件的浏览器 API

```js
import { useSyncExternalStore } from "react";
import { todosStore } from "./todoStore.js";

export default function TodosApp() {
  const todos = useSyncExternalStore(
    todosStore.subscribe,
    todosStore.getSnapshot
  );
  // 数据脱离react 之外 ，需要用useSyncExternalStore 链接起来
  return (
    <>
      <button onClick={() => todosStore.addTodo()}>Add todo</button>
      <hr />
      <ul>
        {todos.map((todo) => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}
```

<!-- 关于对 react 哲学的思考 -->
