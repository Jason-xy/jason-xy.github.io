---
title: 「Icoding题解」哈希表添加 
category_bar: true
date: 2020-06-23 17:17:36
tags: ['algorithm', 'oj']
categories:
- 技术
- Algorithm
- OJ
---
# 哈希表添加

![](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/wp-content/uploads/2020/06/icoding.png)

[题目地址](https://icoding.run/ide#/question/131)

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

// 向哈希表中添加元素，其中键类型为char*， 元素类型为int。
HASH_RESULT hash_add_int(HashTable * table, const char * key, int value);
```

## 哈希表相关说明：

1. HASH_RESULT 类型为相关函数的返回类型
2. HashEntry 为哈希表所保存元素（即键值对 《key, value》）类型
3. HashTable 为哈希表，其中 bucket 指向大小为size的、元素类型为 HashEntry*的指针数组
4. 哈希表采用链地址法处理冲突

请实现 hash_add_int 函数，向哈希表中添加元素，其中键类型为char*， 元素类型为int。在添加过程中，如果要添加的键值key已在哈希表中，且对应的值value也已存在，则函数返回 HASH_ALREADY_ADDED；如果要添加的键值key已在哈希表中，但对应的值value不同，则函数将value值更新到哈希表中，之后返回 HASH_REPLACED_VALUE；如果要添加的键值key不在哈希表中，则函数创建 HashEntry 类型，并将其加入到哈希表中，且函数返回 HASH_ADDED。本题所用的哈希函数如下：

```
long hash_string(const char *str)
{
    long hash = 5381;
    int c;

    while (c = *str++)
        hash = ((hash << 5) + hash) + c; /* hash * 33 + c */
    if(hash < 0)
        hash *= -1;
    return hash;
}
```

### 答案代码及注释

```
#include "hash.h"
#include "stdlib.h"
#include <stdio.h>
#include <string.h>

HASH_RESULT hash_add_int(HashTable* table, const char* key, int value)
{
    long hash = hash_string(key) % table->size;//计算哈希地址
    HashEntry* p = table->bucket[hash];
    for (p = table->bucket[hash]; p && strcmp(p->key.str_value, key);)//对于char*的变量一定要用strcmp来进行比较
        p = p->next;//查找是否存在键值相同的元素
    if (p) {//若存在相同键值的元素
        if (value == p->value.int_value)
            return HASH_ALREADY_ADDED;
        else {
            p->value.int_value = value;
            return HASH_REPLACED_VALUE;
        }
    }
    p = (HashEntry*)malloc(sizeof(HashEntry));//不存在则添加节点
    if (!p)//是否成功分配节点空间
        return HASH_ERROR;
    else {
        p->key.str_value = (char*)malloc(100 * sizeof(char));
        if (!p->key.str_value)//是否成功分配键值空间
            return HASH_ERROR;
    }//这里一定要判定是否成功！！！
    strcpy(p->key.str_value, key);
    p->value.int_value = value;
    table->bucket[hash] = p;//按道理这里应该把之前的节点接在p的next后面，但是代码过不了OJ，没想到这样直接不要后面的节点居然对了，没搞明白。
    return HASH_ADDED;
}
```