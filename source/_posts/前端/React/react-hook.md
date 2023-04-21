---
title: react-hook
date: 2019-12-15 16:53:33
tags: React
---
## 前言

react-hook 是 16.8 版本之后为组件函数提供 state 功能的工具。关于诸多介绍，详细看文档了，下面只讲述我在项目的实际使用。

## react-hook 诞生目的

根据官方的说法，多年用 class 编写组件，发现其中逻辑的复用并不高，尽管有 renderProps 和高阶组件解决方案，但终究不够优雅，组件间嵌套的层数，一不小心就会让人深陷泥潭。为此官方一直寻找一个方式，更大的复用组件中的一些 state 以及耦合的逻辑，由此 react-hook 诞生出来了，当然 react-hook 也不仅仅是逻辑间的复用，还有 state 组织方式，以及类式 class 生命周期的 useEffect hook。

## useEffect

useEffect 这个 hook，里面的门道一点也不简单，目前在项目中使用场景也非常简单，只是在里面处理异步的请求，后续会补充其更多的使用场景。详情请看下面两个链接：

https://overreacted.io/zh-hans/making-setinterval-declarative-with-react-hooks/

https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/

https://www.robinwieruch.de/react-hooks-fetch-data/

useEffect 集合了类式 class 组件的生命周期 componentDidMount(),componentWillUnMount()
componentDidUpdate()。

## 自定义的 hook

大大提升复用率，自定义的 hook 是什么呢，在我看来，它就是一个函数，你可以往里面传一些参数，里面有一个自己的 state，然后返回一个 state 就行了。看下面我封装的代码就可以明白这一点

## 项目中的使用

在最近的重构项目中使用 react-hook，react-hook 不仅赋予了函数组件 state 的能力，而且一个强大之处就是对 state 的复用，这点深有体会。用了 react-hook 不仅代码函数大大缩减，而且可以对不同的 state 声明，以及操作进行划分设置，最厉害的就是复用率大大提升。下面就放上项目封装的 request hook

```js
// 简单示意
function useRequestData(params) {
  const [data, setData] = useState();

  const fetchData = () => {
    // params  参数可以传进请求需要的参数
    request.post(params).then(res => {
      setData(res.data);
    });
  };

  useEffect(() => {
    fetchData();
  }, []);

  // 暴露出fetchData 主要是为了 刷新，查询所用
  return [data, fetchData];
}
```

## useCallback的使用场景
使传递给子组件的函数保持meroize化，这样可以使子组件不必重新渲染。来看一个example

```js
  function Hello({onClick}){
    return <button onClick={onClick}>example text</button>
  }

  function App(){
     const  onClick=()=>{

      }
      
      return <Hello onClick={onClick}/>
  }

```
可以看到每次当父组件重新渲染时，onClick每次都是新的传进Hello,故此引起Hello组件的渲染，引起不必要的渲染，当然，我们一般情况也不必
在意，但我们可以进行优化。优化如下:

```js
   const onClick=useCallback(()=>{},[])
```

## 实现componentWillReceiverComponent|getDerivedStateFromProps

在初次用 hook 封装组件的时候，遇到了需要使用 getDerivedStateFromProps 场景，
官方给出的建议是保存 state 上次的 props,然后跟更新的 props 做比较。实话实说，实现不怎么优雅

https://react.docschina.org/docs/hooks-faq.html#how-do-i-implement-getderivedstatefromprops

请看下面代码

```tsx
import React, { useState, CSSProperties } from "react";
import { Modal, Icon, message } from "antd";

const styles: {
  [key: string]: CSSProperties;
} = {
  modalStyle: {},
  container: {
    width: "100%",
    height: "100%",
    position: "relative"
  },

  leftIcon: {
    fontSize: 40,
    position: "absolute",
    left: 0,
    top: "50%",
    marginTop: -20,
    cursor: "pointer"
  },
  rightIcon: {
    fontSize: 40,
    position: "absolute",
    right: 0,
    top: "50%",
    marginTop: -20,
    cursor: "pointer"
  },
  img: {
    width: "100%",
    height: "auto"
  },
  pictureDesc: {
    fontWeight: "bold",
    marginBottom: 4,
    marginTop: 4,
    textAlign: "center",
    display: "block"
  }
};

interface PictureScrollViewProps {
  visible: boolean;
  picIndex?: number;
  close: () => void;
  data: Array<{
    type_name: string;
    url: string;
  }>;
}

const PictureScrollView: React.FC<PictureScrollViewProps> = ({
  visible,
  close,
  data,
  picIndex = 0
}) => {
  const [currentIndex, setCurrentIndex] = useState(picIndex);

  /*  目的是当picIndex发生变化,
      更新它
      https://react.docschina.org/docs/hooks-faq.html#how-do-i-implement-getderivedstatefromprops
  */
  let [prevIndex, setPreIndex] = useState < number > 0;

  if (picIndex !== prevIndex) {
    setPreIndex(picIndex);
    setCurrentIndex(picIndex);
  }

  const last = () => {
    if (currentIndex === 0) {
      message.warn("已到第一张图片");
    } else {
      setCurrentIndex(currentIndex - 1);
    }
  };

  const next = () => {
    if (currentIndex === data.length - 1) {
      message.warn("已到最后一张图片");
    } else {
      setCurrentIndex(currentIndex + 1);
    }
  };

  return (
    <Modal
      visible={visible}
      footer={null}
      title={null}
      onCancel={() => {
        setCurrentIndex(picIndex);
        close();
      }}
      width={600}
    >
      {data.length !== 0 ? (
        <div style={styles.container}>
          <picture>
            <figcaption style={styles.pictureDesc}>
              {data[currentIndex].type_name}
            </figcaption>
            <img src={data[currentIndex].url} alt="xxx" style={styles.img} />
          </picture>
          <Icon
            type="left-circle"
            theme="filled"
            style={styles.leftIcon}
            onClick={last}
          />
          <Icon
            type="right-circle"
            theme="filled"
            style={styles.rightIcon}
            onClick={next}
          />
        </div>
      ) : (
        <div>无图片数据</div>
      )}
    </Modal>
  );
};
export default PictureScrollView;
```

上面的组件封装的是一个照片轮播墙，picIndex 是指当前的照片是哪一张，组件初入挂载，
picIndex 从组件外传进来，关闭，再次点击另一张照片，picIndex 要相应改变，故此要监听 picIndex prop 的变量，从而同步更新内部的 picIndex state .


