---
title: PWA
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

## 在 pwa 模式下 iOS 长按图片不出现菜单

兼容性问题，无法解决。由于长按图片在低版本 ios 系统中，无法显示菜单，所以只好另打开一个 window,以下为 photoPrism 的解决方案

```ts
/*

Copyright (c) 2018 - 2025 PhotoPrism UG. All rights reserved.

    This program is free software: you can redistribute it and/or modify
    it under Version 3 of the GNU Affero General Public License (the "AGPL"):
    <https://docs.photoprism.app/license/agpl>

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU Affero General Public License for more details.

    The AGPL is supplemented by our Trademark and Brand Guidelines,
    which describe how our Brand Assets may be used:
    <https://www.photoprism.app/trademark>

Feel free to send an email to hello@photoprism.app if you have questions,
want to support our work, or just want to say hello.

Additional information can be found in our Developer Guide:
<https://docs.photoprism.app/developer-guide/>

*/

import saveAs from "file-saver";

// Detect Safari browser.
const isSafari =
  navigator.appVersion.indexOf("Safari/") !== -1 &&
  navigator.appVersion.indexOf("Chrome") === -1;

// Downloads a file from the server.
export function download(url: string, name: string) {
  // Abort if download url is empty.
  if (!url) {
    console.warn("can't download: empty url");
    return;
  }

  if (isSafari) {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.responseType = "blob";

    xhr.onload = function () {
      saveAs(xhr.response, name);
    };

    xhr.onerror = function () {
      console.error("download failed", url);
    };

    xhr.send();
    return;
  }

  // Create download link.
  const link = document.createElement("a");

  if (name) {
    link.download = name;
  }

  link.href = url;
  link.style.display = "none";

  document.body.appendChild(link);

  // Start download.
  link.click();

  // Remove download link.
  document.body.removeChild(link);
}
```

- https://stackoverflow.com/questions/64100909/pwa-on-ios-wont-let-me-dowload-image-from-my-app

- https://segmentfault.com/a/1190000041729491#item-3-10
