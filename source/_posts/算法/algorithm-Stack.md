---
title: Stack
date: 2020-02-05 11:06:38
tags: algorithm
---

## 前言

今天就来总结一下最近学习栈的内容，栈：**后进先出的一种数据结构**，就像叠书本一样，一层一层叠加，每次取最顶层的书，也是最后一次放入的书。栈的实现有很多种，有队列模拟,数组储存出入栈数据，hash 栈实现(猜想)。


## 栈的实现方式

根据栈的特性,栈要实现的方法如下:

```js
   pop()  // 出栈,并返回出栈的元素
   push(el)  //入栈
   size()  // 栈数量
   clear()  //清栈
   isEmpty()  //栈是否为空
   peek()   // 返回栈顶元素
```


### 存储元素采用数组

```js
  class Stack {
      constructor() {
        this.elements = [];
      }

      push(el) {
        this.elements.push(el)
      }

      pop() {
        return this.elements.pop()
      }

      clear() {
        this.elements = []
      }

      size() {
        return this.elements.length;
      }

      peek() {
        return this.elements[this.size() - 1];
      }

      isEmpty() {
        return this.size() === 0;
      }

      print() {
        console.dir(this.elements)
      }
    }
```
还有其它栈的实现方式，用队列实现也有，有空再深入探究.

## 运用

栈的数据结构比较简单，但在项目实战中，我没有**显式**使用过它解决过问题。栈 是一种数据结构，首先要明确这一点，重点关注的是**通过出栈的方式，取栈顶的值**，运用此特性，可以解决 **进制转换**,**字符串匹配**,**图的遍历**等等

## leetCode


### [有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

>给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

    左括号必须用相同类型的右括号闭合。
    左括号必须以正确的顺序闭合。

**注意空字符串可被认为是有效字符串**。

此题目可谓是非常经典，栈的特性起手运用,哪种情况值入栈，出栈，stack的内元素的数量判断。简述下解题的思路。
1. 创建一个栈
2. 遍历字符串，争对 6个特殊字符进行第三步处理
3. 假设 遇到 "( " ,加入栈，假设下一个字符为" ) ",然后比较 （ 于 )匹配, 出栈 ( , 如果 下一个字符 为 其它字符，则加入 栈中 ，后续一直持续此操作
4. 遍历结束，判断栈是否为空，如果空就 true ,不为空就false

js 实现如下:

```js
   const isValid = function (s) {
      let result = true;
      if (!s) {
        return result
      }
      let stack = new Stack();

      for (let char of s) {
        if (char === '(' || char === '[' || char === '{') {
          stack.push(char)
        } else {
          let top = stack.peek(); // 取栈顶元素
          if (top === '(' && char === ')' || top === '[' && char === ']' || top === '{' && char === '}') {
            stack.pop()
          } else {
            stack.push(char)
          }

        }
      }

      return stack.isEmpty()
    };

    console.log(isValid("()[]{}"))
    console.log(isValid("(]"))
    console.log(isValid("([)]"))
```

### [最小栈 (辅助栈 空间换取时间)](https://leetcode-cn.com/problems/min-stack/)

设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

    push(x) -- 将元素 x 推入栈中。
    pop() -- 删除栈顶的元素。
    top() -- 获取栈顶元素。
    getMin() -- 检索栈中的最小元素。

此题要用到辅助栈的概念,以牺牲一定的空间换取时间.接替的思路如下：
1. 创建一个数据栈和辅助栈
2. 每push 一个元素，就要与辅助栈的栈顶元素做比较，如果大于等于，就把栈顶元素加入辅助栈中，否则，就把该值加入辅助栈中。
可以看出，辅助栈的数据 与 数据栈的数据是**同步**的，辅助栈数据的意思，是 每轮加入的最小值.

实现代码:
```js 
  class MinStack {
      constructor() {
        this.data = new Stack();
        this.helper = new Stack();
      }

      push(x) {
        this.data.push(x);
        /*
          这段代码实现比较精妙，保持数据栈与辅助栈互相对应每一轮中的最小值

          比如说:
          如果入栈 1 2 -1
          辅助栈经过处理为 1 1 -1
          我们来看一下执行过程,
         1 入栈，辅助栈为空，自身只有一个值最小，也就是第一轮，最小值为1,直接入栈,
         辅助栈数据为1
         2 入栈，第二轮，与辅助栈的栈顶比较，得出第二轮得最小值，入栈 1<2 ,1入栈
         -1 入栈，与辅助栈栈顶1比较，因为1>-1所以这第三轮中得最小值为-1
         
        */
        if (this.helper.isEmpty() || x <= this.helper.peek()) {
          this.helper.push(x)
        } else {
          this.helper.push(this.helper.peek())
        }
      }

      pop() {
        this.helper.pop();
        return this.data.pop()
      }

      top() {
        return this.data.peek()
      }

      getMin() {
        return this.helper.peek();
      }
    }
```
### [队列实现栈](https://leetcode-cn.com/problems/implement-stack-using-queues/)

此题解法甚是巧妙，思路如下:队列入队 1 2 3 ,我们只需要转化成队列 3 2 1 即可 模拟栈了,
在push 中转化顺序 甚为精妙,如下所示:
```js
      class MyStack {
    
      constructor() {
        this.queue = new Queue();
      }

      pop() {
        return this.queue.shift()
      }

      push(el) {
        // 这步的操作很巧妙...
        
        if (this.queue.isEmpty()) {
          this.queue.push(el)
        } else {
          this.queue.push(el)
          let len = this.queue.size(); // 3
          
          for (let i = 1; i < len; i++) {
            /*
               把自己的头加到自己的尾部
            */
            this.queue.push(this.queue.shift())
          }
        }
      }

      top() {
        return this.queue.front()
      }

    }
```