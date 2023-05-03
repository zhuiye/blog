---
title: 前端 微信 支付宝 支付H5 接入
date: 2020-03-21 21:32:51
tags: web 
---

## 前言

近来项目中有要求接入 微信和支付宝 H5 的支付的接入，由于是第一次接入，也遇到了不少的坑。

## 支付宝支付的接入

### 前端如何接入，并唤起支付宝客户端进行支付
  **前端的接入，有两种方式**
  1. 后端返回一个form 表单字符串，我们插入到页面中进行提交即可，页面会跳转到支付宝的一个中间页，由这个中间页来唤起支付宝客户端进行支付
  2. 后端返回一个地址，前端直接如下代码即可
  ```ts
      window.location.href="xxxx"
  ```
一开始，后端返回一个支付相关信息字符串回来，由前端进行form 表单生成并提交。我也如实做了如下的封装

payInfo 的处理
```ts
  const querystring = require("querystring");
  const url = require("url");
  export function getSearchParamsByUrl(urlString: string): any {
    // return
    const searchString = url.parse(urlString).query;
    return querystring.parse(searchString);
  }
```

```ts

const submitAliForm = (payInfo: any) => {
  const formStr = `
          <form action="https://openapi.alipay.com/gateway.do?charset=${payInfo.charset}" target="_blank" method="post" class="form">
              <input type="text" name="app_id" value=${payInfo.app_id}>
              <input type="text" name="method" value=${payInfo.method}>
              <input type="text" name="format" value="JSON">
              <input type="text" name="charset" value=${payInfo.charset}>
              <input type="text" name="sign" value=${payInfo.sign}>
              <input type="text" name="sign_type" value=${payInfo.sign_type}>
              <input type="text" name="version" value=${payInfo.version}>
              <input type="text" name="notify_url" value=${payInfo.notify_url}>
              <input type="text" name="biz_content" value=${payInfo.biz_content}>
      </form>`;
  const divDom = document.createElement("div");

  divDom.style.display = "none";
  divDom.innerHTML = formStr;
  document.body.appendChild(divDom);
  
  // 为什么不直接字符串生成？因为，timestamp 由空格 2024-12-4 12:12:12
  const timeInput = document.createElement("input");
  timeInput.value = payInfo.timestamp.replace("+", " ");
  timeInput.name = "timestamp";
  document.forms[0].appendChild(timeInput);

  document.forms[0].submit();

  // 调用后可清除表单
  // document.forms[0].parentNode?.removeChild(document.forms[0]);
  // document.body.removeChild(document.forms[0]);
};
```
但是提交支付宝的支付网关，却报了错，**签名错误**，排除了后端的错误，最后发现是前端的解码出了问题,我也不知道支付宝做了什么处理，找了很多资料也没看到，网上的大部分都是在后端生成，前端提交。最后，**我只能采取采用第二种方式提交**

```ts
   window.location.href="https://openapi.alipay.com/gateway.do?charset后端返回的支付宝参数,return_url=前端页面的回调地址",
```

## 微信jsApi 支付接入

1. 前端发起授权请求,获取code,发送给后端，存到openId 到本地

2. 提交订单信息和openId 给后端，换取微信支付信息，然后前端，调用微信客户端暴露出的支付接口


### 微信授权

首先页面第一次进入微信的时候，发送 如下链接，然后就会弹出授权框，问你是否需要授权，如果点确定，就会跳转至redirect_uri
，然后在redirect_uri中带上code

```ts
const authUrl = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appid}&redirect_uri=${redirect_uri}&response_type=code&scope=snsapi_userinfo&state=123#wechat_redirect`;
window.location.href=authUrl
```
解析跳转地址中 url,获取code，发送给后端，取得openId,存于本地,以便下次进入页面，不需要授权,下面为部分代码

```ts 

   import Cookies from "js-cookie";
   const queryString = getSearchString(); //自己封装的方法
   const code=queryString.code,

   getOpenId(code).then(res=>{
     /* 为什么不用localStorage ? 微信中莫名奇妙，清不了。网上查找的方案
     大多是用cookies存，那么我们也用cookie存了 
     */
     Cookies.set("openId", res.openid, { expires: 7 });
   })
   
```

下次进入页面不需要授权

```ts 
    if(!Cookies.get("openId")){
      // 授权......
    }
    // 否则正常渲染页面
```

### 完整授权参考代码如下:

```ts
  import React from "react";
  import { isWeiXin } from "utils/envCheck";
  import getSearchString from "utils/searchString";
  import { getOpenId } from "./apply/service";
  import { Toast } from "antd-mobile";
  import { CenterContainer } from "components";
  import Cookies from "js-cookie";
  export default function withWeiXinAuth(
    WrapperComponent: React.FunctionComponent
  ) {
    return class WeiXinAuth extends React.Component {

     

      componentDidMount() {
        const queryString = getSearchString();

        if (isWeiXin && !Cookies.get("openId") && !queryString.code) {
          const appid = "微信appid";
          const redirect_uri = encodeURIComponent(window.location.href);
          const authUrl = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appid}&redirect_uri=${redirect_uri}&response_type=code&scope=snsapi_userinfo&state=123#wechat_redirect`;
          window.location.href = authUrl;
        }
        if (isWeiXin && queryString.code) {
        
          getOpenId(queryString.code, queryString.oem)
            .then(res => {
              Cookies.set("openId", res.openid, { expires: 7 });
              this.setState({
                loading: false
              });
            })
            .catch(e => {
              // 不经过state 对比，强制执行render函数
              this.forceUpdate()
              Toast.fail(e.cnmsg);
            });
        }
      }

      render() {
        //

        if (isWeiXin && !Cookies.get("openId")) {
          return <CenterContainer>等待授权中~</CenterContainer>;
        }

        return (
          <>
            <WrapperComponent {...this.props} />
          </>
        );
      }
    };
  }

```

## 微信H5支付

我们只需要如下操作即可

```js
  window.location.href="后端返回的url"
```
