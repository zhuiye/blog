---
title: css-use
date: 2020-01-02 23:22:49
tags: css
categories: 前端
---

## 前言

是非经过不知难，近期由于业务需要，做一些展示交互型的 web 移动端页面，由于接触较少，遇到不少坑，特来记录一下此次的获取的经验。

## 技术运用

1. react 作页面架构
2. **[css animate](https://github.com/daneden/animate.css/)**
3. **[animejs](https://www.animejs.cn/)**
4. **[antV](https://g2.antv.vision/zh/)**
5. **[hammer.js](https://hammerjs.github.io/)** 手势识别
6. **[lottie-web](http://airbnb.io/lottie/#/)** 动画库
7. **echart**

## 关于页面的适配

**100vh 或许会产生有滚动条的现象**，浏览器一般都有标题栏，和工具栏，如果我们给 body 设置 100vh,就会产生滚动条，究其原因，100vh 等于可视屏幕视口高度，但工具栏遮盖住了一部分，所以产生了滚动条，所以得注意一下这个问题，但我们可以用 window.innerHeight 来实现**全屏**（没有滚动条得效果），其次我们可以<s>**隐藏标题栏或工具栏**</s>

在本次的实践项目当中，由于采用图片做背景，文字要覆盖在其固定的位置，故我们要给高度做适配。有两种方式，

1. 拉伸图片，高度采用 vh，长屏幕，适配 ok，展示有拉伸效果。
2. 按宽度比率缩放，高度采用 vw， 此等展示效果比较好，就是长屏幕会有大量留白，

## 关于一些转化效果

主要用到 css transform:scale(x,y) 这个主要是进行缩放，以及 translateY()配合 transaction 进行平滑的移动，

## 阻止浏览器,微信的下拉刷新

```js
document.body.addEvenetlistener(
  "touchmove",
  function (e) {
    e.preventDefault();
  },
  {
    passive: false,
  }
);
```

## 阻止菜单出现

```js
document.oncontextmenu = function (e) {
  e.preventDefault();
};
```

## safari 浏览器无法隐藏工具栏及标题栏

## 关于 iPhoneX 的适配

关于在项目中我使用特意适配，因为比较特殊，没有采用采用 css 属性适配
https://aotu.io/notes/2017/11/27/iphonex/index.html

## 浏览器外部唤醒 app

android 则用 URL schemes ,简单配置如下：

```xml
<activity android:name=".MainActivity">
  <intent-filter>
      <action android:name="android.intent.action.MAIN" />
      <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <!--URL Scheme启动-->
  <intent-filter>
    <!--URL Scheme协议部分-->
      <data
          android:host="hengcheng"
          android:path="/main"
          android:port="8000"
          android:scheme="example" /> <!--必填 -->
      <!--必有项-->
      <action android:name="android.intent.action.VIEW" />
      <!--表示该页面可以被隐式调用，必须加上该项-->
      <category android:name="android.intent.category.DEFAULT" />
      <!--如果希望该应用可以通过浏览器的连接启动，则添加该项-->
      <category android:name="android.intent.category.BROWSABLE" />
  </intent-filter>
</activity>
```

调用如下

```js
window.location.href = "example://hengcheng:8000/main";
```

iOS 则采用**Universal Link**, 也需要经过一番配置，具体操作配置可以上网搜搜，不过需要注意点是跳转 iOS 那个链接，必须是**不同域**,意味着 h5 的页面（假如采用:pmtest）要和配置的**Universal Link** (采用 pmtest2)不同域。

在业务中我们的跳转 app 大体流程如下：

  <img src="../images/jumpToApp.jpg">
  
  下段代码为一个中间页，用来唤醒APP
  ```js
    export default class WakeUpApp extends React.Component {
      componentDidMount() {
        if (isAndroid()) {
          window.location.href = "cktaSpp://xxxxx";
          setTimeout(function() {
            window.location.href = "https://fir.im/yfckhb";
          }, 2000);
        } else {
          window.location.href =
            "https://itunes.apple.com/cn/app/%E7%95%85%E5%AE%A2%E4%BC%99%E4%BC%B4/id1449233800?mt=8";
        }
      }
      render() {
        return <div></div>;
      }
}
  ```
  
  推荐参考链接：
  
  https://juejin.im/post/5b7efb2ee51d45388b6af96c

## 关于此次的页面设计

设计师尽管是按照 iphone6 的尺寸来设计，但并没有考虑到 iphone6 在 safari 中的可视页面高度为 677-标题栏-工具栏高度，大约 517px，如果要求没有**滚动条**的要求，就需要考虑这个**可视高度**。其次，**布局随背景图片乃是大忌**，何解？其一，不同屏幕的适配，会拉伸图片(当然你也可以媒体查询设置不同分辨率的图片,但意味着，要写多套高度适配代码),其二，垂直方向随屏幕高度适配，此处可采取 vh,或者可视高度占比来作适配，vh 计算其实并不是那么准确，占比也没试过，这些方案并不好，到了这里，说得很清楚了，这就是设计问题了。得出结论，**谨慎布局随背景需求**

## 最大难点

业务的场景有一个从远到近的缩放效果，缩放后的页面要适配到各个屏幕，如：在页面的某个位置有一个 200\*200 的图片，你要经过缩放然后扩充到全屏，后续的布局**严重**依赖此缩放的图片。到目前为此：此事无解，因为我根本没法计算**缩放比率**，也就没法正好缩放到图片高度设置（100%）得样子，只好采取拙劣手法，大致缩放到一定范围，然后拿正确图片替换，此法会有一闪而过，无法避免~~
