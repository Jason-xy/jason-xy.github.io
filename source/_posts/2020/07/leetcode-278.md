---
title: 「LeetCode」——278. 第一个错误的版本（二分查找）
category_bar: true
date: 2020-07-09 10:15:05
tags: ['algorithm', 'oj', 'leetcode']
categories:
- 技术
- Algorithm
- OJ
---

## 详述二分查找

先看看Knuth（KMP算法发明者）怎么说：

```
Although the basic idea of binary search is comparatively straightforward, the details can be surprisingly tricky...
```

总之就是二分查找的细节处理往往决定了程序是否能够正确运行。

比如，比较大小有没有等于号，mid该不该加1，往往这些问题就在查找的边界出现......

### 二分查找基本框架

```c
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;
    while(...) {
        int mid = left + (right - left) / 2;//防止溢出
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```

其中...标记的部分，就是可能出现细节问题的地方，当你见到一个二分查找的代码时，首先注意这几个地方。后文用实例分析这些地方能有什么样的变化。

### 寻找一个数

这个场景是最简单的，可能也是大家最熟悉的，即搜索一个数，如果存在，返回其索引，否则返回 -1。

```c++
int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意（1）

    while(left <= right) { // 注意（2）
        int mid = left + (right - left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意（3）
        else if (nums[mid] > target)
            right = mid - 1; // 注意（4）
        }
    return -1;
}
```

**（1）**数组索引最大是length-1；

**（2）**这里的while循环条件中为<=,如果为<相当于查找区间为[left,right)，而无法找到最右边的值；如果为=，那么查找区间就是[left,right]，这一点可以用在1，2，3中查找3的例子模拟验证一下。 

**（3）、（4）**如果mid不满足条件，那么边界应该移动到mid的下一个数

再来探讨一下**停止搜索的条件**：

1. 什么时候应该停止搜索呢？当然，找到了目标值的时候可以终止：

   ```c
   if(nums[mid] == target)
           return mid; 
   ```

2. 但如果没找到，就需要 while 循环终止，然后返回 -1。

   在while(left <= right)的约束下下，终止条件是 left == right + 1。

   在while(left < right)的约束下下，终止条件是 left == right。

   那么说明在不加等号的时候right所指的元素是无法参与比较的，那么就刚好漏掉了一个。

   当然，如果你非要用 while(left < right) 也可以，我们已经知道了出错的原因，就打个补丁好了：

   ```c
   //...
   while(left < right) {
       // ...
   }
   return nums[left] == target ? left : -1;
   ```

   只需要在最后比较一下漏掉的元素即可。

### **寻找左侧边界的二分搜索**

直接看代码，其中的标记是需要注意的细节：

```c
int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0;
    int right = nums.length; // 注意（1）

    while (left < right) { // 注意（2）
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            right = mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid; // 注意（3）
        }
    }
    // target 比所有数都大
	if (left == nums.length) return -1;
	// 类似之前算法的处理方式
	return nums[left] == target ? left : -1;
    //返回left或者right都一样，因为right=left为终止条件
}
```

先了解一下返回值的含义：

例：target=2；nums=[1,2,2,4,5]。对于这个数组，算法会返回 1。这个 1 的含义可以这样解读：nums 中小于 2 的元素有 1 个。

对于有序数组 nums = [2,3,5,7], target = 1，算法会返回 0，含义是：nums 中小于 1 的元素有 0 个。如果 target = 8，算法会返回 4，含义是：nums 中小于 8 的元素有 4 个，但是数组中没有这个数应该返回-1。

综上可以看出，函数的返回值（即 left 变量的值）取值区间是闭区间 [0, nums.length]，所以我们简单添加两行代码就能在正确的时候 return -1：

```c
while (left < right) {
    //...
}
// target 比所有数都大
if (left == nums.length) return -1;
// 类似之前算法的处理方式
return nums[left] == target ? left : -1;
```

**（1）**这里的初始化条件一定要满足left的取值区间 [0, nums.length]，并且当left=nums.length时返回-1，否则很可能会漏掉-1的情况。

**（2）** 这里的终止条件是left = right，相当于在搜索区间中，左边是闭区间，右边是开区间。

**（3）**while条件决定了搜索区间，那么这里要满足左闭右开，并且不漏掉元素的话就需要让right=mid。

该算法能找到左边界的主要原因还是对 nums[mid] == target 这种情况的处理：

```c
if (nums[mid] == target)
	right = mid;
```

可见，找到 target 时不要立即返回，而是缩小「搜索区间」的上界 right，在区间 [left, mid) 中继续搜索，即不断向左收缩，达到锁定左侧边界的目的。

### 寻找右侧边界的二分查找

寻找右侧边界和寻找左侧边界的代码差不多，只有两处不同，已标注：

```c
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int left = 0, right = nums.length;

    while (left < right) {
        int mid = (left + right) / 2;
        if (nums[mid] == target) {
            left = mid + 1; // 注意（1）
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid;
        }
    }
    return left - 1; // 注意（2）
```

*1.* 为什么这个算法能够找到右侧边界？

答：类似地，关键点还是这里：

```c
if (nums[mid] == target) {
	left = mid + 1;
```

当 nums[mid] == target 时，不要立即返回，而是增大「搜索区间」的下界 left，使得区间不断向右收缩，达到锁定右侧边界的目的。

*2.* 为什么最后返回 left - 1 而不像左侧边界的函数，返回 left？而且我觉得这里既然是搜索右侧边界，应该返回 right 才对。

答：首先，while 循环的终止条件是 left == right，所以 left 和 right 是一样的，你非要体现右侧的特点，返回 right - 1 好了。

至于为什么要减一，这是搜索右侧边界的一个特殊点，关键在这个条件判断：

```c
if (nums[mid] == target) {
	left = mid + 1;
	// 这样想: mid = left - 1
```

因为我们对 left 的更新必须是 left = mid + 1，就是说 while 循环结束时，nums[left] **一定不等于** target 了，而 nums[left - 1]**可能是**target。

*3.* 为什么没有返回 -1 的操作？如果 nums 中不存在 target 这个值，怎么办？

答：类似之前的左侧边界搜索，因为 while 的终止条件是 left == right，就是说 left 的取值范围是 [0, nums.length]，所以可以添加两行代码，正确地返回 -1：

```c
while (left < right) {
    // ...
}
if (left == 0) return -1;
return nums[left-1] == target ? (left-1) : -1;
```

### 概括总结

第一个，最基本的二分查找算法：

```c
因为我们初始化 right = nums.length - 1
所以决定了我们的「搜索区间」是 [left, right]
所以决定了 while (left <= right)
同时也决定了 left = mid+1 和 right = mid-1

因为我们只需找到一个 target 的索引即可
所以当 nums[mid] == target 时可以立即返回
```

第二个，寻找左侧边界的二分查找：

```c
因为我们初始化 right = nums.length
所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid+1 和 right = mid

因为我们需找到 target 的最左侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧右侧边界以锁定左侧边界
```

第三个，寻找右侧边界的二分查找：

```c
因为我们初始化 right = nums.length
所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid+1 和 right = mid

因为我们需找到 target 的最右侧索引
所以当 nums[mid] == target 时不要立即返回
而要收紧左侧边界以锁定右侧边界

又因为收紧左侧边界时必须 left = mid + 1
所以最后无论返回 left 还是 right，必须减一
```

