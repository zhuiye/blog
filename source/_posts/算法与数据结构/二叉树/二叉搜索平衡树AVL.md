# 二叉搜索平衡树

为什么要有平衡树呢？简单的说，提高搜索效率，不至于 让 二叉搜索树的 查询 变为 0(n)。
要明白 avl 树的算法，首先要明白如下定义:

- 平衡因子
- 左旋，右旋，LR 旋转 RL 旋转 等四种特殊的方式

这个算法是边构建，边调整。平衡因子是指：左子树与右子树的高度之差，如果插入某个节点后，根节点的平衡因子
等于 2，那么我们就需要进行**调整**，而调整 就有 如上的四种方式。也是比较好理解。

- https://www.cs.usfca.edu/~galles/visualization/AVLtree.html

https://zhuanlan.zhihu.com/p/56066942

详解 AVL 树（基础篇） - Name1e5s 的文章 - 知乎
https://zhuanlan.zhihu.com/p/34899732

这是个非常重要的知识点，主要是设计二叉搜索平衡树的构建，要花费较少的时间。
细细研究一下。

- 平衡调整
- 重新建树

## 实现

```c++
//AVL树纪念周赛这波愚蠢操作
struct AVLTreeNode
{
    int data, height;
    AVLTreeNode* left, * right;
    AVLTreeNode(int val) :data(val), left(NULL), right(NULL), height(0) {}
};
int getheight(AVLTreeNode* r)
{
    return r == NULL ? 0 : r->height;
}
/*
   左旋,当左子树的右节点有根时，一般的处理时，把它
   赋给原跟节点的左子节点
*/
AVLTreeNode* leftLeftRotation(AVLTreeNode* k2)
{
    AVLTreeNode* k1 = k2->left;
    // 特别要注意这里的处理
    k2->left = k1->right;

    k1->right = k2;
    // k2 更新之后的高度
    k2->height = max(getheight(k2->left), getheight(k2->right)) + 1;

    // k1 的高度
    k1->height = max(getheight(k1->left), k2->height) + 1;
    return k1;
}
/*
   右旋转
*/
AVLTreeNode* rightRightRotation(AVLTreeNode* k1)
{
    AVLTreeNode* k2 = k1->right;

    // 与左旋相似的操作
    k1->right = k2->left;
    k2->left = k1;
    k1->height = max(getheight(k1->left), getheight(k1->right)) + 1;
    k2->height = max(getheight(k2->right), k1->height) + 1;
    return k2;
}

// LR 模式旋转
AVLTreeNode* leftRightRatation(AVLTreeNode* k3)
{
    //
    k3->left = rightRightRotation(k3->left);
    return leftLeftRotation(k3);
}
// RL 模式旋转
AVLTreeNode* rightLeftRotation(AVLTreeNode* k4)
{
    k4->right = leftLeftRotation(k4->right);
    return rightRightRotation(k4);
}
// 插入构建
AVLTreeNode* insertNode(AVLTreeNode* root, int data)
{
    if (!root)
        root = new AVLTreeNode(data);
    else if (data < root->data)
    {
        // 左旋
        root->left = insertNode(root->left, data);
        if (getheight(root->left) - getheight(root->right) == 2)
            if (data < root->left->data)
                root = leftLeftRotation(root);
            else
                root = leftRightRatation(root);
    }
    else if (data > root->data)
    {
        // 右旋
        root->right = insertNode(root->right, data);
        if (getheight(root->right) - getheight(root->left) == 2)
            if (data > root->right->data)
                root = rightRightRotation(root);
            else
                root = rightLeftRotation(root);
    }
    else
        cout << "fail" << endl;
    // 非常要注意，这里需要不断的更新 二叉树每个节点的高度
    root->height = max(getheight(root->left), getheight(root->right)) + 1;
    return root;
}
AVLTreeNode* Root=nullptr;
void dfs(TreeNode* root)
{
    if(!root) return ;
    Root=insertNode(Root,root->val);
    dfs(root->left);
    dfs(root->right);
}

// 复制一棵树
void build(AVLTreeNode* Troot,TreeNode*& root)
{
    if(!Troot) return ;
    // 构建一个映射
    root=new TreeNode(Troot->data);
    build(Troot->left,root->left);
    build(Troot->right,root->right);
}
class Solution {
public:
    TreeNode* balanceBST(TreeNode* root) {
        Root=nullptr;
        dfs(root);
        TreeNode* ret=nullptr;
        build(Root,ret);
        return ret;
    }
};

```

https://leetcode-cn.com/problems/balance-a-binary-search-tree/comments/
