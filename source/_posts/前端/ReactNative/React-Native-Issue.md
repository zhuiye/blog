---
title: React-Native-Issue
date: 2020-07-18 19:14:40
tags: React Native
---

## (Text) some texts get cut off in some device(Android)

在一些 Android 手机 设备,Text 组件 包裹的文字,会出现一种截断的现象,设置 fontWeight:'bold',也有可能发生此现象,究竟原因可能是
库中的 Text 字体 与 系统的 Text 字体发生了冲突? 不过我们也有解决的方案.通过设置 fontFamily 为 '',可以解决此问题,我们只需全局
更改 Text 的渲染即可,如下代码:

```js
import React from "react";
import { Platform, Text } from "react-native";

export function fixAndroidFont(fontName = "") {
  if (Platform.OS !== "android") {
    return;
  }
  let styles = {
    androidFontFixFontFamily: {
      fontFamily: fontName,
    },
  };
  let __render = Text.render;
  Text.render = function (...args) {
    let origin = __render.call(this, ...args);
    return React.cloneElement(origin, {
      style: [styles.androidFontFixFontFamily, origin.props.style],
    });
  };
}
```

最后在 App.js 执行 `fixAndroidFont()` 即可

### Link

https://github.com/facebook/react-native/issues/15114

## React-Native-WebView and React Navigation was cashed (android)

android 9 以上, WebView(最新) 和 React Navigation(V4,V5) 会应用崩溃 不小心使用户的话,就会造成应用奔溃,webView 默认是启用的.原因是 React Navigation 的动画 实现影响了 WebView ,我们只要关闭 React Navigation 的动画 即可,代码如下:

### 方案 1

React Navigation V5

```tsx
<NavigationContainer>
  <Stack.Navigator initialRouteName="HomeScreen">
    <Stack.Screen
      name="HomeScreen"
      component={HomeScreen}
      options={{
        animationEnabled: false,
      }}
    />
  </Stack.Navigator>
</NavigationContainer>
```

V4

```js
Screen.navigationOptions = {
  animationEnabled: Platform.OS === "ios",
};
```

### 方案 2

经发现,关闭 webView 的硬件加速功能,应用就能正常运行,但关闭硬件功能,可能会使 webView 中的视频播放不了,在一些低端的手机,所以,我们得配合方案 1 使用,如果需要再 webView 播放视频,执行一些高性能操作,我们就得关闭 React Navigation 中的动画,否则关闭硬件加速功能

```js
   <WebView androidHardwareAccelerationDisabled={true}>
```

### link

[Android 9: App crashes if hardware acceleration is enabled ](https://github.com/react-native-community/react-native-webview/issues/575#issuecomment-587267906)

## react-native-image-crop-picker Android app crashes with cropping enabled on large images

使用改库的时候,选择启用裁剪,选择大像素图片,会发生应用奔溃,目前修复的办法,只能是对选择的图片进行压缩处理,且看代码如下:

```js
  import ImageResizer from 'react-native-image-resizer';
  import ImagePicker from 'react-native-image-crop-picker';
  ImagePicker.openPicker({
      cropping: false,
      cropperToolbarColor: Global.primaryColor,
    }).then(image => {
      let divider = 1;
      if (image.size > 300000) {
        divider = image.size / 300000;
      }
      ImageResizer.createResizedImage(
        image.path,
        image.width / divider,
        image.height / divider,
        'JPEG',
        100,
        0,
        null,
      ).then(resp => {
          ImagePicker.openCropper({
            path: resp.uri,
          }).then(image => {

            })
          }
        )
```

### link

[Android app crashes with cropping enabled on large images](https://github.com/ivpusic/react-native-image-crop-picker/issues/1291)
