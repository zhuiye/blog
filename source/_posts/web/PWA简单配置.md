---
title: PWA 简单配置
date: 2024-11-11 16:00:46
tags: web
---

PWA,让你的网站拥有原生般的体验。

## html 的 meta 基本配置

```html
<!-- 图标-->
<link rel="icon" href="logo.png" />
<link rel="manifest" href="/manifest.json" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<!-- 透明的状态栏-->
<meta
  name="apple-mobile-web-app-status-bar-style"
  content="black-translucent"
/>
<meta name="apple-touch-fullscreen" content="yes" />
<meta
  name="viewport"
  content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no,
    viewport-fit=cover"
/>
<link
  rel="apple-touch-startup-image"
  href="/img/pwa/splash-2048x2732.webp"
  media="(device-width: 1024px) and (device-height: 1366px) and (-webkit-device-pixel-ratio: 2) and (orientation: portrait)"
/>
```

文件 manifest.json

```json
{
  // https://developer.mozilla.org/en-US/docs/Web/Manifest/display
  "display": "standalone"
}
```

## PWA 模式判断

判断是否处于 PWA 模式，可能用来适配一些玩意

```js
const isInStandaloneMode = () =>
  window.matchMedia("(display-mode: standalone)").matches ||
  window.navigator.standalone ||
  document.referrer.includes("android-app://");

if (isInStandaloneMode()) {
  console.log("webapp is installed");
}
```

- https://stackoverflow.com/questions/41742390/javascript-to-check-if-pwa-or-mobile-web

## hexo+fluid 中的配置

\_config.fluid.yml 文件中

```yml
custom_head: '
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <!-- 透明的状态栏-->
    <meta
      name="apple-mobile-web-app-status-bar-style"
      content="black-translucent"
    />
    <meta name="apple-touch-fullscreen" content="yes" />
    <link rel="manifest" href="/manifest.json">
'
```
