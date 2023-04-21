---
title: oc-ts
date: 2020-01-25 12:06:19
tags: objective-c
---

## OC & Typescipt  hello world

### Objective-c

```objective-c
#import <Foundation/Foundation.h>
int main (int argc,const char* argv[]){
    @autoreleasepool {
        NSString *sayWorld=@"hello world";
        NSLog(sayWorld)
    }
    return 0
}
```

### Typescript

```typescript
const sayWorld:string="hello world";
console.log(string);

```

对比

| 特性     | OC                                | TS                           |
| -------- | --------------------------------- | ---------------------------- |
| 导入包   | #import <Foundation/Foundation.h> | import 'page-name'           |
| 主入口   | main 函数                         | 直接在v8上执行，执行从上到下 |
| 声明     | 比较麻烦，是一个对象，            | 当然在ts                     |
| 函数调用 | 普通一样                          |                              |
| 内存管理 | @autoreleasepool  ARC             | V8的垃圾回收机制             |

### 类

|          | OC                                            | TS                      |
| -------- | --------------------------------------------- | ----------------------- |
| 方法调用 | [class methodName:param]   [recevier message] | class.methodName(param) |
| 声明类   | 1.定义(即使这个类的接口)  2.实现              | 直接实现即可            |
| 当前对象 | self                                          | this                    |
| 生成器   | @property  的属性是私有变量 @systhesize       |                         |

### 强制类型转化

| OC                                         | TS                              |
| ------------------------------------------ | ------------------------------- |
| (int) xxx,(float) xxx   (TestClass *)value | Number()，paseInt()  floatInt() |
| id 类型 表示不确定类型的对象               | any                             |
| 类型运算符将一般id类型的对象转化特定的类型 | (test as TestClass)             |

### 继承

| OC                                                         | TS                                          |
| ---------------------------------------------------------- | ------------------------------------------- |
| 分类机制 相当于 JS 中的property 原型机制                   | JQuery中的extends                           |
| @class 指令  @class  ClassName 或者   #import  "ClassName" | 类型定义文件  import Test from 'index.d.ts' |
|                                                            |                                             |

### 枚举类型可以当成一种特殊的对象

### 预处理程序#defined

可以定义真正意义上的“全局常量”，在我的理解中，用来定义项目的常量配置最好不过，如：第三方key,或者RAS key 等等

特点：不同于变量，只要定义了，程序任何地方都可以使用

宏：可以看作是预定义函数，待有自定义变量



### 块

可以当成一个匿名函数,一般当作函数或者方法的参数，可以理解为JS中的回调函数，格式有点像匿名函数，函数表达式可以，赋值给一个变量。能让系统分配给其它处理器或者应用的其它线程执行。

### 结构

| OC                               | TS   |
| -------------------------------- | ---- |
| 数据组织在一起，就跟JS的对象一样 | 对象 |
| . 语法 赋值 和访问               | ---  |
| 与对象的区别 ,变量名前缺少 * 号  |      |



### 循环

用途 :用来遍历展示数据，组装所需要得数据格式，