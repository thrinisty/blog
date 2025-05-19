---
title: LeetCode刷题（子集，汉明距离，反转二叉树）
published: 2025-05-19
updated: 2025-05-19
description: '子集（二进制实现），汉明距离（位运算），反转二叉树（递归）'
image: ''
tags: [LeetCode]
category: 'LeetCode'
draft: false 
---

## 子集

### 题目描述

给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。

解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

**示例 1：**

```
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**示例 2：**

```
输入：nums = [0]
输出：[[],[0]]
```



### 题解

:::tip

1. 根据返回类型判断返回值为存放List集合的大List集合
2. 存放进入list中的数据对应其mask的二进制表示形式
3. 在内部循环中逐个判断是否将对应元素存入小List集合

:::

```java
class Solution {
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> list = new ArrayList<>();

    public List<List<Integer>> subsets(int[] nums) {
        result.clear();
        int n = nums.length;
        for (int mask = 0; mask < (1 << n); mask++) {
            list.clear();
            for (int i = 0; i < n; i++) {
                if((mask & (1 << i)) != 0) {
                    list.add(nums[i]);
                }
            }
            result.add(new ArrayList<Integer>(list));
        }
        return result;
    }
}
```



## 汉明距离

### 题目描述

两个整数之间的 [汉明距离](https://baike.baidu.com/item/汉明距离) 指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 `x` 和 `y`，计算并返回它们之间的汉明距离。

**示例 1：**

```
输入：x = 1, y = 4
输出：2
解释：
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
上面的箭头指出了对应二进制位不同的位置。
```

**示例 2：**

```
输入：x = 3, y = 1
输出：1
```



### 题解

:::tip

​	两个数的二进制不同的位通过取按位异或，再通过bitCount记录1个数即可

:::

使用Integer类静态方法

```java
class Solution {
    public int hammingDistance(int x, int y) {
        return Integer.bitCount(x ^ y);
    }
}
```

自己实现计算1个数

```java
class Solution {
    public int hammingDistance(int x, int y) {
        int target = x ^ y;
        int result = 0;
        while(target != 0) {
            result += (target & 1);
            target = target >> 1;
        }
        return result;
    }
}
```



## 反转二叉树

### 题目描述

给你一棵二叉树的根节点 `root` ，翻转这棵二叉树，并返回其根节点。

 

**示例 1：**

![157](../images/157.jpg)

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

**示例 2：**

![158](../images/158.jpg)

```
输入：root = [2,1,3]
输出：[2,3,1]
```

**示例 3：**

```
输入：root = []
输出：[]
```



### 题解

```java
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        TreeNode left = invertTree(root.left);
        TreeNode right = invertTree(root.right);
        root.left = right;
        root.right = left;
        return root;
    }
}
```

