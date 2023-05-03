---
title: summary-learn
date: 2019-03-31 22:47:57
tags:
---

http://caibaojian.com/fedbook/learning/seo.html

https://yq.aliyun.com/articles/6272?utm_campaign=wenzhang&utm_medium=article&utm_source=QQ-qun&utm_content=m_10838

## 前端seo优化

## 1.搜索引擎工作原理

## 2.seo(Search English Optimization)简介

 1.为了提升网页在搜索引擎自然搜索结果中的收录数量以及排序位置而做的优化行为 .

​	简单的说就是让你的网站  排在 搜索引擎结果的前面的一种优化行为

​	SEO 是让我们的站点更加符合搜索引擎的胃口

2.

### 3.前端SEO(Search English Optimization)







**SEO**包括**站内优化**和**站外优化：**

一、**站内优化**

1、**网站结构：**树形结构，结构清晰明了，更利于爬虫的爬取

2、**meta标签的优化：**

meta标签的内容，官网在搜索结果的展示中尤其重要：

（1）**title属性：**标题要明确，用户一看就明白这网站是干嘛的。

（2）**keywords属性：**虽然现在主流搜索引擎中对搜索结果的排名几乎没有影响了，但是爬虫会收录，也值得设置。

（3）**discription属性：**为了让官网在搜索结果中，信息展示更完善，需要设置，控制70字以内（多了被折叠）。

如：

![img](https://pic4.zhimg.com/v2-03b1a701c45dd7a4b75a2689ad624557_b.png)![img](https://pic4.zhimg.com/80/v2-03b1a701c45dd7a4b75a2689ad624557_hd.png)

3、**html标签中的h1、h2、h3…h6标签**

h1放最重要的内容，h2次之，一般的中小型网站控制在h3之内。

**4、页面链接的静态化**

5**、****页面中的关键词的词频和密度（一般1%、2%就可以）**

词频和密度对SEO没有太直接的相关性了，可优化，可不优化。不过适当提高一点的话，可能对网络搜索的排名有所提高。

**6、定期更新网站内容，爬虫会更勤快来网站做客哦，能增加被收录的页面~**

二、**站外优化**

**1、外部链接建设**

其他网站有链接指向网站的链接对SEO有帮助，有传递权重、提升形象或者带来流量的作用。

**（1）**友情链接（不要垃圾网站的友链）

**（2）**导航网站中网站目录提交

**（3）**垂直媒体或门户网站发表关于产品或网站的文章带链接（媒体的编辑发表更佳）

**（4）**论坛中回答相关的问题并带上链接（若论坛，如知乎，网站内对我们的链接加“nofollow”属性，对权重没影响，但如果我们的回答是对别人有帮助的，可提升形象和带来流量）

**三、如何增加百度搜索中与网站和产品相关的结果？**

**1、百度百科、文库、经验、知道、地图地址、相关的热门贴吧。**

**2、热门论坛****（如豆瓣、知乎）上与网站和产品等的提问和回答。**



具体可查看马海祥博客《学习SEO优化最常见的100个SEO问答》的相关介绍）。

《百度移动搜索优化指南2.0》



## 谈谈你对jQuery的理解

为什么会有 jQuery 的存在 ？

​	1.各浏览器对DOM的实现不同,浏览器兼容问题

​	2.对获取HTML 元素的DOM对象操作更加便利 

​        3.此外该库还 封装对ajax ，dom，bom 等等操作 的一些方法

###     一个基本的实现?

```
//init 中的原型指挥 Jquery 中的原型
function Jquery (cssselect,context){
    return new Jquery.prototype.init(cssselect,context)
}
Jquery.prototype={
	init:function(){
        
	}
}
Jquery.prototype.init.prototype=Jquery.prototype;
```

### window.load()  与$(fn)的区别

​	1.window.onload是在网页中所有元素**加上所有资源**++完全加++载到浏览器后才执行。

### 谈谈你对boostrap的理解

用于开发响应式布局，移动设备优先的UI框架, 

其核心原理是利用CSS3媒体查询，针对不同的屏幕大小，匹配不同的样式

盒模型采用 border-box

栅格布局

## Bootstrap源码阅读

```
目录结构
  assert
    images
    javascript
    stylesheets
       bootstrap
          mixins 混合器
       
       _bootstrap.scss 主页
```

 改变大小写

```
text-transform: lowercase/uppercase/capitalize;
```

## 迭代器和生成器

**Symbol.iterator**  符号被用于定义对象的默认迭代器

 **迭代器**为何会出现?

如何简单的实现一个迭代器,

```js
function createIterator(items){
    var i=0;
    return {
        next:function(){
             var done=(i>=items.length);
             var value=!done?items[i++]:undefined;
             return {
                 done:done,
                 value:value
             }     
        }
    };
}
```

**迭代器**   --- **专用于迭代的对象**  一个对象而已

迭代器对象都拥有next()方法，返回一个结果对象

**生成器**  ---**返回一个迭代器的函数**

语法    不能将箭头函数创建为生成器

```
function * createIterator(){
    yield 1;
    yield 2;
    yield 3;
}
```

**for -of 循环**  --**留意条件更少**

**创建可迭代对象**

**内置迭代器**

  集合的迭代 数组  Map  Set

entries()   values()  keys()

**Weak Set**       **Weak Map**    并未拥有内置的迭代器

NodeList  的迭代器

**传递参数给迭代器** 

生成器的Return  语句

## Promise 与异步编程---回调地狱的解决方案

回调地狱：嵌套过多的回调函数

pendding 

fulfilled

**创建已决的Promise**

Promise.resolve()

**创建未决的Promise**

Promise.reject()

非 Promise 的 Thenable

串联Promise

Promise.then() 中返回 Promise

响应多个 Promise

Promise.all()

**接受单个可迭代对象(如数组)作为参数,并返回一个Promise.这个可迭代对象的元素都是Promise,**

**只有在他们都完成后，所返回的Promise 才会被完成**



**若传递给  Promise.all()  的任意 Promise 被拒绝了，那么方法所返回的 Promise 就会立刻被**
**拒绝，而不必等待其他的 Promise 结束**



**拒绝处理函数总会接收到单个值，而不是一个数组，该值就是被拒绝的 Promise 所返回的拒
绝值**

```javascript
let p1 = new Promise(function(resolve, reject) {
    setTimeout(function(){
      resolve(42);
    },2000)
});
let p2 = new Promise(function(resolve, reject) {

   setTimeout(function(){
      resolve(43);
    },2000)
});
let p3 = new Promise(function(resolve, reject) {
resolve(44);
});
let p4 = Promise.all([p1, p2, p3]);
p4.then(function(value) {
    //返回按promise.all()中传入Promise 的顺序
console.log(Array.isArray(value)); // true
console.log(value[0]); // 42
console.log(value[1]); // 43
console.log(value[2]); // 44
});
```

promise.race()

在来源Promise 中任意一个被完成时，Promise.race()方法所返回的 Promise 就能作出响应.

```javascript
let p1 = Promise.resolve(42);
let p2 = new Promise(function(resolve, reject) {
resolve(43);
});
let p3 = new Promise(function(resolve, reject) {
resolve(44);
});
let p4 = Promise.race([p1, p2, p3]);
p4.then(function(value) {
console.log(value); // 42
});
/*若胜出的 Promise 是被
完成，则返回的新 Promise 也会被完成；而胜出的 Promise 若是被拒绝，则新 Promise 也会
被拒绝*/
```

集成Promise

## Async  函数 await 

Generator 函数的语法糖

`async`函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖



`async`函数返回一个 Promise 对象，可以使用`then`方法添加回调函数。

当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。



`**async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。**

## 谈谈你对ES6,ES7了解程度

## 微信小程序的开发

## 函数表达式

   什么是函数表达式 ---

**声明一个匿名函数的时候立即复制给一个变量，没有函数提升**

**递归**   arguments.callee() ;  是一个指向正在执行函数的指针

**命名函数表达式**

**闭包**:有权访问另一个函数作用域中的变量的函数

**如何创建闭包**  :一个函数内部创建另一个函数

**闭包的优点**

​        ** 模范块级作用域**

 	**私有变量**

在构造函数中定义一个私有变量和函数

```js
function Test(){
    var a=10;
    function privateFunction(){
        return false;
    }
    this.publicMethod=function(){
        a++;
        return privateFunction();
    }
}
```

**静态私有变量  ---属于这个类有的**

要点:在私有作用域中定义私有变量或函数。

```js
(function(){
    var privateVariable=10;
    function privateFunction(){
        return false;
    }
    //构造函数
    MyObject=function(){}
    MyObject.prototype.publicMethod=function(){
        privateVariable++;
        return privateFunction();
    }
})()
```

**模块模式**

匿名函数立即执行 返回一个单例对象而已

**闭包的缺点**  

由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。

在闭包中使用this 对象也会可能导致一些问题 

**匿名函数的执行环境具有全局性,因此其this对象通常指向window**



## ICARE 源码简单浏览

- react native 开发环境搭建
- android 打包 .....真机调试......chrome 调试
- 搭建APP路由，
- 项目目录结构,
- 工具类封装,
- 样式兼容

### index 文件

```react
import { AppRegistry } from 'react-native';

//导入换成  ----生产环境下 去除 console 的输出
if (!__DEV__) {
  global.console = {
    info: () => {},
    log: () => {},
    warn: () => {},
    debug: () => {},
    error: () => {},
  };
}
import storage from './App/Storage';  //这个是用来

import Root from   './App/Components/Root';  //主入口文件，搭建App路由


AppRegistry.registerComponent('inquiry', () => Root);
```

### Root 文件

```react
import React, { Component } from 'react';
import {
    Platform,
    StyleSheet,
    Text,
    View,
    ScrollView,
    Image,
    TextInput,
    StatusBar,
    TouchableOpacity,
    ImageBackground
} from 'react-native';

import CardStackStyleInterpolator from 'react-navigation/src/views/CardStack/CardStackStyleInterpolator';  //这个让路由切换跟IOS效果一样
import sizeutil  from '../Utils/sizeUtil';


const  screenWidth  = sizeutil.screenWidth;

import Login     from  './Login/Login';
import Notice    from  './Notice/Notice';
import Mine      from   './Mine/Mine';

import Workbenck from  './Workbench/Workbench';

import apiUtil from '../Utils/apiUtil';

//接车
//登记记录
import RegisterRecards   from   './Workbench/AcceptCar/RegisterRecards';
import DragDetails       from   './Workbench/AcceptCar/DragDetails';
import ConfirmDetails    from    './Workbench/AcceptCar/ConfirmDetails';



import  {StackNavigator,TabNavigator,Header } from  'react-navigation';

//构建主路由
const MainScreenNavigator = TabNavigator({
    Workbenck: {
        screen: Workbenck,
        navigationOptions: {
            tabBarLabel: '主页',
            // 将来设置图片用
            tabBarIcon: ({tintColor,focused}) => (
                focused ?
                <Image
                    source={require('../Images/workbench/tab_icon_workbench_focus.png')}
                    style={styles.icon}/>
                :
                <Image
                    source={require('../Images/workbench/tab_icon_workbench_default.png')}
                    style={styles.icon}/>
            ),

        }
    },
    Notice: {
        screen: Notice,
        navigationOptions: {
            tabBarLabel: '消息',
        tabBarIcon: ({tintColor,focused}) => (
            focused ?
            <View>
                <Image
                    source={require('../Images/workbench/tab_icon_notice_focus.png')}
                    style={styles.icon}/>
            </View>
            :
            <View>
                <Image
                    source={require('../Images/workbench/tab_icon_notice_default.png')}
                    style={styles.icon}/>
            </View>
        ),

        }

    },
    Mine: {
        screen: Mine,
        navigationOptions: {
            tabBarLabel: '我的',
            tabBarIcon: ({tintColor,focused}) => (
                focused ?
                <Image
                    source={require('../Images/workbench/tab_icon_mine_focus.png')}
                    style={styles.icon}/>
                :
                <Image
                    source={require('../Images/workbench/tab_icon_mine_default.png')}
                    style={styles.icon}/>
            ),
        }

      }
}, {
    lazy:true,
    tabBarPosition: 'bottom', // 显示在底端，android 默认是显示在页面顶端的
    animationEnabled: false, //切换页面时显示切换效果
    swipeEnabled: false, // 禁止左右滑动
    backBehavior: 'none', // 按 back 键是否跳转到第一个 Tab， none 为不跳转
    tabBarOptions: {
        activeTintColor: '#0685f9', // 文字和图片选中颜色
        showIcon: true, //android默认不展示图片，需要设置为true
        inactiveTintColor: '#0685f9', // 文字和图片默认颜色
        style: {backgroundColor: '#ffffff',height:58},
        indicatorStyle: {height: 0}, // android 中TabBar下面会显示一条线，高度设为 0 后就不显示线了
    },
})
// const ImageHeader = props => (
//     <View style={{backgroundColor: '#eee'}}>
//         <Image
//             style={[StyleSheet.absoluteFill,{width:screenWidth,height:80}]}
//             source={require('../Images/others/bottom_color.png')}
//         />
//         <Header {...props}/>
//   </View>
//   );


/*
* Android 状态栏高度不计入屏幕高度 (translucent似乎可以使其不计入屏幕高度) , 而IOS 状态栏高度是计入屏幕高度 .
*
* IOS 状态栏高度 StatusBar = 20;
* IPhone X 状态栏高度 StatusBar = 44;
*
* headerHeight为了适配Androd和IOS进行特殊处理。
*/
const headerHeight = Platform.OS==='android'?80:44;

//嵌套路由----stackNavigator中嵌套 TabNavigator
//'#004FFA'
const Root = StackNavigator({
    //登录
    Workbenck:{ screen: MainScreenNavigator},
    Login: { screen: Login },
    //工作台
    // //接车
    RegisterRecards :{ screen: RegisterRecards  },
    DragDetails:{ screen: DragDetails },
    ConfirmDetails:{screen:ConfirmDetails},
    Symptoms: {screen:Symptoms},
    PartsList:{screen:PartsList},
    FaultDesc:{screen:FaultDesc},
},
{
    navigationOptions: {
        headerBackTitle: null,
        headerTitleStyle: {alignSelf: 'center',color:'#ffffff'},
        headerStyle: {elevation: 0, shadowOpacity: 0,borderBottomWidth:0,paddingTop:20,height:headerHeight,backgroundColor:'#004FFA'},
        // header:(props) => <ImageHeader {...props} />,
        gesturesEnabled: true,
        headerMode: 'float',
        headerRight:(
            <Text></Text>
        )
    },
    transitionConfig: () => ({
        screenInterpolator: CardStackStyleInterpolator.forHorizontal,
    })
}
);


const styles = StyleSheet.create({
    icon:{
            height: 24,
            width: 24,
            resizeMode: 'contain'
         },
    amount:{
           position:'absolute',
           color:'#ffffff',
           width:16,
           height:16,
           right:0,
           top:0,
           fontSize:10,
           backgroundColor:'#FF4200',
           borderRadius:8,
           textAlign:'center',
           zIndex:100
    }
})


export default Root ;

```

### 分析login页面

```react
import React, { Component } from 'react';
import {
    Platform, //针对不同的平台-   Platform.OS  //android 或者 ios
    StyleSheet,
    Text,
    View,
    ScrollView,
    Image,
    TextInput,
    StatusBar,
    TouchableOpacity,
    ImageBackground,
    Alert,
    Linking
} from 'react-native';

import { KeyboardAwareScrollView } from 'react-native-keyboard-aware-scroll-view'

import Toast from 'react-native-root-toast';

import sizeutil from '../../Utils/sizeUtil';

import Spinner from 'react-native-loading-spinner-overlay';

import HotUpdate  from '../../Commons/Components/HotUpdate';

import apiUtil  from '../../Utils/apiUtil';

import _updateConfig from '../../../update.json';


const {appKey} = _updateConfig[Platform.OS];



const  screenWidth  = sizeutil.screenWidth;
const  screenHeight = sizeutil.screenHeight;
const  pixel        = sizeutil.pixel;

//上次登录的账号
let lastAccount=null;

/*
         测试账号
        dealerCode:'APPMD1',
        account:'13811111111',
        password:'123qwe',
*/
export default class Login extends Component {
    static navigationOptions={
        header:null
    }
    constructor(props){
        super(props);
        this.state={
            dealerCode:'',
            account:'',
            password:'',
            visible: false,
            isLogin:false
        };
        this.login=this.login.bind(this);

    }
    componentDidMount(){
        //2018年7月12日
        //添加热更新
        HotUpdate.checkUpdate();

        this.readStorage();
    }

     //先读取本地缓存有没有登录数据
    readStorage(){
        storage.load({
            key: 'loginState',
            autoSync: false,
        }).then(ret => {
                this.setState(ret);
                lastAccount=ret.account; //保存上一次的账号
        }).catch(err => {
            switch (err.name) {
                case 'NotFoundError':
                    console.log('暂未存储');
                    break;
                case 'ExpiredError':
                    break;
            }
        })
    }
    //这里处理登录的逻辑
    login(){
        let {dealerCode,account,password} =this.state;
        let reqData={
            "serverCode": "managerDealerLogin",
            dealerCode,
            account,
            password
        };
        apiUtil.fetchers('app/managerDealerLogin',reqData,10000)
        .then((json)=>{
            let {serverMsg,showMsg}=json;
            if(serverMsg==="success"){
                //判断...如果却换账号,就清掉上一次账号问诊的本地存储...
                if(lastAccount!==account&&lastAccount!==null){
                    //清除本地....
                    console.log('清除上次的缓存');
                    storage.remove({
                        key: 'InquiryData'
                    });
                }
                this.saveStorage(dealerCode,account,password);
                Toast.show('登录成功', {duration: Toast.durations.SHORT});
                this.props.navigation.navigate('Workbenck',{hasLogin:true});
            }else{
                Toast.show(showMsg, {duration: Toast.durations.SHORT});
            }
            this.setState({
                visible:!this.state.visible
            });
        }).catch((e)=>{
            console.log("请求超时",e);
            if(e==='timeOut'){
                Toast.show('登录超时,请重试', {duration: Toast.durations.SHORT});
            }
            // Toast.show('登录超时,请重试', {duration: Toast.durations.SHORT});
            this.setState({
                visible:!this.state.visible
            });
        })
        this.setState({
            visible:!this.state.visible
        });
    }

    //保存登录信息
    saveStorage(dealerCode,account,password){
        storage.save({
            key: 'loginState',  // 注意:请不要在key中使用_下划线符号!
            data: {
                dealerCode,
                account,
                password,
                isLogin:true,
            }
        });
    }



    render(){

        let {dealerCode,account,password,isLogin} =this.state;
        return (
                    <KeyboardAwareScrollView keyboardShouldPersistTaps='always'>
                        <View style={styles.container}>
                            <StatusBar
                                hidden={true}
                            />
                            <ImageBackground style={{width: screenWidth, height: screenHeight}} source={require('../../Images/login_bg_img.png')}>
                                    <View style={styles.inputWrapper}>
                                        <View style={styles.inputGroup}>
                                            <View style={{flexDirection:'row'}}>
                                                <Image source={require('../../Images/login_icon_business.png')} />
                                                <TextInput
                                                    style={[styles.inputText,{marginTop:0}]}
                                                    onChangeText={(text) => this.setState({dealerCode:text})}
                                                    value={dealerCode}
                                                    placeholder="门店代码"
                                                    placeholderTextColor="#ffffff"
                                                    underlineColorAndroid="transparent"
                                                />
                                            </View>
                                            <View style={styles.underLine}>
                                            </View>
                                        </View>
                                        <View style={styles.inputGroup}>
                                            <View style={{flexDirection:'row'}}>
                                                <Image source={require('../../Images/-login_icon_-acomber.png')}/>
                                                <TextInput
                                                    style={[styles.inputText]}
                                                    onChangeText={(text) => this.setState({account:text})}
                                                    value={account}
                                                    maxLength={11}
                                                    placeholder="账号"
                                                    placeholderTextColor="#ffffff"
                                                    underlineColorAndroid="transparent"
                                                />
                                            </View>
                                            <View style={styles.underLine}>
                                            </View>
                                        </View>
                                        <View style={styles.inputGroup}>
                                            <View style={{flexDirection:'row'}}>
                                                <Image source={require('../../Images/login_icon_password.png')} />
                                                <TextInput
                                                    style={[styles.inputText]}
                                                    onChangeText={(text) => this.setState({password:text})}
                                                    value={password}
                                                    placeholder="密码"
                                                    placeholderTextColor="#ffffff"
                                                    underlineColorAndroid="transparent"
                                                    secureTextEntry={true}
                                                />
                                            </View>
                                            <View style={styles.underLine}></View>
                                        </View>

                                        <View style={[styles.inputGroup,{marginTop:28,}]}>
                                            <TouchableOpacity onPress={this.login}>
                                                <View style={styles.loginBtn}>
                                                    <Text style={styles.loginBtnText}>登 录</Text>
                                                </View>
                                            </TouchableOpacity>
                                        </View>

                                    </View>

                            </ImageBackground>
                            <Spinner visible={this.state.visible}/>
                        </View>
                    </KeyboardAwareScrollView>
                )
    }

}

const styles = StyleSheet.create({
    container:{
        backgroundColor:'rgba(245,245,245,1)',
        width:screenWidth,
        height:screenHeight
    },
    inputWrapper:{
        width:screenWidth,
        height:0.5*screenHeight,
        position:'absolute',
        top:0.43*screenHeight,
    },
    inputGroup:{
        flex:1,
        height:screenHeight*0.059,
        alignItems:'center',
        marginTop:0.03*screenHeight,
    },
    inputText:{
        width:screenWidth*0.6,
        height:screenHeight*0.059,
        color:'#ffffff',
        paddingLeft:0.05*screenWidth,
    },
    loginBtn:{
        width:screenWidth*0.68,
        height:screenHeight*0.06,
        justifyContent:'center',
        alignItems:'center',
        backgroundColor:'#fcf9e9',
        borderRadius:40,

    },
    loginBtnText:{
        fontSize:16,
        color:'#027eff',

    },
    underLine:{
        backgroundColor:'#f3f4c7',
        height:pixel,
        width:0.68*screenWidth,
    }
})


```

## 为什么用React来构建webApp呢？

## Ant -design 源码读计划...

## React

### JSX---描述用户界面

   jsx 是javascript 的语言扩展 

### 渲染

ReactDom.render(<App/>,document.getElementById('root'));

### 组件&Props

组件可是把UI 分割成独立可复用的小部件

组件的概念，什么是组件，从概念上看就是一个函数，接受传进来的参数，然后返回react 元素

###  **两种定义组件的方式**

javascript函数定义    es6类定义

### **组件名称必须以大写字母开头**

**组件的返回值只能有一个根元素。这也是我们要用一个`<div>`来包裹所有`<Welcome />`元素的原因。**

**props 只读性**

**所有的React组件必须像纯函数那样使用它们的props。**

### state & 声明周期

**构造函数是唯一能够初始化 `this.state` 的地方。**

### 事件处理

### 列表 &keys

keys 可以在DOM的某些元素被增加或者删除的时候帮助React 识别哪些元素发生了变化

不要用索引来当key值 

### **JSX 子代是一个函数** 

### 使用 PropTypes 进行类型检查

### Refs &Dom

Refs 提供了一种方式，用于访问在render方法中创建的 Dom节点或React元素



```react
this.myRef=React.createRef()

<input ref={this.myRef}/>
```

使用 `ref` 回调函数，在实例的属性中存储对 DOM 节点的引用。

```react
 this.setTextInputRef = element => {
      this.textInput = element;
    };
    
 <input
          type="text"
          ref={this.setTextInputRef}
        />
```

### 性能优化----重点

**map key**

**shouldComponentUpdate实战**

### Immutable .js     最流行的不可变数据结构

​	**原生应用类型的坑**

```js
//Map()  原生object转Map对象 (只会转换第一层，注意和fromJS区别)
immutable.Map({name:'danny', age:18})

//List()  原生array转List对象 (只会转换第一层，注意和fromJS区别)
immutable.List([1,2,3,4,5])

//fromJS()   原生js转immutable对象  (深度转换，会将内部嵌套的对象和数组全部转成immutable)
immutable.fromJS([1,2,3,4,5])    //将原生array  --> List
immutable.fromJS({name:'danny', age:18})   //将原生object  --> Map

//toJS()  immutable对象转原生js  (深度转换，会将内部嵌套的Map和List全部转换成原生js)
immutableData.toJS();

//查看List或者map大小  
immutableData.size  或者 immutableData.count()

// is()   判断两个immutable对象是否相等
immutable.is(imA, imB);

//merge()  对象合并
var imA = immutable.fromJS({a:1,b:2});
var imA = immutable.fromJS({c:3});
var imC = imA.merge(imB);
console.log(imC.toJS())  //{a:1,b:2,c:3}

//增删改查（所有操作都会返回新的值，不会修改原来值）
var immutableData = immutable.fromJS({
    a:1,
    b:2，
    c:{
        d:3
    }
});
var data1 = immutableData.get('a') //  data1 = 1  
var data2 = immutableData.getIn(['c', 'd']) // data2 = 3   getIn用于深层结构访问
var data3 = immutableData.set('a' , 2);   // data3中的 a = 2
var data4 = immutableData.setIn(['c', 'd'], 4);   //data4中的 d = 4
var data5 = immutableData.update('a',function(x){return x+4})   //data5中的 a = 5
var data6 = immutableData.updateIn(['c', 'd'],function(x){return x+4})   //data6中的 d = 7
var data7 = immutableData.delete('a')   //data7中的 a 不存在
var data8 = immutableData.deleteIn(['c', 'd'])   //data8中的 d 不存在

作者：美团点评点餐
链接：https://juejin.im/post/5948985ea0bb9f006bed7472
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

https://juejin.im/post/5948985ea0bb9f006bed7472

https://juejin.im/post/5a12881a6fb9a044fd115dd4

创造不可突变元素，原对象和突变部分结合成不可突变对象

## Context  

Context 通过组件树提供了一个传递数据的方法，从而避免了在每一个层级手动的传递props

对组件树认为是全局的数据

避免中间组件传递props

> 不要仅仅为了避免在几个层级下的组件传递 props 而使用 context，它是被用于在多个层级的多个组件需要访问相同数据的情景。

## Fragments

React 中一个常见模式是为一个组件返回多个元素。Fragments 可以让你聚合一个子元素列表，并且不在DOM中增加额外节点。

## axios 

https://github.com/axios/axios

https://www.kancloud.cn/yunye/axios/234845

## Jest

http://www.cnblogs.com/Wolfmanlq/p/8012847.html

https://segmentfault.com/a/1190000008628067

https://segmentfault.com/a/1190000009220530

匹配器:验证测试的返回值

toBe() 测试的是值 ，**toEqual** ()是对象

基本语法:

```js
test('1+2等于3',()=>{
    expect(sum(1 , 2)).toBe(3);
})
```

测试异步代码

```js
//回调函数
test('the data is peanut butter', done => {
  function callback(data) {
    expect(data).toBe('peanut butter');
    done();
  }

  fetchData(callback);
});
//承诺验证
test('the data is peanut butter', () => {
  expect.assertions(1);
  return fetchData().then(data => {
    expect(data).toBe('peanut butter');
  }）.catch(e => expect(e).toMatch('error'))
});

//或者
test('the data is peanut butter', () => {
  expect.assertions(1);
  return expect(fetchData()).resolves.toBe('peanut butter');
});
//使用async await

test('the data is peanut butter', async () => {
  expect.assertions(1);
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});

```

Mock Function(模拟器)

Enzyme

全局函数


## 冻结列和表格的研究

## 节流 与防抖动

http://caibaojian.com/throttle-debounce.html

## Vue

## b em strong 标签

b:吸引读者

em:着重阅读

strong:十分强调