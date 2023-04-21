---
title: Reactive Extensions 响应式编程
date: 2020-04-14 20:51:00
tags:
---

## 前言

偶然看到隔壁的小伙伴在用 RxJS,他说这是一款不错的 处理**异步事件流**一个工具,对于 RxJS 的认识,也就是只听说过一个名词而已,对于具体处理什么
业务场景确实不得而知了.故此花了一定的时间来入门了解一下.

## 响应式编程

RxJS 是针对响应式编程 实现的一个库,什么是响应式编程?它与**面向对象**,**命令式编程** 地位与之并列,是一种利用**异步事件流的**方式进行编程, 具体长什么样的,先来一段 JS 代码来看看

```js
const { Observable } = rxjs;
const stringStream = new Observable(function (subscriber) {
  subscriber.next("hello world");
});

stringStream.subscribe({
  next: (data) => {
    console.log(data); // hello world
  },
});
```

这就好像 一般的 **Event Bus** 或者看着像 **Promise**,不错,你可以认为这是他们的升级版,但这样看不出写的好处在哪,反而有一点繁琐. 是的,单凭上面小小的例子看不出什么门道,但里面却贯穿着一个无比重要的思想 **everything is stream**

## everything is stream

响应式编程的魔咒,从现在开始,我们要抛弃**命令式的思维**去编写程序,重新从另一个角度去看待事件.
**everything is stream**,一切皆是流,怎么理解这就话,页面的点击,用户的输入,请求的过程,编程的一切都从流开始,用户的输入的字符串就是一个流,这个流(所带的数据)可以被更改,删除,替换,在一定的阶段,我们可以(消费)获取到这个流来做一些事情.下面给出几个简单的例子加深一下理解

### 监听用户的点击按钮,并获取按钮上的 id 值

```js
const { fromEvent } = rxjs;
const { map } = rxjs.operators;
const btnDom = document.querySelector("#btn");

// 获取到点击事件流
const buttonClickStream = fromEvent(btnDom, "click");

// 这个点击事件流所带的数据就是event

buttonClickStream.subscribe((event) => {
  console.log(event);
});

/*
  对这个点击事件流进行处理
  pipe 与 map 方式,只是单纯的接收这个buttonClickStream流,经过处理返回一个新流,
  这个流带着 id 的值,然后,我们"订阅"到这个值
*/
buttonClickStream.pipe(map((event) => event.target.id)).subscribe((value) => {
  console.log(value); // btn
});
```

### 对输入框进行防抖处理

```js
const { fromEvent } = rxjs;
const { debounceTime } = rxjs.operators;
const searchInputDom = document.querySelector("#searchInput");
/*
  监听change事件流,然后对这个事件流进行防抖处理,最后订阅该值
*/
fromEvent(searchInputDom, "change")
  .pipe(debounceTime(1000))
  .subscribe((e) => {
    console.log(e.target.value); // btn
  });
```

## 小结

从上面连个小例子不难看出,我们编程首先要切换到 **"流式思想"**,然后对这个流进行各种处理,得到我们想到的 值,最后我们消费该值,当然,转换流式思想有一定的难度,这就需要在项目中不断的实践,还要经常去查看 RxJS 提供的操作符,如何优雅辨别流之间的关系,以及组合,这是后续学习的一个难点.说这这么久,还没说它到底有什么业务场景需要使用,使用不多,我只能从网上摘出几点

1. 让异步编程以同步的方式编写
2. 加深代码抽象的程度，让你可以更专注于定义与事件相互依赖的业务逻辑，而不是把大量精力放在实现细节上，同时，使用响应式编程还能让你的代码变得更加简洁

学习成本还是很高的,但就目前而言,大多数场景都用不上,但开拓思维还是不错的

## 一个完成的登陆例子

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <script src="https://unpkg.com/rxjs/bundles/rxjs.umd.min.js"></script>
  </head>
  <body>
    <div id="btn">点击一下按钮</div>
    <form id="form">
      <div>
        <input placeholder="xxxx" id="phone" />
        <div style="color: red;" id="error"></div>
      </div>

      <input type="password" id="password" />
      <input type="submit" id="submitBtn" />
    </form>

    <script>
      window.onload = function () {
        const { range, fromEvent, Observable, from, of, interval } = rxjs;
        const {
          map,
          filter,
          scan,
          throttle,
          debounceTime,
          flatMap,
        } = rxjs.operators;

        const query = document.querySelector;

        const phoneDom = query("#phone");
        const passwordDom = query("#password");
        const errorDom = query("#error");
        const submitBtn = query("#submitBtn");
        const form = query("#form");

        const passwordStream = fromEvent(passwordDom, "change");
        passwordStream.subscribe((e) => {
          console.log(e.target.value);
        });

        const checkPhoneBlurStream = fromEvent(phoneDom, "blur");

        const checkPhone = (value) =>
          /0?(13|14|15|18|17)[0-9]{9}/g.test(value) && value.length === 11;

        checkPhoneBlurStream
          .pipe(map((event) => checkPhone(event.target.value)))
          .subscribe((value) => {
            if (!value) {
              errorDom.innerHTML = "请输入合法的字符";
            } else {
              errorDom.innerHTML = "";
            }
          });

        const formStream = fromEvent(form, "submit");

        formStream
          .pipe(
            map((event) => {
              event.preventDefault();
              if (checkPhone(phoneDom.value)) {
                return {
                  phone: phoneDom.value,
                  password: phoneDom.password,
                };
              }
              return null;
            }),
            filter((value) => value !== null)
          )
          .subscribe((data) => {
            loginStream(data);
          });

        const loginApi = function (data) {
          return new Promise(function (resolve, reject) {
            setTimeout(function () {
              resolve([{ success: "成功", data: data }]);
            }, 2000);
          });
        };

        function loginStream(data) {
          const loginDataStream = of(data);
          loginDataStream
            .pipe(flatMap((data) => from(loginApi(data))))
            .subscribe((value) => {
              console.log(value);
            });
        }
      };
    </script>
  </body>
</html>
```

## 参考链接:

https://rxjs.dev/

https://github.com/benjycui/introrx-chinese-edition

https://hijiangtao.github.io/2020/01/13/RxJS-Introduction-and-Actions/
