---
title: Ts
date: 2023-11-6 16:00:46
tags: web
hide: true
---

TypeScript：静态类型检查器

我需要掌握到什么程度

- 探究原生类型的接口声明实现。

如何管理整个类型系统文件，现在还是没有整理出来

在不运行代码的情况下检测代码中的错误称为静态检查。根据操作值的类型确定什么是错误、什么不是错误称为静态类型检查。

如果要扩展原生的对象 ,需要 declare global ,以及往远行链条上添加方法

```ts
declare global {
  interface Array<T> {
    cForEach: (fn: (arg: T) => void) => void;
  }
}
```

```ts
  // 谓词判断，直接断定 arg 为数组 ，可以看到，返回值是boolean ,参数断言
  isArray(arg: any): arg is any[];
```

## type & interface

关键区别在于类型不能重新打开以添加新属性，而接口始终可以扩展。

- https://www.typescriptlang.org/docs/handbook/2/everyday-types.html

对于编译器来说，使用接口通常比使用类型别名和交集更高效

1.  扩展类型的方式 有所不同
    interface 使用 extends ,type 使用 & 或者 |
2.  向现有的接口添加新的类型
    interface 可以， 但 type 不行 ,type 类型创建后无法更改

```ts
// 两次断言，有时候发现，as any 还会报错，再次 as 具体类型就好
const a = expr as any as T;
```

类型 boolean 本身实际上只是 union 的别名 true | false。

as const 将整个对象转换为类型文字,对类型系统而言，它确保所有属性都被分配文字类型，

object 类型用于表示所有非原始类型，包括数组、函数、类实例等。
object 类型的缺点是，它不能描述对象的具体结构。如果需要更细粒度的类型定义，通常会使用接口（interface）或类型别名（type）。

在泛型约束中，object 是一个常用的类型，用于限制泛型参数必须是复杂类型。

in 操作符

一般用来遍历一个联合类型 如 “1”｜“2”｜“3” 如：{}｜{}

这两个概念有点难理解

协变（Covariance）：父类型可以赋值给子类型
逆变特性：子类型可以赋值给父类型

如果一个类型参数依赖与另一个类型参数，需要用 extends 来捏合起来

```ts
type GetPropsType<T, K extends keyof T> = T[K];
```
