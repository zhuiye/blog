# jwt 授权

jwt: json-web-token,是一种跨域授权的一种方式，不需要保存会话信息，授权过期，只能等待 token 失效

## jwt 的组成

header:
playload:
sign:

## jwt 授权流程

1. client 发送登陆信息，服务器端 jwt.sign() 签名，返回 token 给 client
2. client 接受 token,存储，在每次的请求头带上 auth:token 的值。
3. 服务端验证 token.

## 与 session 鉴权的不同

- 可以跨域，
- 不需要格外的空间记录 通话的状态，session 授权，一般是存在一个持久层，或者内存中，格式
  `session_id,user_id`,而 jwt 则需要验证 token 就可以了，不需要查表什么。

## 链接

- https://www.atjiang.com/http-authentications-basic-session-token/

```js
i = 0;
if (++i === 1) {
  console.log("好了");
} else {
  console.log("误了");
}
```
