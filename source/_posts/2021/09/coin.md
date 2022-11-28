---
title: 概率生成问题
category_bar: true
date: 2021-09-05 14:42:03
tags: ['algorithm', 'possibilities']
categories:
- 技术
- Algorithm
- Basic
---

作者：imageslr
链接：https://leetcode-cn.com/problems/implement-rand10-using-rand7/solution/cong-pao-ying-bi-kai-shi-xun-xu-jian-jin-ba-zhe-da/
来源：力扣（LeetCode）

## 引言

概率生成问题是一种比较常见的面试题，常见题型举例：

- 有一枚不均匀的硬币，要求产生均匀的概率分布
- 有一枚均匀的硬币，要求产生不均匀的概率分布，如 0.25 和 0.75
- 利用 Rand7() 实现 Rand10()

本文将依次讲解这三个问题，然后总结此类问题的通用方法。

## 不均匀硬币，产生等概率

现有一枚不均匀的硬币 `coin()`，能够返回 0、1 两个值，其概率分别为 0.6、0.4。要求使用这枚硬币，产生均匀的概率分布。即编写一个函数 `coin_new()` 使得它返回 0、1 的概率均为 0.5。

```c
// 不均匀硬币，返回 0、1 的概率分别为 0.6、0.4
int coin() {
    return (rand() % 10) > 5;
}
```

统计抛两次硬币的结果的概率分布：

| 结果 | 0             | 1            |
| ---- | ------------- | ------------ |
| 0    | 0.6\*0.6=0.36 | 0.6*0.4=0.24 |
| 1    | 0.4*0.6=0.24  | 0.4*0.4=0.16 |


不难发现，连续抛两枚硬币得到 `0 1` 和 `1 0` 的概率分布是相同的。因此这道题的解法就是连续抛两次硬币，如果得到 `0 1`，返回 0；如果得到 `1 0`，返回 1；如果两次结果相同，则重新抛。

以此类推，无论这枚不均匀硬币的概率是多少，都可以用这种方法得到等概率的结果。

```c
int coin_new() {
    while (true) {
        int a = coin();
        if (coin() != a) return a;
    }
}
```

完整测试代码：

```c++
#include <iostream>
#include <map>
using namespace std;

// 不均匀硬币
int coin() {
    return (rand() % 10) > 4;
}

int coin_new() {
    while (true) {
        int a = coin();
        if (coin() != a) return a;
    }
}

// 测试
int main() {
    int N = 1000000;
    float count[2] = {0, 0};
    for (int i = 0; i < N; i++)
        count[coin_new()] ++;
    
    cout << "0: " << count[0]/N << endl;
    cout << "1: " << count[1]/N << endl;
}
```

输出：

```bash
0: 0.50037
1: 0.49963
```

## 均匀硬币，产生不等概率

现有一枚均匀的硬币 `coin()`，能够返回 0、1 两个值，其概率均为 0.5。要求编写一个函数 `coin_new()`，使得它返回指定的 0、1 概率分布。

```c
// 均匀硬币
int coin() {
    return rand() % 2;
}
```

### **P(0) = 1/4，P(1) = 3/4**

对于均匀硬币而言，连续抛两次，得到 0 0、0 1、1 0、1 1 的概率均为 1/4。显然，只需要连续抛两次硬币，如果得到 0 0，返回 0，其他情况返回 1。

```c
int coin_new() {
    return coin() || coin();
}
```

测试输出：

```bash
0: 0.249249
1: 0.750751
```

### P(0) = 1/3，P(1) = 2/3

连续抛两次硬币。如果得到 `1 1`，返回 0；如果得到 `1 0` 或 `0 1`，返回 1；如果得到 `0 0`，继续抛硬币。

```c
int coin_new() {
    while (true) {
        int a = coin(), b = coin();
        if (a && b) return 0;
        if (a || b) return 1;
    }
}
```

测试输出：

```bash
0: 0.333663
1: 0.666337
```

### P(0) = 0.3，P(1) = 0.7

每抛一次硬币，会得到二进制数的一位
连续抛 4 次硬币，可以等概率生成 `[0, 15]` 的每个数，记为 xx
去掉 `[10, 15]`，剩下 `[0, 9]` 的每个数依然是等概率的
如果 x∈[0,2]，返回 0；x∈[4,9]，返回 1；x≥10，重复上述过程

测试输出：

```bash
0: 0.300324
1: 0.699676
```

## 总结

![image-20210905141534174](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/09/05/image-20210905141534174.png)

