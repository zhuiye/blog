# 滑动窗口

从字面上来看，很容易理解，就是一个窗口，向前一步步移动，遇到的元素，我们窗口某个特性(比如窗口内所有元素的和)增加这个元素，与此同时，我们的窗口左边可以做一些收缩，改变窗口的大小，或着保持一定的长度。而后，right 继续向前移动，直到遍历结束。下面我们看一道例题感受下。

核心：利用指针

## 必须要深入探究窗口形成的条件.

- https://leetcode-cn.com/problems/longest-substring-with-at-least-k-repeating-characters/

  有时候，不容易找出滑动窗口，滑动窗口成立的条件不存在，为此我们自己要创造 条件，如这一道题中的 "p" 就是 最长重复字符中字符种类的数量，1-26 个

## 同时维护两个窗口

- https://leetcode-cn.com/problems/binary-subarrays-with-sum/

这题还看不懂 ...

## 典型-求 k 个连续数组之和的最大值

```js
function getMaxSum(nums,k) {
  const n = nums.length;

  let left = 0;

  let right = 0;

  let maxSum = 0;
  let sum = 0;
  for(let i=0;i<k,i++){
    sum+=nums[i]
  }

  maxSum=sum;

  for(let right=k;right<right;right++){
      // 向前移动 窗口（某种特性）.. 加一个，减一个
      sum+=nums[right]-nums[left]
      left++
      maxSum=Math.max(sum,maxSum)

  }
   return maxSum
}
```

从上面那道题中，我们看到 sum 的值一直随着左右指针的移动，不断改变着，一直 以 0(1)的时间复杂度，计算着长度为 k 的连续数组的元素之和。

## 滑动窗口问题关键字眼

连续，最多，强调顺序性，一组之内的差，和，乘积，一组之内最多做少能有多少个字符，字符替换等等

## 滑动窗口的求解

- 双指针
- 左指针 也可能一步步的移动，也可能跳跃式收缩，窗口的长度可固定，可不固定。右指针，大部分都是一步步移动
- 移动的过程，我们要对窗口内元素组合 而成的 某个 特性（如:和，乘积）做相应的 增加，减少。
- 最优解，就是在每次窗口元素结合特性的最优值

- 窗口内有时候需要借助 set,数组来 维护 “某种特性”

## 解析复杂题意

窗口滑动，有时候，并不太明确的把窗口找出来，不知道怎么滑，或者很复杂，不知道如何下手，那么我们就要**转化题意了**。

我们来看下列的一道题:

### 1004. 最大连续 1 的个数 III

```

给定一个二进制数组 nums 和一个整数 k ，如果可以翻转最多k 个 0 ，则返回 数组中连续 1 的最大个数 。

示例 1：

输入：nums = [1,1,1,0,0,0,1,1,1,1,0], K = 2
输出：6
解释：[1,1,1,0,0,1,1,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 6。
示例 2：

输入：nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
输出：10
解释：[0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 10。
```

翻转数字，最大连续 1 的个数，如果直接来的话，都不知道如何操作，也不知道如何把这个窗口给找出来。
这个时候就得转换题意了。
**把「最多可以把 K 个 0 变成 1，求仅包含 1 的最长子数组的长度」转换为 「找出一个最长的子数组，该子数组内最多允许有 K 个 0 」。** 转化成这样，那么这道题解就出来了。

```js
var longestOnes = function (nums, k) {
  let left = 0;
  let zoreCnt = 0;
  let ans = 0;
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] === 0) zoreCnt++;

    if (zoreCnt > k) {
      while (nums[left] === 1) {
        left++;
      }
      left++;
      zoreCnt--;
    }
    ans = Math.max(i - left + 1, ans);
  }
  return ans;
};
```

https://leetcode-cn.com/problems/max-consecutive-ones-iii/solution/fen-xiang-hua-dong-chuang-kou-mo-ban-mia-f76z/

## 窗口的大小不固定伸缩

解决的方案中特点

- 一定长度的数组 窗口
- 双指针 同向移动
- 每移动一次 在窗口中 构建解
- 窗口中的 特性（奇数，偶数，x 的数量）移动 1 次，特性要相应更新

下面来一道题，从双暴力之上优化而来的窗口滑动。

### [爱生气的书店老板](https://leetcode-cn.com/problems/grumpy-bookstore-owner/)

### 暴力解法

```js
/**
 * @param {number[]} customers
 * @param {number[]} grumpy
 * @param {number} minutes
 * @return {number}
 */
var maxSatisfied = function (customers, grumpy, minutes) {
  /*
     具体算法思路如下：
     1:先算出没有使用技巧时的顾客满意人数
     2:开始遍历，每次在一定区间内计算出 顾客的满意人数
  */

  const n = grumpy.length;

  let cnt = 0;

  let maxCnt = 0;

  for (let i = 0; i < n; i++) {
    if (grumpy[i] === 0) {
      cnt += customers[i];
    }
  }

  for (let i = 0; i <= n - minutes; i++) {
    let j = i;
    let wCnt = cnt;
    /*
        可以看到，我们每前进一步，都要计算，并加上 区间的值。
        时间复杂度为0(n),为此要优化，就需要考虑窗口滑动了。
        ⚠️：重复的计算区间的值，我们可以优化成串口滑动
    */
    while (j < i + minutes) {
      if (grumpy[j] === 1) wCnt += customers[j];
      j++;
    }
    maxCnt = Math.max(maxCnt, wCnt);
  }
  return maxCnt;
};
```

### 窗口滑动优化

```js
/**
 * @param {number[]} customers
 * @param {number[]} grumpy
 * @param {number} minutes
 * @return {number}
 */
var maxSatisfied = function (customers, grumpy, minutes) {
  const n = grumpy.length;

  let cnt = 0;

  let maxCnt = 0;

  for (let i = 0; i < n; i++) {
    if (grumpy[i] === 0) {
      cnt += customers[i];
    }
  }

  /*上面算出无秘密技巧 */

  let j = 0;

  let wCnt = cnt;
  while (j < minutes) {
    if (grumpy[j] === 1) wCnt += customers[j];
    j++;
  }

  /*上面从第一分钟开始使用技巧 */
  maxCnt = wCnt;
  // 往前加入一个， 是 grumpy 的索引在移动

  for (let i = 0; i <= n - minutes; i++) {
    // 移动
    if (grumpy[i] === 1) {
      // 去除第一个
      wCnt = wCnt - customers[i];
    }
    if (grumpy[i + minutes] === 1) {
      wCnt = wCnt + customers[i + minutes];
    }

    maxCnt = Math.max(maxCnt, wCnt);
  }
  return maxCnt;
};
```

### 如果题意要求涉及字母的比较，我们可以用一个数组进行计数.

```js
let s1Cnt = new Array(26).fill(0);
const s1Len = s1.length;

for (let i = 0; i < s1Len; i++) {
  s1Cnt[s1[i].charCodeAt() - "a".charCodeAt()]++;
}
```

```
567. 字符串的排列
     给你两个字符串 s1 和 s2 ，写一个函数来判断 s2 是否包含 s1 的排列。如果是，返回 true ；否则，返回 false 。

换句话说，s1 的排列之一是 s2 的 子串 。

示例 1：

输入：s1 = "ab" s2 = "eidbaooo"
输出：true
解释：s2 包含 s1 的排列之一 ("ba").
示例 2：

输入：s1= "ab" s2 = "eidboaoo"
输出：false
```

### 题解

一开始很容易想到，直接求 ab 的排列，然后遍历进行窗口内的字字符串的比较,这很明显是暴力解，不优美，那么我就想了，
怎么能直接比较呢，我就观察 他们的比较规律，其实转换问题就是
**s1 长度的每一个字符出现的次数 与 s2 中 s1 长度 的每一个字符出现的次数相等**
那么我们就可以求出我们的解了，怎么统计字母出现的个数呢？我记得以前用数组表示过，所以我们就有了如下代码，

保持 s1 长度的窗口口，一直在 s2 中移动，与此同时，比较是否一致，那么一致 ，返回 true,否则返回 false

```js
/**
 * @param {string} s1
 * @param {string} s2
 * @return {boolean}
 */
var checkInclusion = function (s1, s2) {
  if (s1.length > s2.length) return false;

  let s1Cnt = new Array(26).fill(0);
  const s1Len = s1.length;

  for (let i = 0; i < s1Len; i++) {
    s1Cnt[s1[i].charCodeAt() - "a".charCodeAt()]++;
  }

  let s2cnt = new Array(26).fill(0);

  for (let i = 0; i < s2.length; i++) {
    s2cnt[s2[i].charCodeAt() - "a".charCodeAt()]++;

    if (i >= s1Len - 1) {
      if (i > s1Len - 1) {
        s2cnt[s2[i - s1Len].charCodeAt() - "a".charCodeAt()]--;
      }
      if (s1Cnt.toString() === s2cnt.toString()) return true;
    }
  }
  return false;
};
```

## 大佬心得。

什么情况下会想到滑动窗口法：

任何题目如果没有思路其实都可以想一下暴力解法。这道题暴力解法思路简单：

遍历任意 i，j，使得 i 和 j 之间的子串长度，等于 p 串的长度。该子串称之为 x。该步复杂度为 O（n）。
判断 x 是否与 p 是异位词。是的话，则把 i 加入答案中。该步复杂度为 O（n）。
暴力法的复杂度为 O（n^2）。显然不高效。

可以发现第二步其实做了很多不必要的操作，例如[i, j]和[i+1, j+1]两个子串在暴力法第二步中，需要各遍历一次，完全没必要。其实[i+1, j+1]完全可以在[i, j]的基础上做判断，也就是去掉头部的字符（i 位置），加上尾部的字符（j+1 位置）。这样第一步的复杂度可以降到 O(1)。整体复杂度降到 O(n)。已经得到信息不重复使用就浪费了，没必要重新搜集近乎相同的信息。这就是滑动窗口法。

滑动窗口法的特点是，一连串元素的信息，可以用常数时间推断出，该串整体移位后，新串信息。

所有滑动窗口问题，如果能从暴力法优化的角度思考，都不难想到。

## 求滑动窗口内的最大值和最小值。常见的方法有：

使用 multiset、TreeMap 等数据结构；
单调递增队列或者单调递减队列；

## 关于 两组之间的串口滑动，还未研究
