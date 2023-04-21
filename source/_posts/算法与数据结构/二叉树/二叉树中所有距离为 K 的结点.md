# [二叉树中所有距离为 K 的结点](https://leetcode-cn.com/problems/all-nodes-distance-k-in-binary-tree/)

给定一个二叉树（具有根结点  root），  一个目标结点  target ，和一个整数值 K 。

返回到目标结点 target 距离为 K 的所有结点的值的列表。 答案可以以任何顺序返回。

## 分析

一开始我的思路是分两步，目标节点向上，目标节点向上，组合题目要求的结果，但往目标节点的父节点之上遍历，却又不知道用何种方式。
最后只能无奈看题解。题解实现的方式非常巧妙。

- 通过一个 字典 记录 每个节点的父节点
- 在向下的目标节点遍历的同时，向目标节点的 parent 遍历。
- 借助 from ，来达到一个非常巧妙的遍历不循环

## 代码实现

```javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @param {TreeNode} target
 * @param {number} k
 * @return {number[]}
 */
var distanceK = function (root, target, k) {
  /*借助 map 向上遍历，非常的巧妙，且看实现 */
  const parent = {};
  const ans = [];
  const dfs = (node) => {
    if (!node) return;
    if (node.left !== null) {
      parent[node.left.val] = node;
      dfs(node.left);
    }
    if (node.right !== null) {
      parent[node.right.val] = node;
      dfs(node.right);
    }
  };
  dfs(root);

  const getAns = (target, from, depth, k) => {
    if (!target) return;

    if (depth == k) {
      ans.push(target.val);
      return;
    }
    //向下遍历，不等于 from 是为了，向上遍历的时候再次跳过已经遍历的
    if (target.left != from) {
      getAns(target.left, target, depth + 1, k);
    }
    if (target.right != from) {
      getAns(target.right, target, depth + 1, k);
    }
    /*
         解释下这步，目标节点5 的父节点 3 ，
    */
    if (parent[target.val] !== from) {
      getAns(parent[target.val], target, depth + 1, k);
    }
  };
  getAns(target, null, 0, k);
  return ans;
};
```

## 小结

- 往父节点遍历，可以用一个 字典存储每个节点的父节点.
- 需要考虑 “死循环”的问题，通常需要借助一个**变量** "不等于"
