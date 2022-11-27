---
title: 感知器（神经网络模型）
date: 2020-06-19 11:41:35
category_bar: true
tags: ['ai', 'nn', 'pnn', 'algorithm']
categories:
- 技术
- Algorithm
- AI
---

**感知器**是人工神经网络中的一种典型结构， 它的主要的特点是结构简单，对所能解决的问题 存在着收敛算法，并能从数学上严格证明，从而对神经网络研究起了重要的推动作用。

**感知器**，也可翻译为**感知机**，是Frank Rosenblatt在1957年就职于Cornell航空实验室(Cornell Aeronautical Laboratory)时所发明的一种人工神经网络。它可以被视为一种最简单形式的前馈式人工神经网络，是一种二元线性分类器。 Frank Rosenblatt给出了相应的感知器学习算法，常用的有感知机学习、最小二乘法和梯度下降法。譬如，感知机利用梯度下降法对损失函数进行极小化，求出可将训练数据进行线性划分的分离超平面，从而求得感知器模型。

感知器是生物神经细胞的简单抽象，如右图.神经细胞结构大致可分为：树突、突触、

[![图1.神经细胞示意图](https://bkimg.cdn.bcebos.com/pic/810a19d8bc3eb1357c1bdf11a41ea8d3fc1f44bf?x-bce-process=image/resize,m_lfit,w_220,h_220,limit_1)](https://baike.baidu.com/pic/%E6%84%9F%E7%9F%A5%E5%99%A8/16525448/0/810a19d8bc3eb1357c1bdf11a41ea8d3fc1f44bf?fr=lemma&ct=single "图1.神经细胞示意图")

神经细胞示意图

细胞体及轴突。单个神经细胞可被视为一种只有两种状态的机器——激动时为‘是’，而未激动时为‘否’。

 神经细胞的状态取决于从其它的神经细胞收到的输入信号量，及突触的强度（抑制或加强）。当信号量总和超过了某个阈值时，细胞体就会激动，产生电脉冲。电脉冲沿着轴突并通过突触传递到其它神经元。为了模拟神经细胞行为，与之对应的感知机基础概念被提出，如权量（突触）、偏置（阈值）及激活函数（细胞体）。 在人工神经网络领域中，感知器也被指为单层的人工神经网络，以区别于较复杂的多层感知器（Multilayer Perceptron）。 作为一种线性分类器，（单层）感知器可说是最简单的前向人工神经网络形式。尽管结构简单，感知器能够学习并解决相当复杂的问题。感知器主要的本质缺陷是它不能处理线性不可分问题。

### 模型构建的代码实现（Python）

单层感知器模型

```
import numpy as np
#sigmoid 激活函数
def nonlin(x, deriv = False):
	if(deriv==True):
	return x*(1-x)
	return 1/(1+np.exp(-x))

#输入-输出
#[0,0,1]-0
#[0,1,1]-1
#[1,0,1]-1
#[1,1,1]-0

#输入矩阵
X=np.array([[0,0,1],
	[0,1,1],
	[1,0,1],
	[1,1,1]])

# 目标矩阵
y=np.array([[0,1,0,1]]).T

#生成随机权重
np.random.seed()
weight = np.random.random((3,1))

#训练次数
times=input(How many times you want to train:\n)
times=int(times)

#训练开始
for iter in range(times):
	#计算输出
	L0=X
	L1=nonlin(np.dot(L0,weight))
	#计算误差
	L1_error=y-L1
	L1_delta = L1_error*nonlin(L1,True)
	#更新权重

	weight+=np.dot(L0.T,L1_delta)

print(Output After Training:)
print(L1)
print(The Weight After Training:)
print(weight)

```

测试结果：

```
How many times you want to train:
10000
Output After Training:
[[0.00966633]
	[0.99359071]
	[0.00786352]
	[0.99211866]]
The Weight After Training:
[[-0.20823225]
. [ 9.67307298]
. [-4.6294448 ]]
```