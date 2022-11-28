---
title: CUDA on Arm - 并行计算基础
category_bar: true
date: 2021-08-22 14:29:02
tags: ['jetson','cuda','ubuntu', 'GPU', 'ARM']
categories:
- 技术
- Course
- CUDA
---

🎉感谢来自NVIDIA企业级开发者社区的何琨（Ken）老师提供的资料和细致耐心的讲解

## 异构计算

**术语：**

Host： CPU和内存（host memory）

Device：GPU和显存（device memory）

![image-20210816140636318](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816140636318.png)

## CUDA安装

### 适用设备

所有包含NVIDIA GPU的服务器，工作站，个人电脑，嵌入式设备等电子设备。

### 软件安装

**Windows：**https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html

Windows安装最简单，只需运行一个exe即可。

**Linux：**https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html

具体步骤也可以参考我的这篇博文，但由于时间推移注意替换软件版本号。

[Ubuntu20.04下安装Cuda11.0+Nvidia-440+Cudnn7.1.4](https://jason-xy.cn/2020/07/ubuntu_nvidia/)

**Jetson：**https://developer.nvidia.com/embedded/jetpack

采用NVIDIA SDK Manager安装。

**GPU状态查询：**

PC、工作站：`nvidia-smi`

Jetson：`jtop`

GPU信息查询：

```bash
cd /usr/local/cuda/samples/1_Utilities/deviceQuery
./deviceQuery
```

![image-20210816142314321](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816142314321.png)

## CUDA程序的编写模型

![image-20210822135353790](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822135353790.png)

### 把输入数据从CPU内存复制到GPU显存

![image-20210822135532199](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822135532199.png)

### 在执行芯片上缓存数据，加载GPU程序并执行

![image-20210822135626626](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822135626626.png)

### 将计算结果从GPU显存中复制到CPU内存中

![image-20210822135754435](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822135754435.png)

### 关键字

![image-20210816143012665](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816143012665.png)

### CUDA程序案例

**核函数**

```c
__global__ void gpu_kernel(int input_M[N * N], int output_M[N * N])
{
    int idx = blockIdx.x * blockDim.x + threadIdx.x;
    if (idx >= N * N)
        return;
    if (input_M[idx] > 100)
        output_M[idx] = 1;
    else if (input_M[idx] < 100)
        output_M[idx] = 0;
}
```

**核函数配置及调用**

```c
int main(int argc char **argv){
	...
	//kernel configuration
    dim3 dimGrid(ceil(N/TILE_DIM), ceil(M/TILE_DIM), 1);//网格大小配置
    dim3 dimBlock(TILE_DIM, BLOCK_ROWS, 1);//块大小配置
    //<<<执行配置>>>指定了线程数量以及线程分组
    gpu_kernel<<<dimGrid, dimBlock>>>(inputMat, outputMat);
	...
}
```



## CUDA程序的编译

![image-20210816143817075](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816143817075.png)

![image-20210816144054858](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/16/image-20210816144054858.png)

### NVCC使用案例

![image-20210822141032544](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822141032544.png)

```bash
nvcc --device-c hello_from_gpu.cu -o hello_from_gpu.o
nvcc hello_from_gpu.o hello_cuda_main.cu -o hello_from_gpu
```



## CUDA程序性能分析

### NVPROF 

Kernel Timeline输出的是以gpu kernel为单位的一段时间的运行时间线，我们可以通过它观察GPU在什么时候有闲置或者利用不够充分的行为，更准确地定位优化问题。nvprof是nvidia提供的用于生成gpu timeline的工具，其为cuda toolkit的自带工具。

可以结合nvvp和nsight进行可视化分析。

### NVPROF 使用案例

**查看a.exe的运行总体情况：**

在不指定参数的情况下默认输出`--print-api-summary`

执行时可能需要管理员权限

```bash
nvprof a.exe
```

![image-20210822141513806](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822141513806.png)

**查看a.exe的运行流程：**

需要指定参数：`--print-api-trace`

```bash
nvprof --print-api-trace a.exe
```

![image-20210822141641403](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/08/22/image-20210822141641403.png)

# 实验

## 编写第一个CUDA程序

- 关键词：\"\_\_global\_\_\" ,  <<<...>>>  ,  .cu

在当前的目录下创建一个名为hello_cuda.cu的文件，编写第一个Cuda程序：

**CPU版本**

```c
#include <stdio.h>

int main(void)
{
    printf("Hello World!\n");
    return 0;
}
```

编译：

```bash
nvcc hello_cuda.cu -o hello_cuda
```

输出：

```
Hello World!
```

GPU版本

```c
#include <stdio.h>

__global__ void cuda_hello(void){
    printf("Hello CUDA from GPU!\n");
}

int main(void){
    cuda_hello<<<8,1>>>();
    cudaDeviceSynchronize();
    return 0;
}
```

编译：

```bash
nvcc hello_cuda.cu -o hello_cuda
```

输出：

```
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
```

## 使用nvprof查看程序性能

```bash
sudo /usr/local/cuda/bin/nvprof --print-api-summary ./hello_cuda
```

输出：

```
==17055== NVPROF is profiling process 17055, command: ./hello_cuda
==17055== Warning: Unified Memory Profiling is not supported on the underlying platform. System requirements for unified memory can be found at: http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#um-requirements
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
Hello CUDA from GPU!
==17055== Profiling application: ./hello_cuda
==17055== Profiling result:
            Type  Time(%)      Time     Calls       Avg       Min       Max  Name
      API calls:   99.89%  361.69ms         1  361.69ms  361.69ms  361.69ms  cudaLaunchKernel
                    0.06%  213.18us        97  2.1970us     960ns  58.272us  cuDeviceGetAttribute
                    0.04%  136.67us         1  136.67us  136.67us  136.67us  cudaDeviceSynchronize
                    0.01%  21.152us         1  21.152us  21.152us  21.152us  cuDeviceTotalMem
                    0.00%  10.432us         3  3.4770us  1.5360us  5.4720us  cuDeviceGetCount
                    0.00%  8.0320us         2  4.0160us  2.1440us  5.8880us  cuDeviceGet
                    0.00%  2.9120us         1  2.9120us  2.9120us  2.9120us  cuDeviceGetName
                    0.00%  1.5040us         1  1.5040us  1.5040us  1.5040us  cuDeviceGetUuid
```

## 尝试利用CUDA API打印GPU参数

device.cu

```c
#include <cuda_runtime.h>
#include <iostream>
#include <memory>
#include <string>

int main(void){
    //define variables
    int deviceCount = 0;
    int dev = 0, driverVersion = 0, runtimeVersion = 0;
    
    //count device
    cudaError_t error_id = cudaGetDeviceCount(&deviceCount);
    printf("Detected %d CUDA Capable device(s)\n", deviceCount);
    
    //select device
    cudaSetDevice(dev);
    cudaDeviceProp deviceProp;
    cudaGetDeviceProperties(&deviceProp, dev);
    printf("Device %d: \"%s\"\n", dev, deviceProp.name);
    
    //CUDA info
    cudaDriverGetVersion(&driverVersion);
    cudaRuntimeGetVersion(&runtimeVersion);
    printf("CUDA Driver Version / Runtime Version          %d.%d / %d.%d\n",
           driverVersion / 1000, (driverVersion % 100) / 10,
           runtimeVersion / 1000, (runtimeVersion % 100) / 10);
    printf("CUDA Capability Major/Minor version number:    %d.%d\n",
           deviceProp.major, deviceProp.minor);
    
    return 0;
}
```

编译：

```bash
nvcc device.cu -o device
```

输出：

```
Detected 1 CUDA Capable device(s)
Device 0: "Xavier"
CUDA Driver Version / Runtime Version          10.2 / 10.2
CUDA Capability Major/Minor version number:    7.2
```
