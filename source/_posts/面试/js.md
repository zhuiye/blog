## 闭包

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures#:~:text=%E9%97%AD%E5%8C%85%EF%BC%88closure%EF%BC%89%E6%98%AF%E4%B8%80%E4%B8%AA,%E5%88%9B%E5%BB%BA%E8%80%8C%E8%A2%AB%E5%90%8C%E6%97%B6%E5%88%9B%E5%BB%BA%E3%80%82

|闭包是指有权访问另一个函数作用域中的变量的函数--《JavaScript 高级程序设计》

|嵌套函数可访问声明于它们外部作用域的变量 -- MDN

闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。

简言之就是高阶函数- 返回一个函数的函数，这个函数可以传入参数与内部的函数绑定在一起。

# ES6 - 箭头函数、箭头函数与普通函数的区别

- https://juejin.cn/post/6844903805960585224#heading-0

｜｜｜箭头函数表达式的语法比函数表达式更简洁，并且没有自己的 this，arguments，super 或 new.target。箭头函数表达式更适用于那些本来需要匿名函数的地方，并且它不能用作构造函数

｜｜引入箭头函数有两个方面的作用：更简短的函数并且不绑定 this。

｜｜ 箭头函数不会创建自己的 this，它只会从自己的作用域链的上一层继承 this

```js
function Person() {
  this.age = 0;

  setInterval(() => {
    this.age++; // |this| 正确地指向 p 实例
  }, 1000);
}

var p = new Person();
```

## this

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this

### 定义

当前执行上下文（global、function 或 eval）的一个属性，在非严格模式下，总是指向一个对象，在严格模式下可以是任意值

函数上下文中的 this,取决函数调用的方式

## event loop

https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop

执行代码,处理事件,执行排队的子任务

视觉表现。 堆，栈，消息队列

1. 当消息来临，从取出消息，
2. 把相应的监听函数，把它加入栈中执行，执行完栈中的帧（函数）,
3. 检查是否有消息到达。如有执行第一步

-https://juejin.cn/post/7016593221815910408#heading-49

## 宏任务，微任务

这里只要考察代码的执行顺序,

宏任务：运行时间相对长,setTimeout,setImmediate
微任务:Promise.then, MutationObserver

运行顺序:先微，后宏

1. 执行代码，先执行同步代码，异步代码加入相应的 微宏队列中
2. 执行栈空，检查微队列，有就执行微队列。
3. 清空微队列，检查宏队列，把任务加入执行栈，
4. 执行栈完毕之后，检查微队列，重复第 3 步

```js
console.log("1");
setTimeout(function () {
  console.log("2");
  new Promise(function (resolve) {
    console.log("4");
    resolve();
  }).then(function () {
    console.log("5");
  });
}, 0);
new Promise(function (resolve) {
  console.log("7");
  resolve();
}).then(function () {
  console.log("8");
});
setTimeout(function () {
  console.log("9");
  new Promise(function (resolve) {
    console.log("11");
    resolve();
  }).then(function () {
    console.log("12");
  });
});
```

## new 运算符过程

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new

1. 新建一个空对象 newInstance
2. newInstance 的原型指向构造函数的原型
3. 执行构造函数，绑定 newInstance 为 this
4. 如果构造函数有返回对象，使用这个返回对象为 new 的结果，如果没有返回 newInstance。

## 节流&防抖

节流：规定时间内，事件多次触发，只会只执行的最初一次。
防抖： 规定时间内，事件多次触发，只执行最后一次事件，重新规定时间

```js
function throttle(fn, threshhold) {
  let timer, last;

  return function () {
    const context = this;
    const args = arguments;

    const now = +new Date();
    // 如果之前有执行过并且距离上次执行时间小于阈值，则延迟剩余时间后再执行
    // 保证执行间隔大于阈值
    const remaining = last ? last + threshhold - now : 0;
    /*
        如果在剩余时间范围内，就清空上一次的，剩余时间为零，就执行调用
    */
    if (remaining > 0) {
      clearTimeout(timer);

      timer = setTimeout(() => {
        last = +new Date();
        fn.apply(context, args);
      }, remaining);
    }
    // 第一次调用会执行，从上次执行开始超过阈值也会执行
    else {
      last = +new Date();
      fn.apply(context, args);
    }
  };
}
```

要注意绑定 this

## reflow & repaint

- https://developers.google.com/speed/docs/insights/browser-reflow

reflow：重新计算文档中各元素的**位置**和**几何形状**,重排必然重绘

引起 reflow 的操作：

- 调整浏览器窗口的大小、
- 使用涉及计算出的样式的 JavaScript 方法、在 DOM 中添加或移除元素，以及更改某个元素的类
  repaint:外观改变，不影响布局

减少 reflow

- 减少不必要的 DOM 深度
- 尽可能减少 CSS 规则的数量，并移除未使用的 CSS 规则。
- 如果您想进行复杂的渲染更改（例如动画），请在流程外执行此操作。您可以使用 position-absolute 或 position-fixed 来实现此目的。
- 避免使用不必要且复杂的 CSS 选择器（尤其是后代选择器），因为此类选择器需要耗用更多的 CPU 处理能力来执行选择器匹配

repaint:当一个元素的外观发生改变，但没有改变布局,重新把元素外观绘制出来的过程，叫做重绘。表现为某些元素的外观被改变

## http 缓存

- https://juejin.cn/post/6844903801778864136

## https 运行流程

- https://xie.infoq.cn/article/e021a8389f783e65d46149cc8
- https://segmentfault.com/a/1190000038406039

## 网络七层协议究竟是什么玩意
