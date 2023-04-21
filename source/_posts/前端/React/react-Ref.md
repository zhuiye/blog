---
title: React Ref
date: 2019-12-14 14:36:10
tags: React
---

## 基本使用
某些情况下,我们需要获取组件的实例,或者 react 元素的 Dom 节点,,这个时候就得用到 ref,关于 ref 的基本使用方法有两种:

1. ``this.ref=createRef()``
2. ``<ReactDom ref={(ref)=>{this.ref=ref }}/>``

基本使用 : https://zh-hans.reactjs.org/docs/refs-and-the-dom.html

## 项目中的使用
最近在做的项目的时候,遇到一个需求.十几个输入框或者选择框组成一个表单域,然后整体对这个"表单域"进行增加.正常思维就是把这个"表单域"封装为一个组件,我就起名为了 SchemeForm ,然后组件内就声明一个方法获取数据 ,就叫getFormData(),类的结构大致如下:

```js
class SchemeForm  extends component {
    getFormData(){
        // 此方法进行表单验证,以及获取数据,
    }

    render(){
        ...
    }
}
```
App.js

```js
import React from 'react';

class App extends React.Component {
  state = {
    data: [{ name: 'aa' }],
  };

  formRefs = [];

  getFormData() {
    // 此方法进行表单验证,以及获取数据,
  }

  add = () => {
    this.formRefs = [];
    this.setState({
      data: data.concat({ name: 'bbb' }),
    });
  };

  getRef(refs, item) {
    console.log('执行...', refs, item);

    if (refs) {
      this.formRefs.push(refs);
    }
  }
  /*
     此执行结果也一样
    <SchemeForm key={index} ref={this.getRef.bind(this,item)}/>
  */

  render() {
    const { data } = this.state;
    // 保险起见,每次render 函数被调用都重置formRefs
    this.formRefs = [];
    return (
      <div>
        {data.map((item, index) => (
          <SchemeForm
            key={index}
            ref={ref => {
              console.log(ref, item);
              if (ref) {
                this.formRefs.push(ref);
              }
            }}
          />
        ))}
        <button onClick={this.add}>+</button>
      </div>
    );
  }
}
```

日志输出如下:
```js
初入渲染:
  输出 (组件的ref,{name:'aa'})
点击+按钮:
  输出 (null,{name:'aa'})
  输出 (组件的ref,{name:'aa'})
  输出 (组件的ref,{name:'bb'})
再次点击:
  输出 (null,{name:'aa'})
  输出 (null,{name:'bb'})
  输出 (组件的ref,{name:'aa'})
  输出 (组件的ref,{name:'bb'})
  输出 (组件的ref,{name:'bb'})

```
经过观察,里面的回调函数,会执行上一次 render 里面 ref 绑定的回调函数.是在怪哉,不明,但终归动态的获取到了组件的 ref 数组

### 收获
经过折腾得知,ref 里面的回调函数,是在 render 函数之后才执行的,在 componentDidMount 和 compnentDidUpdate 可以获取得到 。ref的用法，在项目中也经常可见，如,在最APP中根元素，放置一个Modal组件,然后把ref保持出来存在一个类中，这样，我们就可以在项目其它地方可以调用 ref.show(config),把模态框展现出来。