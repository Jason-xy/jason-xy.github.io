---
title: 「LeetCode」——136.只出现一次的数字
category_bar: true
date: 2020-07-02 17:45:23
tags: ['algorithm', 'oj', 'leetcode']
categories:
- 技术
- Algorithm
- OJ
---

## 题面

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

## 题解

注意这里的条件：

**算法应该具有线性时间复杂度：**时间复杂度O(n)

**不使用额外空间：**空间复杂度O(1)

目前我能想到的就是利用异或运算的运算性质。

```
异或运算性质：
1、交换律
2、结合律
3、对于任何数x，都有x^x=0，x^0=x
4、自反性 A XOR B XOR B = A xor 0 = A
```

那么这里只需要将所有数挨个异或，相同的数异或为0，0与不重复的数异或就是答案。

```c
int singleNumber(int* nums, int numsSize){
    if(nums==NULL||numsSize==0){
        return 0;
    }
    int num=0;
    for (int i=0; i<numsSize; i++) {
        num^=nums[i];
    }
    return num;
}
```

