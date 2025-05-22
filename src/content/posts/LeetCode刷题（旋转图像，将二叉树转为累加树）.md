---
title: LeetCode刷题（旋转图像，将二叉树转为累加树）
published: 2025-05-22
updated: 2025-05-22
description: '旋转图像，将二叉树转为累加树'
image: ''
tags: [LeetCode]
category: 'LeetCode'
draft: false 
---

# 旋转图像

## 题目描述

给定一个 *n* × *n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在**[ 原地](https://baike.baidu.com/item/原地算法)** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

```
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

## 题解

使用新的矩阵辅助完成

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        int[][] newArr = new int[n][n];
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < n; j++) {
                newArr[j][n - 1 - i] = matrix[i][j];
            }
        }

         for(int i = 0; i < n; i++) {
            for(int j = 0; j < n; j++) {
                matrix[i][j] = newArr[i][j];
            }
        }
    }
}
```

使用原矩阵完成（水平反转 + 对角线反转）

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        int temp;
        for(int i = 0, k = n - 1; i < k; i++, k--) {
            for(int j = 0; j < n; j++) {
                temp = matrix[i][j];
                matrix[i][j] = matrix[k][j];
                matrix[k][j] = temp;
            }
        }
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
    }
}
```



# 二叉搜索树转为累加树

## 题目描述

给出二叉 **搜索** 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 `node` 的新值等于原树中大于或等于 `node.val` 的值之和。

提醒一下，二叉搜索树满足下列约束条件：

- 节点的左子树仅包含键 **小于** 节点键的节点。
- 节点的右子树仅包含键 **大于** 节点键的节点。
- 左右子树也必须是二叉搜索树。

![161](../images/161.png)

```
输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

## 题解

反着中序遍历，递归完成，在其中运用sum记录累加值，进行赋值操作即可

```java
class Solution {
    int sum = 0;
    public TreeNode convertBST(TreeNode root) {
        if(root != null) {
            convertBST(root.right);
            sum += root.val;
            root.val = sum;
            convertBST(root.left);
        }
        return root;
    }
}
```

