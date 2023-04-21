---
title: Can't perform a React state update
date: 2020-03-04 20:47:43
tags: React 
---

相信使用react 的时候,都会遇到这么个类式如下的警告

>Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function

大致的意思是,不能在已经卸载的组件上执行 state 的更新操作 ,这是不推荐的操作,在你的应用中可能会内存泄露,修复它,请在useEffect 清理函数中执行一些操作 .这也是我此处遇到的警告

**实际项目中遇到的场景**

首页请求接口获取数据,遭遇未授权,返回302,要求重定向到别的页面。
由于重定向到别的页面,当前页面遭到销毁,而后续的更新还在继续,故此报了这么个警告
大致代码如下:
```ts
  const loadData = useCallback(
    async (passParams: any) => {
      setLoading(true);

      try {
        const response = await api(passParams);

        setData(response);
       
      } catch (err) {
        if (err && err.code !== 302) {
          message.warn(err ? err.cnmsg : '服务器端出现错误', 2);
        }
      }
      // 继续执行这 setLoading
     setLoading(false);
    },
    [api],
  );
```
从上述代码中可以看出,即便我已经跳出页面了,还是会执行 setLoading(false);如此会出现警告,解决方案也简单.如下:
```ts
  const loadData = useCallback(
    async (passParams: any) => {
      setLoading(true);

      try {
        const response = await api(passParams);

        setData(response);
        setLoading(false);
      } catch (err) {
        if (err && err.code !== 302) {
          message.warn(err ? err.cnmsg : '服务器端出现错误', 2);
          setLoading(false);
        }
      }
    },
    [api],
  );
```
在React Native 中我们也切换页面的时候，也常常会遇到,解决也很简单,如下
```js
class ExamplePage extends Component {
    mounted=false
   
    componentDidComponent() {
        this.mounted=true
        fetchData().then(res=>{
            if(this.mounted){
                this.setState(....)
            }
        })
    }

    componentWillUnmount (){
        this.mounted=false
        ....
    }

}
```
至于出现了此等情况,容易造成什么危害,详细请看如下链接

https://www.robinwieruch.de/react-warning-cant-call-setstate-on-an-unmounted-component