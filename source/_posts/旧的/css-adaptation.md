---
title: CSS适配
date: 2019-12-02 22:53:38
tags: css
---

说起移动端的适配方案，提最多的当属手淘的 rem 适配布局,现今已经过度到 vw 了。今来溯本求源来说一下适配原理，

对于移动端的适配，我们是怎么做的呢？首先，设计师按 iPhone6 的设计尺度出图，一般设计图是 **750\*1334** 或者 **375\*667** 尺寸。
接着就到我们前端进行适配了。撇去那些繁多的，设备独立像素，物理像素，设备像素比的定义，我们只需要记得，
一般标准下：

1. 1px 像素对应 1 物理像素.
2. 设备像素比=设备独立像素/物理像素
3. meta 标签 设置 name="viewport" device=device-width，是让 css 布局层=设备像素的宽度=设备独立像素
   document.documentElement.clientWidth 可获取得到.

一般情况下。在 pc 端，无需顾及太多，1px 会对应到屏幕的物理像素，设备的像素比为 1，如设置 100px 的宽度，他所占的物理
像素也为 100 像素。而在 iPhone6 上 retain 屏幕又所有不同。简单的看下例子.

```html
<!-- 
       一个100*100 的盒子在pc端就很正常的显示，但在移动端，显示就比较模糊，而且有往远处拉的感觉,变得很小很小,原因是我们没有设置 name="viewport" device=device-width，让css 布局视口=设备的宽度，设置之后，我们又会发现100px 占了大约屏幕三分之一大。
   -->
<div style="width:100px;height:100px;background:red"></div>
```

现在来讲讲如何将设计稿中的尺度映射到我们到设置的具体 css 像素。以下皆设定设计稿 iPhone6 750\*1334
设计稿中宽 100px 的盒子,需要设置多少才能在 iPhone6 真机中按比率显示呢？
首先，我们先让布局适口宽度等于设备宽度,即添加如下标签

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

iPhone6 我们的设置的 css 像素计算为：100/750\*document.documentElement.clientWidth(375)=50px;
pixel2 下 我们设置的 css 像素为: 100/750\*document.documentElement.clientWidth(411)=54.8px;

可以看到，如果我们要让不同的屏幕的"100px"跟设计图的占比相同，需要设置不同的 px,但好在我们有一个 rem 像素单位（以 html 的 fontSize 大小（px）为倍数），有的这个连接点，我们在不同的屏幕下，动态计算 html 中 fontSize 的大小，然后我们只需要根据计算图中算出的 rem 比率，就可以适配到不同的屏幕。这也就使淘宝的适配方案了，接下来，分析下源码：

https://github.com/amfe/lib-flexible/blob/2.0/index.js

```js
(function flexible(window, document) {
  // 获取适口布局元素
  var docEl = document.documentElement;
  var dpr = window.devicePixelRatio || 1;
  // adjust body font size
  function setBodyFontSize() {
    if (document.body) {
      document.body.style.fontSize = 12 * dpr + "px";
    } else {
      document.addEventListener("DOMContentLoaded", setBodyFontSize);
    }
  }
  setBodyFontSize();
  // set 1rem = viewWidth / 10
  function setRemUnit() {
    var rem = docEl.clientWidth / 10;
    docEl.style.fontSize = rem + "px";
  }
  setRemUnit();
  // reset rem unit on page resize
  window.addEventListener("resize", setRemUnit);
  window.addEventListener("pageshow", function(e) {
    if (e.persisted) {
      setRemUnit();
    }
  });
  // detect 0.5px supports
  if (dpr >= 2) {
    var fakeBody = document.createElement("body");
    var testElement = document.createElement("div");
    testElement.style.border = ".5px solid transparent";
    fakeBody.appendChild(testElement);
    docEl.appendChild(fakeBody);
    if (testElement.offsetHeight === 1) {
      docEl.classList.add("hairlines");
    }
    docEl.removeChild(fakeBody);
  }
})(window, document);
```

把布局适口分成 10 份，可以看到，挡在 iPhone6 下,1rem=37.5px,fontSize 为 37.5px,在 pixel2 下为 41.1px。
计算图也分成 10 份，1rem=75px;100px 的设计图对应的比率为 100/75=1.33333rem,即我们布局输入为 1.3333rem。

## 关于 vw,vh

vw:代表布局适口的宽度百分之一
vh:代表布局适口的高度百分之一
假设 750*1334 的设计图，计算 vw,vh 如下,假设计算

100px\*100px

100/750\*100=13.3vw

100/1334\*100=7.496vh

我们当然也可以借助 vscode 的插件进行转化 px-to-vw ,pxtorem 等等。。。

## 项目中的具体运用

对于公司的项目，我采用 vw +flexBox 布局，对于 margin,padding,采用 vw,对于字体，则用 css 媒体查询，
不同 screenWidth,设置 不同的 body fontSizes
