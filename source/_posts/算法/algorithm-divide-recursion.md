---
title: 分治与递归
date: 2020-04-25 20:06:23
tags: algorithm
---

## 分治

分治算法,比较经典的算法思想,且看它的描述:把问题分割多 N 多个子问题,然后
求解子问题的答案,利用递归把子问题的结果**合并处理*返回.

从上面可以得出以下结论:
1. 一般把大问题进行分割,然后变成一对一求解
2. 一般配合递归,返回"子问题求解的结果,然后进行**合并**"

## 递归

递归就是，不断的调用自身函数，直到执行某一个条件，递归终止，返回某个值
特点如下：

1. 不断调用函数自身，通常对 传入的值进行切分
2. 有一个终止函数调用的条件，通常对传入函数的值进行判断
3. 对函数栈返回的结果进行组合

下面来几个简单的例子

### n 的阶乘

计算公式如下：
n！=n(n-1)\*(n-2)----\*(1)

如: n=6

n!=6\*5\*4\*3\*2\*1

 观察可知，把 6 不断的分解成1(递进),直到n=1,返回的结果相乘（归），代码实现如下
```js
function factorial(n){
   if(n===1){
       return 1;
   }
   return n*factorial(n-1)
}

```
递归的实现，符合计算机的逻辑，非常的巧妙，一开始很不习惯递归，
看思考多了，却发现，大多数问题都可以用递归的思想去思考，当然，递归又可以用for循环改写


### 外观序列

    n        结果         描述
    1.  ==>   1           1
    2.  ==>   11          1 个 1
    3.  ==>   21          2 个 1
    4.  ==>   1211        1 个 2  1 个 1
    5.  ==>   111221      1 个 1  1 个 2  2 个 1

递归解法

观察上面，我们可以看出，我们都是依据上一项来描述当前项的，
所以，我们可以用递归的思维去思考问题。

对于n=2 时，字符串 '21',它产生的下一项，
我们可以通过代码这样去描述
count =1 ,遍历'21',如果前一面与后一项相等，
我们就把count+1,如果不相等，我们就加入 count+str[i],重置
count=1

下面的代码就是体现了这一过程

```js
const countAndSay = function (n) {
  if (n === 1) {
    return "1";
  }
  let base = countAndSay(n - 1);
  let result = "";
  let count = 1;
  for (let i = 0; i < base.length; i++) {
    if (base[i] === base[i + 1]) {
      count = count + 1;
    } else {
      result += count + base[i];
      count = 1;
    }
  }
  return result;
};
```

## 归并排序

最经典的运用分治算法思想进行排序,对一组 N 个无序数组排序,利用分治思想来解决此问题:

### 分割大问题成小问题

这或许是最难的一步,对于排序,我们一般的想法就是冒泡(两个 for 循环搞定),或者**选择排序**(每一轮循环,取最大值
或最小值,然后放在相应位置),**插入排序**(每一轮前后比较,小在前,大在后).切换分治的思想来考虑排序
我们可以先对两个数排序,产生排序后,我们再与第三个数进行排序,与此类推,最后成为一个排序
好了数组.可以看出,**最后的排序结果,依赖于前一轮的排序结果**,**也就是子问题的结果,通过递归进行组合成大问题的结果**,

#### 如何查找子问题

注意几个字眼,**分**,思考的方向,**取两个数,求出问题结果,然后再拿这两个数与下一个数进行某种运算得出结果,一层一层往上嵌套**. 如果
符合这种特征,说明适用分治算法解决此题

#### 排序分治实现

了解思想是一回事,实现又是一回事,由于分治算法,一般配合的递归,比较难看懂代码,但都有
一般的实现套路

```js
// 主函数
function mergeSort(array) {
  // 分解过程
  if (array.length > 1) {
    const { length } = array;
    //  分割 数据 成两边,一直递归直至两边都为1个元素
    const middle = Math.floor(length / 2); // {2}
    const left = mergeSort(array.slice(0, middle));
    const right = mergeSort(array.slice(middle));

    // 对两边的结构进行排序
    array = merge(left, right); // {5}
  }
  //  分割后的子项以及 子项合并排序之后的结构, 这里两个数据共用了
  return array;
}
/*
  merge 函数的解说,粗略一瞥很复杂,还得细看

  [1,2]  [3,4]

*/

function merge(left, right) {
  let i = 0;
  let j = 0;
  const result = [];
  //  排序--  列如 [1,3] [2,4]
  while (i < left.length && j < right.length) {
    result.push(left[i] < right[j] ? left[i++] : right[j++]); // {8}
  }
  //合并的意思,是吧剩余的加进去
  return result.concat(i < left.length ? left.slice(i) : right.slice(j)); // {9}
}
```

#### 实现套路:

1. 一般有个主函数和一个合并结果的辅助函数
2. 一般把元素分割成两部分,然后递归的调用主函数,把分割的两部分 left 和 right ,然后把 left, right 传入 辅助函数中,返回结果
3. 主函数 一般有两个返回, 一个是 递归条件的结果,一个是合并结果的返回

代码模板如下:

```js
function mainFunction(arr) {
  if (arr.length === 1) {
    return arr[0];
  }
  if (arr.length > 1) {
    const middle = Math.floor(length / 2); // {2}
    // 也不一定是对半切.....
    const left = mergeSort(array.slice(0, middle));
    const right = mergeSort(array.slice(middle));
    result = merge(left, right); // {5}
  }

  return result;
}

function merge(left, right) {}
```

## 运算字符串组合数

题目描述如下:

给定一个含有数字和运算符的字符串，为表达式添加括号，
改变其运算优先级以求出不同的结果。你需要给出所有可能的组合的结果。有效的运算符号包含+,-以及\*。

```
  示例 1:输入: "2-1-1"
  输出: [0, 2]
  解释:
  ((2-1)-1) = 0
  (2-(1-1)) = 2

  示例 2:输入: "2*3-4*5"
  输出: [-34, -14, -10, -10, 10]
  解释:
  (2*(3-(4*5))) = -34
  ((2*3)-(4*5)) = -14
  ((2*(3-4))*5) = -10
  (2*((3-4)*5)) = -10
  (((2*3)-4)*5) = 10
```

这道题说实话,不太那么容易看出分治算法的实现,目前我也无法用语言表达
一图胜千言,按操作符号分割字符串

<img src="../images/fen.png">

实现如下 :

```js
function diffWaysToCompute(str) {
  if (Number(str)) {
    return [Number(str)];
  }

  let len = str.length;

  const result = [];

  for (let i = 0; i < len; i++) {
    if (str[i] === "+" || str[i] === "-" || str[i] === "*") {
      let left = diffWaysToCompute(str.substring(0, i));
      let right = diffWaysToCompute(str.slice(i + 1));

      // 合并结果处理
      for (let lv of left) {
        for (let rv of right) {
          switch (str[i]) {
            case "+":
              result.push(lv + rv);
              break;
            case "-":
              result.push(lv - rv);
              break;
            case "*":
              result.push(lv * rv);
          }
        }
      }
    }
  }
  return result;
}
```

## 求数组字符串中的最长公共子串

对数组字符串进行分割,然后两两比较,递归合并结果,跟归并排序差不多

```js
const searchCommonStrTwo = function (strArr) {
  if (strArr.length === 0) {
    return "";
  }
  let prefix;
  if (strArr.length === 1) {
    return strArr[0];
  }

  if (strArr.length > 1) {
    const length = strArr.length;
    const mod = Math.floor(length / 2);
    let left = searchCommonStrTwo(strArr.slice(0, mod));
    let right = searchCommonStrTwo(strArr.slice(mod));
    prefix = searchTowStr(left, right);
  }

  return prefix;

  // let prefix = strArr[0];
};

function searchTowStr(left, right) {
  let prefix = left;
  while (right.indexOf(prefix) !== 0) {
    prefix = prefix.substring(0, prefix.length - 1);
    if (!prefix) return "";
  }
  return prefix;
}

console.log(searchCommonStrTwo(["axx", "xx3", "xx1"]));
```

## 反转单链表

题目描述: 链表为：1->2->3->4。反转后为 4->3->2->1

此题也可用分治递归的思想,来解决,我们可以这样想,且看思想:

3->4 ===> 4->3
2 4->3 ===> 4->3->2
1 4 3 2 ====>4->3->2->1

过程如下:先交换 3 和 4 得到结果 结果 4->3

第二轮 2 和 4->3 这个结果组合 ,得到 4->3->2 ,可以观察到是加在了结果链表的最后一个节点

第三轮 1 和 4->3->2 这个结果组合,得到 4->3->2-1 ,此时递归结束,得到结果

### 实现代码如下:

```js
const testLink = {
  val: 1,
  next: {
    val: 2,
    next: {
      val: 3,
      next: null,
    },
  },
};

function revertLink(testLink) {
  // 寻找递结束的条件
  if (!testLink) {
    return testLink;
  } else {
    let result = revertLink(testLink.next);

    if (result === null) {
      return testLink; //返回最后一个节点
    }

    // 把上一个节点,加到结果节点的最后一个节点上
    let header = result;

    while (header.next !== null) {
      header = header.next;
    }

    header.next = testLink;
    testLink.next = null;

    return result;
  }
}

console.log(revertLink(testLink));
```

此道题可以发现,一般递归都伴随着分治的思想

## 给定一个数字数组,和一个数,去除数组中和这个数相同的一项

如: nums=[1,2,2,4,5] value=2 返回 [1,4,5] 数组中的顺序无关要紧

也可以用分治的思想来实现

数组中的每个数与 value 比较 得到的结果合并就是我们的过滤后的数组

步骤如下:
1 和 2 比较 ,如果 相等 就返回 [] 如果不相等得到集合结果 [1],
2 与 2 比较, 得到 [] ,与上一个比较结果 合并,得到 [1]
2 与 2 比较, 得到 [] ,与上一个比较结果 合并,得到 [1]
4 与 2 比较, 得到 [4] ,与上一个比较结果 合并,得到 [1,4]
5 与 2 比较, 得到 [5] ,与上一个比较结果 合并,得到 [1,4,5]

代码实现如下:

```js
const removeElement = function (nums, val) {
  let result = [];
  if (nums.length === 1 && nums[0] !== val) {
    return nums;
  } else if (nums.length === 1 && nums[0] === val) {
    return [];
  } else {
    const value = nums.slice(0, 1);

    result = removeHelp(nums.slice(1), val);

    if (value[0] !== val) {
      return result.concat(value);
    }
    return result;
  }
};
```

## 移除链表中的重复元素

如: 1->1->2 1->2

算法思路如下:
链表末尾元素 : 1 和 2 比较 1->2

1 和 1->2 比较 1=1 ,返回 1->2

```js
const sortLink = {
  val: 1,
  next: {
    val: 1,
    next: {
      val: 2,
      next: {
        val: 3,
        next: {
          val: 3,
          next: null,
        },
      },
    },
  },
};

function deleteDuplicates(head) {
  if (!head) {
    return null;
  } else {
    let result = deleteDuplicates(head.next);
    if (result === null) {
      return head;
    }
    if (result.val !== head.val) {
      head.next = result;
      return head;
    } else {
      return result;
    }
  }
}
```


## 小结

分解->解决->合并

分解：分解原问题为结构相同的子问题（即寻找子问题）
解决：当分解到容易求解的边界后，进行递归求解
合并：将子问题的解合并成原问题的解
