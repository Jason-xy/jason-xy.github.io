---
title: 「LeetCode」——217.存在重复元素
category_bar: true
date: 2020-07-02 17:34:05
tags: ['algorithm', 'oj', 'leetcode']
categories:
- 技术
- Algorithm
- OJ
---

## 题面

给定一个整数数组，判断是否存在重复元素。

如果任意一值在数组中出现至少两次，函数返回 `true` 。如果数组中每个元素都不相同，则返回 `false` 。

**示例 1:**

```
输入: [1,2,3,1]
输出: true
```

**示例 2:**

```
输入: [1,2,3,4]
输出: false
```

**示例 3:**

```
输入: [1,1,1,3,3,4,3,2,4,2]
输出: true
```

## 题解

### 暴力解法

```c
bool containsDuplicate(int* nums, int numsSize) {
    for(int i = 0; i < numsSize; i++){
        for(int j = i+1; j <numsSize;j++){
            if(nums[j] == nums[i])
            return true;
        }
    }
    return false;
}
```

这暴力一波，时间复杂度O(n^2)不出所料超时了。

### 哈希映射

其实就是一个比较常规的哈希映射，只是看用什么方法来优化这个哈希映射。

**不考虑冲突（无法通过全部测试）**

```c
bool containsDuplicate(int* nums, int numsSize){
    if (numsSize <= 1)return false;
    if (nums[0] == nums[1])return true;
    int hashtable[50000]={0},hash=0;
    for(int i = 0; i < numsSize; i++){
        hash=(nums[i]+120000006)%49999;
        if(hashtable[hash]>0)
        return true;
        hashtable[hash]+=1;
    }
    return false;
}
```

可以看出来部分数据是存在冲突的，所以考虑一下处理冲突的方法。

**哈希链表处理冲突 224ms 10.5mb**

```c
typedef struct __HashNode HashNode;
struct __HashNode {
    int num;
    HashNode* next;
};
bool hashadd(HashNode** Table, int num) {
    HashNode* temp = (HashNode*)malloc(sizeof(HashNode));
    int hash = (num + 120000006) % 97;
    HashNode* p = Table[hash];
    temp->num = num;
    temp->next = NULL;
    for (; p; p = p->next) {
        if (num == p->num)
            return true;
    }
    if (Table[hash]->next)
        temp->next = Table[hash]->next;
    Table[hash]->next = temp;
    return false;
}
bool containsDuplicate(int* nums, int numsSize) {
    if (numsSize <= 1)return false;
    if (nums[0] == nums[1])return true;
    HashNode** table = (HashNode**)malloc(sizeof(HashNode*) * 100);
    for (int i = 0; i < 100; i++) {
        table[i] = (HashNode*)malloc(sizeof(HashNode));
        table[i]->num = -1;
        table[i]->next = NULL;
    }
    for (int i = 0; i < numsSize; i++) {
        if (hashadd(table, nums[i]))
            return true;
    }
    return false;
}
```

哈希链表操作比较繁琐，导致时间上依然显得有点慢，不妨把链表变成二维数组来试试。

**二维数组哈希表处理冲突**

先贴一下测试结果：

```
//暴力调参
//hashtable[110][110] 52ms 8.1mb
//hashtable[1000][50] 28ms 8.1mb
//hashtable[10000][3] 20ms 7.7mb
//hashtable[5000][3]  16ms 8.0mb
```

可见数组的参数选择对时间的影响还是很大的。

```c
bool containsDuplicate(int* nums, int numsSize){
    if (numsSize <= 1)return false;
    if (nums[0] == nums[1])return true;
    int hashtable[110][110]={0};
    int hash=0,flag=0;
    for(int i=0; i < numsSize; i++){
        if(nums[i]!=0){
            hash=(nums[i]+120000006)%97;
            for(int j=0; j < 110; j++){
                if(hashtable[hash][j]==nums[i]){
                    return true;
                }
                else if(hashtable[hash][j]==0){
                    hashtable[hash][j]=nums[i];
                    break;
                }
            }
        }
        else{
            if(flag==1)
            return true;
            flag=1;
        }
    }
    return false;
}
```

