---
title: CUDA on Arm - 存储单元
category_bar: true
date: 2021-08-24 15:42:20
tags: ['jetson','cuda','ubuntu', 'GPU', 'ARM']
categories:
- 技术
- Course
- CUDA
---

🎉感谢来自NVIDIA企业级开发者社区的何琨（Ken）老师提供的资料和细致耐心的讲解

本篇不涉及存储单元的加速优化，重点演示最基础的内存申请释放、数据拷贝传输

## 存储单元

（这块并没有完全学完，Constant memory和Texture memory存储介绍内容来源于搜索引擎）

![image-20210823233746897](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/23/image-20210823233746897.png)

### Register

寄存器是GPU最快的memory，寄存器变量是每个线程私有的，一旦thread执行结束，寄存器变量就会失效。寄存器是稀有资源。在Fermi上，每个thread限制最多拥有63个register，Kepler则是255个。让自己的kernel使用较少的register就能够允许更多的block驻留在SM中，也就增加了Occupancy，提升了性能。

寄存器不像共享内存、设备内存、常量内存和纹理内存显式声明，它们的声明不加任何限定符，就如普通变量一样，如` int a`。

寄存器主要承担Scalar variables（标量变量）和编译时已知的静态索引的数组。

### Local memory

有时候，如果Register不够用了，那么就会使用Local memory来代替这部分寄存器空间。

除此外，下面几种情况，编译器可能会把变量放置在Local memory：

- 编译期无法决定确切值的本地数组。
- 较大的结构体或者数组，也就是那些可能会消耗大量Register的变量。
- 任何超过寄存器限制的变量。

在Local memory中的变量本质上跟Global memory在同一块存储区。所以，Local memory有很高的atency和较低的bandwidth。在CC2.0以上，GPU针对Local memory会有L1（per-SM）和L2（per-device）两级cache。

### Shared memory

用\_\_shared\_\_修饰符修饰的变量存放在Shared memory。因为Shared memory是on-chip的，他相比Local Memory和Global memory来说，拥有高的多bandwidth和低很多的latency。他的使用和CPU的L1 cache非常类似，但是他是programmable的。

按惯例，像这类性能这么好的memory都是有限制的，Shared memory是以block为单位分配的。我们必须非常小心的使用Shared memory，否则会无意识的限制了active warp的数目。

不同于Register，Shared memory尽管在kernel里声明的，但是他的生命周期是伴随整个block，而不是单个thread。当该block执行完毕，他所拥有的资源就会被释放，重新分配给别的block。

Shared memory是thread交流的基本方式。同一个block中的thread通过Shared memory中的数据来相互合作。获取Shared memory的数据前必须先用__syncthreads()同步。

### Global memory

Global Memory是空间最大，latency最高，GPU最基础的memory。“global”指明了其生命周期。任意SM都可以在整个程序的生命期中获取其状态。global中的变量既可以是静态也可以是动态声明。可以使用\_\_device\_\_修饰符来限定其属性。Global memory的分配就是之前频繁使用的`cudaMalloc`，释放使用`cudaFree`。Global memory驻留在Device memory，可以通过32-byte、64-byte或者128-byte三种格式传输。这些memory transaction必须是对齐的，也就是说首地址必须是32、64或者128的倍数。优化memory transaction对于性能提升至关重要。当warp执行memory load/store时，需要的transaction数量依赖于下面两个因素：

1. Distribution of memory address across the thread of that warp 就是前文的连续
2. Alignment of memory address per transaction 对齐

一般来说，所需求的transaction越多，潜在的不必要数据传输就越多，从而导致throughput efficiency降低。

### Constant memory

Constant Memory驻留在Device Memory，并且使用专用的Constant cache（per-SM）。该Memory的声明应该以\_\_connstant\_\_修饰。Constant的范围是全局的，针对所有kernel，对于所有CC其大小都是64KB。在同一个编译单元，Constant对所有kernel可见。

当一个warp中所有thread都从同一个Memory地址读取数据时，Constant Memory表现最好。例如，计算公式中的系数。如果所有的thread从不同的地址读取数据，并且只读一次，那么Constant memory就不是很好的选择，因为一次读Constant memory操作会广播给所有thread知道。

### Texture memory

Texture Memory驻留在Device memory中，并且使用一个只读cache（per-SM）。Texture Memory实际上也是Global Memory在一块，但是他有自己专有的只读cache。这个cache在浮点运算很有用。Texture Memory是针对2D空间局部性的优化策略，所以thread要获取2D数据就可以使用Texture Memory来达到很高的性能。

## 内存的分配与回收

CPU内存

- malloc()
- memset()
- free()

GPU内存

- cudaMalloc()
- cudaMemset()
- cudaFree()

**当我们要为一个方阵`M[m * m]`申请GPU的存储单元时**

```c
cudaMalloc((void **) &d_m,sizeof(int) * m * m);
```

**d_m：**指向存储在Device端数据的地址的指针（双重指针）。

**sizeof(int) \* m \* m：**存储在Device端空间的大小。

**当我们要释放申请的GPU的存储单元时**

```c
cudaFree(d_m);
```

d_m：指向存储在Device端数据的地址的指针。

## 内存拷贝

**cudaMemcpy(void *dst, const void *src, size_t count, cudaMemcpyKind kind)**

- **dst:** 目的内存地址
- **src:** 源内存地址
- **count:** 以字节为单位要拷贝的数据大小
- **kind:** 数据传输方向

**cudaMemcpyKind**

- cudaMemcpyHostToDevice
- cudaMemcpyDeviceToHost
- cudaMemcpyDeviceToDevice
- cudaMemcpyHostToHost

**当我们要将准备好的数据从CPU内存传输到GPU的存储单元时**

```c
cudaMemcpy(d_m, h_m, sizeof(int) * m * m, cudaMemcpyHostToDevice);
```

**d_m：**传输的目的地，GPU存储单元

**h_m：**数据的源地址，CPU存储单元

**sizeof(int) \* m \* m：**数据传输的大小

**cudaMemcpyHostToDevice：**数据传输的方向，CPU -> GPU

**当我们要将准备好的数据从GPU存储单元传输到CPU的内存时**

```c
cudaMemcpy(h_c, d_c, sizeof(int) * m * k, cudaMemcpyDeviceToHost);
```

## 实验（矩阵乘法）

![image-20210824000328785](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/24/image-20210824000328785.png)

矩阵相乘计算样例：

![image-20210823235724074](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/23/image-20210823235724074.png)

### CPU矩阵乘法

使用CPU完成这一过程：

```c
void cpu_matrix_mult(int *h_m, int *h_n, int *h_result, int m, int n, int k){
	for (int i = 0; i < m; ++i){
		for (int j = 0; j < k; ++j){
			int tmp = 0.0;
			for (int h = 0; h < n; ++h){
				tmp += h_m[i * n + h] * h_n[h * k + j];
            }
			h_result[i * k + j] = tmp;
        }
	}
}


```

可见整个计算过程需要完成三重循环，总共需要计算`m * n * k`次`tmp += h_m[i * n + h] * h_n[h * k + j];`才能完成计算。下面采用CUDA的并行编程模型来尝试去掉部分循环进行加速。

### 二维网格和线程块

为了解决矩阵乘法的问题，需要先搞定二维状态下线程的索引计算（当然也可以转换为一维）。

线程的索引排序方式如下图：

![image-20210824000858814](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/24/image-20210824000858814.png)

Global linear memory index: `idx = iy * nx + ix`

**以44号线程为例**

```c
int index = (blockIdx.y * blockDim.y + threadIdx.y) * nx+ (blockIdx.x * blockDim.x + threadIdx.x);
```

```c
Index = (2 * 2 + 1) * 8 + (1 * 4 + 0) 
Index = 44
```

![image-20210824001345666](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/24/image-20210824001345666.png)

将整个grid对应到整个矩阵后，就如下图所示：

![image-20210824001447544](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/24/image-20210824001447544.png)

### GPU矩阵乘法

**grid中多线程计算Pd**

- 每个线程计算Pd的一个元素

**每个线程**

- 读入矩阵Md的一行，读入矩阵Nd的一列
- 为每对Md和Nd元素执行一次乘法和加法

![image-20210824002247739](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/24/image-20210824002247739.png)

### 完整代码

```c
#include <stdio.h>
#include <math.h>

#define BLOCK_SIZE 16

__global__ void gpu_matrix_mult(int *a,int *b, int *c, int m, int n, int k)
{ 
    int row = blockIdx.y * blockDim.y + threadIdx.y; 
    int col = blockIdx.x * blockDim.x + threadIdx.x;
    int sum = 0;
    if( col < k && row < m) 
    {
        for(int i = 0; i < n; i++) 
        {
            sum += a[row * n + i] * b[i * k + col];
        }
        c[row * k + col] = sum;
    }
} 

void cpu_matrix_mult(int *h_a, int *h_b, int *h_result, int m, int n, int k) {
    for (int i = 0; i < m; ++i) 
    {
        for (int j = 0; j < k; ++j) 
        {
            int tmp = 0.0;
            for (int h = 0; h < n; ++h) 
            {
                tmp += h_a[i * n + h] * h_b[h * k + j];
            }
            h_result[i * k + j] = tmp;
        }
    }
}

int main(int argc, char const *argv[])
{
    int m=1000;
    int n=2000;
    int k=3000;

    int *h_a, *h_b, *h_c, *h_cc;
    cudaMallocHost((void **) &h_a, sizeof(int)*m*n);
    cudaMallocHost((void **) &h_b, sizeof(int)*n*k);
    cudaMallocHost((void **) &h_c, sizeof(int)*m*k);
    cudaMallocHost((void **) &h_cc, sizeof(int)*m*k);

    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            h_a[i * n + j] = rand() % 1024;
        }
    }

    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < k; ++j) {
            h_b[i * k + j] = rand() % 1024;
        }
    }

    int *d_a, *d_b, *d_c;
    cudaMalloc((void **) &d_a, sizeof(int)*m*n);
    cudaMalloc((void **) &d_b, sizeof(int)*n*k);
    cudaMalloc((void **) &d_c, sizeof(int)*m*k);

    // copy matrix A and B from host to device memory
    cudaMemcpy(d_a, h_a, sizeof(int)*m*n, cudaMemcpyHostToDevice);
    cudaMemcpy(d_b, h_b, sizeof(int)*n*k, cudaMemcpyHostToDevice);

    unsigned int grid_rows = (m + BLOCK_SIZE - 1) / BLOCK_SIZE;
    unsigned int grid_cols = (k + BLOCK_SIZE - 1) / BLOCK_SIZE;
    dim3 dimGrid(grid_cols, grid_rows);
    dim3 dimBlock(BLOCK_SIZE, BLOCK_SIZE);
    gpu_matrix_mult<<<dimGrid, dimBlock>>>(d_a, d_b, d_c, m, n, k);    
    cudaMemcpy(h_c, d_c, sizeof(int)*m*k, cudaMemcpyDeviceToHost);

    cpu_matrix_mult(h_a, h_b, h_cc, m, n, k);

    int ok = 1;
    for (int i = 0; i < m; ++i)
    {
        for (int j = 0; j < k; ++j)
        {
            if(fabs(h_cc[i*k + j] - h_c[i*k + j])>(1.0e-10))
            {
                ok = 0;
            }
        }
    }

    if(ok)
    {
        printf("Pass!!!\n");
    }
    else
    {
        printf("Error!!!\n");
    }

    // free memory
    cudaFree(d_a);
    cudaFree(d_b);
    cudaFree(d_c);
    cudaFreeHost(h_a);
    cudaFreeHost(h_b);
    cudaFreeHost(h_c);
    return 0;
}
```

### 代码解析

#### 核函数

```c
__global__ void gpu_matrix_mult(int *a,int *b, int *c, int m, int n, int k)
{ 
    int row = blockIdx.y * blockDim.y + threadIdx.y; 
    int col = blockIdx.x * blockDim.x + threadIdx.x;
    int sum = 0;
    if( col < k && row < m) 
    {
        for(int i = 0; i < n; i++) 
        {
            sum += a[row * n + i] * b[i * k + col];
        }
        c[row * k + col] = sum;
    }
} 
```

先计算好元素在矩阵中的行和列，每一个线程处理了矩阵Md的一行和矩阵Nd的一列，分别相乘再相加。将计算好的最终值写入Pd矩阵的对应位置即可。

这里需要注意索引的计算方式，我们输入的矩阵实际上是一个一维的数组，那么需要将二维坐标线性化。

#### CPU计算函数

```c
void cpu_matrix_mult(int *h_a, int *h_b, int *h_result, int m, int n, int k) {
    for (int i = 0; i < m; ++i) 
    {
        for (int j = 0; j < k; ++j) 
        {
            int tmp = 0.0;
            for (int h = 0; h < n; ++h) 
            {
                tmp += h_a[i * n + h] * h_b[h * k + j];
            }
            h_result[i * k + j] = tmp;
        }
    }
}
```

将CPU计算结果作为标准来对GPU计算结果进行校验。

#### 主函数

```c
int *h_a, *h_b, *h_c, *h_cc;
cudaMallocHost((void **) &h_a, sizeof(int)*m*n);
cudaMallocHost((void **) &h_b, sizeof(int)*n*k);
cudaMallocHost((void **) &h_c, sizeof(int)*m*k);
cudaMallocHost((void **) &h_cc, sizeof(int)*m*k);
```

定义变量并在Host上分配数组空间。

```c
for (int i = 0; i < m; ++i) {
    for (int j = 0; j < n; ++j) {
        h_a[i * n + j] = rand() % 1024;
    }
}

for (int i = 0; i < n; ++i) {
    for (int j = 0; j < k; ++j) {
        h_b[i * k + j] = rand() % 1024;
    }
}
```

在Host端初始化数组。

```c
int *d_a, *d_b, *d_c;
cudaMalloc((void **) &d_a, sizeof(int)*m*n);
cudaMalloc((void **) &d_b, sizeof(int)*n*k);
cudaMalloc((void **) &d_c, sizeof(int)*m*k);
```

定义变量，并调用`cudaMalloc`在GPU端为变量分配内存空间。

```c
cudaMemcpy(d_a, h_a, sizeof(int)*m*n, cudaMemcpyHostToDevice);
cudaMemcpy(d_b, h_b, sizeof(int)*n*k, cudaMemcpyHostToDevice);
```

将在CPU端初始化好的数组拷贝到GPU，便于在GPU并行处理数据。

```c
dim3 dimGrid(grid_cols, grid_rows);
dim3 dimBlock(BLOCK_SIZE, BLOCK_SIZE);
gpu_matrix_mult<<<dimGrid, dimBlock>>>(d_a, d_b, d_c, m, n, k); 
cudaMemcpy(h_c, d_c, sizeof(int)*m*k, cudaMemcpyDeviceToHost);
```

完成执行设置并调用核函数，在GPU上开始程序的并行部分。

计算完成后结果存储在`d_c`所指向的GPU内存中，需要调用`cudaMemcpy`将数据从Device拷贝到Host中。

```c
cpu_matrix_mult(h_a, h_b, h_cc, m, n, k);

int ok = 1;
for (int i = 0; i < m; ++i)
{
    for (int j = 0; j < k; ++j)
    {
        if(fabs(h_cc[i*k + j] - h_c[i*k + j])>(1.0e-10))
        {
            ok = 0;
        }
    }
}

if(ok)
{
    printf("Pass!!!\n");
}
else
{
    printf("Error!!!\n");
}
```

在CPU中完成标准答案的计算并与GPU计算结果进行对比校验。

```c
cudaFree(d_a);
cudaFree(d_b);
cudaFree(d_c);
cudaFreeHost(h_a);
cudaFreeHost(h_b);
cudaFreeHost(h_c);
```

调用`cudaFree`相关函数释放刚刚在Host和Device中分配的内存，随即程序结束。