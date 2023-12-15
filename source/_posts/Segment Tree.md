---
title: Segment Tree
url: segment-tree
date: 2020-01-15 10:38:00
description: 线段树
categories: Computer Science
tags: [Algorithm]
---

## 引言
[Leetcode307](https://leetcode.com/problems/range-sum-query-mutable/)这道题如果没有优化, 那么单次query的时间复杂度$O(n)$, 单次update复杂度$O(1)$  
如果用前缀和数组, 那么单次query的时间复杂度$O(1)$, 单次update复杂度$O(n)$, 因为update(i)会使得前缀和数组i以后的元素均更新

因此如果query和update非常多次, 上面的2种方式效率都比较低.

这道题除了使用树状数组，还可以使用线段树。  
线段树是一种平衡二叉树，支持快速区间查找$O(lgn+k)$和更新$O(lgn)$。

## 线段树
[具体实现在这里](https://github.com/EIMadrigal/Recap/blob/main/Templates/Advanced/segment_tree.cpp)
线段树核心思想是叶子结点负责保存原始信息，非叶结点负责其孩子表示范围的union，可以是求和、最值等：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200115103411544.png)
对于每个结点，需要存储起始点、终止点、值、左右指针：

建树可以通过递归方式进行：

对于**更新**操作，只要找到叶子结点，一路向上更新至根结点，复杂度$O(lgn)$：

对于**查询**操作，查询范围有三种情况：

 1. 范围正好和根结点负责的范围一致，直接返回；
 2. 范围由某个下层结点负责，找到该结点返回其值；
 3. 范围由两个下层结点组合负责，返回两个结点的sum。

查询最好情况复杂度$O(1)$，最坏情况$O(lgn+k)$，$k$是某层结点的数目：

```cpp
class SegmentTree {
public:
    struct TreeNode {
        TreeNode(int l, int r, int val) : start(l), end(r), val(val), left(nullptr), right(nullptr) {}
        ~TreeNode() {
            if (left) {
                delete left;
                left = nullptr;
            }
            if (right) {
                delete right;
                right = nullptr;
            }
        }
        int start, end, val;  // val can be sum, min, max...
        TreeNode* left, *right;
    };
    
    TreeNode* buildTree(vector<int>& nums, int l, int r) {
        if (l == r) {
            return new TreeNode(l, r, nums[l]);
        }
        int m = l + (r - l) / 2;
        TreeNode* lef = buildTree(nums, l, m);
        TreeNode* rig = buildTree(nums, m + 1, r);
        TreeNode* root = new TreeNode(l, r, lef->val + rig->val);
        root->left = lef, root->right = rig;
        return root;
    }

    void update(TreeNode* root, int i, int newVal) {
        if (root->start == i && root->end == i) {
            root->val = newVal;
            return;
        }
        int m = root->start + (root->end - root->start) / 2;
        if (i <= m) {
            update(root->left, i, newVal);
        } else {
            update(root->right, i, newVal);
        }
        root->val = root->left->val + root->right->val;
    }

    int query(TreeNode* root, int l, int r) {
        if (l == root->start && r == root->end) {
            return root->val;
        }
        int m = root->start + (root->end - root->start) / 2;
        if (r <= m) {  // 查询范围完全落在左子树
            return query(root->left, l, r);
        } else if (l > m) {  // 查询范围完全落在右子树
            return query(root->right, l, r);
        } else {
            return query(root->left, l, m) + query(root->right, m + 1, r);
        }
        return 0;
    }
};
```

## Reference

 - [花花酱 Segment Tree 线段树 SP14](https://zxi.mytechroad.com/blog/sp/segment-tree-sp14/)
 - [一步一步理解线段树](https://www.cnblogs.com/TenosDoIt/p/3453089.html)
