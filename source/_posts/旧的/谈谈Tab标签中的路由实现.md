---
title: 谈谈Tab标签中的路由实现
date: 2020-05-14 22:06:15
tags:
---

## 前言

近期由于产品需要重构了后台项目中的导航架构，点击导航菜单动态生态一系列的 Tab 标签页面,类似于下图
(侵权请联系我,并告知删除)
<img src="../images/structure.png"/>
我目前的架构是 采取 antd 的 Tab 以及 Menu 组件 动态的生成 tab 标签页面,由于 tab 页面可能有多个,故此我们要为其实现一个页面路由系统，它比较简单，并不涉及浏览器。

## 定义 TabRoute 组件

第一步，我们先首先定义我们 TabRoute 组件的配置方式,首先，我们肯定需要一个 routes 属性，数据类型为数组对象,
里面放着我们所有的 Tab 页面路由的相关信息，然后我们也可以传入一个初始的 initRoutesKey,代表着我们的初始页面是哪个。
其表现形式如下:

```tsx
// 需要有一个 routes 属性，传入Tab内的页面数组
<TabRoute
  initRoutesKey="role"
  routes={[
    { key: "role", component: RolePage },
    { key: "authRole", component: AuthRole },
  ]}
/>
```

## 实现 TabRoute 组件

TabRoute 的实现，具体如下:

```tsx
import React, { useState } from "react";

interface RouteItem {
  key: string; // 唯一key
  component: any; // 对应的页面组件
  name?: string; // 用于面包屑的显示
  routeParams?: object; // 也可静态传入参数进入页面中
}

// 组件属性的定义
interface TabRouteProps {
  routes: RouteItem[];
  initRoutesKey?: string;
}

function TabRoute({ routes, initRoutesKey }: TabRouteProps) {
  /*
    定义一个路由栈，如果没有传initRoutesKey，取routes数组中的第一个为初始页面
  */
  const [routeStack, setRouteStack] = useState<any>(
    initRoutesKey
      ? routes.filter((item) => {
          return item.key === initRoutesKey;
        })
      : [routes[0]]
  );

  /*
    定义一个导航跳转方法，用于每个Tab 内的页面切换。
  */
  const navigate = (key: string, params?: object) => {
    let position = -1;
    routeStack.forEach((item: any, index: number) => {
      if (item.key === key) {
        position = index;
      }
    });
    if (position === -1) {
      // 未存在路由栈中，则加入，并传入params参数,更新路由栈
      routes.forEach((item) => {
        if (key === item.key) {
          setRouteStack(
            routeStack.concat([
              {
                ...item,
                routeParams: params,
              },
            ])
          );
        }
      });
    } else {
      // 如果路由已存在导航栈中，去除目标路由之后的数据
      let newStack = routeStack.slice(0, position + 1);
      newStack[newStack.length - 1].routeParams = params;
      setRouteStack(newStack);
    }
  };

  /*
    定义 返回方法,也就是弹出栈顶元素
  */
  const goBack = () => {
    if (routeStack.length > 1) {
      const newStack = routeStack.slice();
      newStack.pop();
      setRouteStack(newStack);
    }
  };

  // 选择渲染栈顶元素,即代表当前渲染的页面

  const { component: ShowComponent, routeParams = {} } = routeStack[
    routeStack.length - 1
  ];

  /* 
  给页面的组件中传入 navigate,goBack,routeParams,routeStackLength 属性
*/
  return (
    <ShowComponent
      routeStackLength={routeStack.length}
      navigate={navigate}
      goBack={goBack}
      routeParams={{ ...routeParams }}
    />
  );
}

export default TabRoute;
```

### 使用的例子

```tsx
import React from "react";

function Test1({ navigate }) {
  return (
    <div>
      <span>我是test1 页面</span>
      <button
        onClick={() => {
          navigate("test2", { info: "我是来自Test1页面的数据" });
        }}
      >
        跳转带Test2页面
      </button>
    </div>
  );
}

function Test2({ goBack, routeParams: { info } }) {
  return (
    <div>
      <button
        onClick={() => {
          goBack();
        }}
      >
        返回
      </button>
      我是Test2页面，
      {info}
    </div>
  );
}

function Index() {
  return (
    <TabRoute
      initRoutesKey="test1"
      routes={[
        { key: "test1", component: Test1 },
        { key: "test2", component: Test2 },
      ]}
    />
  );
}
```

## 问题

这个 TabRoute，每次进行一次 路由切换的时候，都会重新卸载和挂载组件，所有的数据都会丢失，可能对于某些需要缓存的场景来说
不太好，这里如果有空的化，后续会实现跟 Tab 组件一样的效果，利用 display 隐藏 路由页面
