---
title: React Hook dependencies
date: 2023-11-5 16:00:46
tags: web
---

react 依赖中的比较使用 Object.is

```tsx
const a = {};

useEffect(() => {
  // 每一次渲染都执行
  console.log(test);
}, [a]);
const [test, setTest] = useState({ name: "xxx", id: "" });

// 只有使用setTest使其概念，它才会执行，每次渲染都是同一个内存对象。
// useState
useEffect(() => {
  console.log(test);
}, [test]);
```
