---
title: 「Icoding题解」哈希表创建
category_bar: true
date: 2020-06-23 16:24:26
tags: ['algorithm', 'oj']
categories:
- 技术
- Algorithm
- OJ
---
# 哈希表创建

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/wp-content/uploads/2020/06/icoding.png)

[题目链接](https://icoding.run/ide#/question/130)

哈希表（Hash Table，也叫散列表），是根据键（Key）而直接访问在内存存储位置的数据结构。也就是说，它通过计算一个关于键值的函数，将所需查询的数据映射到表中一个位置来访问记录，这加快了查找速度。这个映射函数称做哈希函数，存放记录的数组称做哈希表。哈希表相关定义如下：

```
typedef enum{
    HASH_OK,
    HASH_ERROR,
    HASH_ADDED,
    HASH_REPLACED_VALUE,
    HASH_ALREADY_ADDED,
    HASH_DELETED,
    HASH_NOT_FOUND,
} HASH_RESULT;

typedef struct __HashEntry HashEntry;
struct __HashEntry{
    union{
        char  *str_value;
        double dbl_value;
        int       int_value;
    } key;
    union{
        char  *str_value;
        double dbl_value;
        int       int_value;
        long   long_value;
        void  *ptr_value;
    } value;
    HashEntry *next;
};

struct __HashTable{
    HashEntry **bucket;        
    int size;
    HASH_RESULT last_error;
};
typedef struct __HashTable HashTable;

// 创建大小为hash_size的哈希表，创建成功后返回HashTable类型的指针，否则返回NULL。
HashTable *create_hash(int hash_size);
```

## 哈希表相关说明：

1. HASH_RESULT 类型为相关函数的返回类型
2. HashEntry 为哈希表所保存元素（即键值对 ）类型
3. HashTable 为哈希表，其中 bucket 指向大小为size的、元素类型为 HashEntry*的指针数组
4. 希表采用链地址法处理冲突

## 请实现 create_hash 函数，创建指定大小的哈希表。

**程序流程图(flow代码）**

```
step1=>start: 开始
step2=>operation: 分配哈希表（容器）空间
step3=>operation: 分配bucket（桶）空间
cond1=>condition: 是否成果分配bucket空间
step4=>operation: 释放哈希表空间
step5=>operation: return NULL
step6=>operation: 初始化哈希表
step7=>operation: return H
step8=>end: 结束

step1->step2->step3->cond1
cond1(yes)->step6
cond1(no)->step4->step5
step6->step7->step8
```

![img](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/icoding_create_hash/20200622222331.png)

### 答案代码

```
#include "hash.h"
#include 
#include 
#include 

HashTable* create_hash(int size)
{
    HashTable* H = (HashTable*)malloc(sizeof(HashTable));
    H->bucket = (HashEntry**)malloc(sizeof(HashEntry**) * size);
    if (!H->bucket) {
        free(H);
        return NULL;
    }
    memset(H, 0, sizeof(HashTable));
    H->size = size;
    return H;
}
```

# 哈希表创建

[题目链接](https://icoding.run/ide#/question/130)

哈希表（Hash Table，也叫散列表），是根据键（Key）而直接访问在内存存储位置的数据结构。也就是说，它通过计算一个关于键值的函数，将所需查询的数据映射到表中一个位置来访问记录，这加快了查找速度。这个映射函数称做哈希函数，存放记录的数组称做哈希表。哈希表相关定义如下：

```
typedef enum{
    HASH_OK,
    HASH_ERROR,
    HASH_ADDED,
    HASH_REPLACED_VALUE,
    HASH_ALREADY_ADDED,
    HASH_DELETED,
    HASH_NOT_FOUND,
} HASH_RESULT;

typedef struct __HashEntry HashEntry;
struct __HashEntry{
    union{
        char  *str_value;
        double dbl_value;
        int       int_value;
    } key;
    union{
        char  *str_value;
        double dbl_value;
        int       int_value;
        long   long_value;
        void  *ptr_value;
    } value;
    HashEntry *next;
};

struct __HashTable{
    HashEntry **bucket;        
    int size;
    HASH_RESULT last_error;
};
typedef struct __HashTable HashTable;

// 创建大小为hash_size的哈希表，创建成功后返回HashTable类型的指针，否则返回NULL。
HashTable *create_hash(int hash_size);
```

## 哈希表相关说明：

1. HASH_RESULT 类型为相关函数的返回类型
2. HashEntry 为哈希表所保存元素（即键值对 ）类型
3. HashTable 为哈希表，其中 bucket 指向大小为size的、元素类型为 HashEntry*的指针数组
4. 希表采用链地址法处理冲突

## 请实现 create_hash 函数，创建指定大小的哈希表。

**程序流程图(flow代码）**

```
step1=>start: 开始
step2=>operation: 分配哈希表（容器）空间
step3=>operation: 分配bucket（桶）空间
cond1=>condition: 是否成果分配bucket空间
step4=>operation: 释放哈希表空间
step5=>operation: return NULL
step6=>operation: 初始化哈希表
step7=>operation: return H
step8=>end: 结束

step1->step2->step3->cond1
cond1(yes)->step6
cond1(no)->step4->step5
step6->step7->step8
```

![img](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Picture/wp/article/202006/icoding_create_hash/20200623164111.png)

### 答案代码

```
#include "hash.h"
#include "stdio.h"
#include "stdlib.h"
#include "string.h"

HashTable* create_hash(int size)
{
    HashTable* H = (HashTable*)malloc(sizeof(HashTable));
    H->bucket = (HashEntry**)malloc(sizeof(HashEntry**) * size);
    if (!H->bucket) {
        free(H);
        return NULL;
    }
    memset(H, 0, sizeof(HashTable));
    H->size = size;
    return H;
}
```