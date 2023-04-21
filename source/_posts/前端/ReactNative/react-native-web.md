---
title: react-native-webView
date: 2020-03-23 21:09:43
tags: react native
---

 最近业务上需要在APP WebView 内的H5 实现图片分享和保持的功能，分享功能那就需要调用APP内的封装方法才行。
 至于保存功能，也是一样。暂且不用考虑这些，我们看看WebView 是否由提供保持的功能，搜寻issue,发现android长按
 无法调出菜单，（自己实践确实是无法调出）iOS可行，（低于13版本）。
 至于如何实现，在react-native-webView 中 也比较简单，下面就贴下简单的代码
 
 ```js
    <WebView 
      onMessage={({nativeEvent})=>{
        nativeEvent.data  // {url:'image url',title:'image name'}
        //拿到数据调用原生的代码即可
      }}
    >
 ```

 ```js
     window.ReactNativeWebView.postMessage(JSON.stringify({url:'image url',title:'image name'}));
 ```
## WebView 中的单页面应用
 如果webView 中嵌入单页面应用的话会怎么样，如果没经过任何的处理,就会出现**直接按设备物理键返回，会返回APP上级路由页面**。
 首先我们得先处理物理设备的返回键，就是进入webView 界面，拦截，监听webView 中路由历史，如果为栈顶，就退出。
 onNavigationStateChange 属性有个bug 就是 h5 页面的 history api 无法监听其变化，至少在android 上是这样，官方也没有给出
 修复，但有如下的讨论
 https://github.com/react-native-community/react-native-webview/issues/24

 ```js
        <WebView
          ref={ref => {
            this.webViewRef = ref;
          }}
          style={{ flex: 1 }}
          source={{ uri: 'http://192.168.10.199:3006/applyHome' }}
          onNavigationStateChange={navState => {
            // Keep track of going back navigation within component
            this.canGoBack = navState.canGoBack;
            console.log(navState);
          }}
          injectedJavaScript={`
            (function() {
              function wrap(fn,type) {
                return function wrapper() {
                  var res = fn.apply(this, arguments);
                
                    window.ReactNativeWebView.postMessage("{
                      type:${type},
                      url:${window.location.href}
                    }");
                
                  return res;
                }
              }
              history.pushState = wrap(history.pushState,'push');
              history.back= wrap(history.pushState,'back')
              history.replaceState = wrap(history.replaceState,'replace');
              window.addEventListener('popstate', function() {
                window.ReactNativeWebView.postMessage("{type:"pop",url:${window.location.href}}");
              });
            })();
            true;
         `}
          onMessage={({ nativeEvent: state }) => {
            const params=JSON.parse(state.data)
           
            if (params.type === 'pop'||params.type === 'back') {
              this.setState({
                pushCount: this.state.pushCount - 1,
              });
            } else if(params.type==='push') {
              this.setState({
                pushCount: this.state.pushCount + 1,
              });
            }
          }}
        />
 ```

 ## WebView 中 与App 中的 cookies 共享

 做混合开发，经常会遇到webView 中网页要获取app中的登陆状态，但好在react-native-webView 中做了处理
 我们并不需要担心，webView 中的页面 我们只要按正常的请求就可以

 
