---
title: 和廖老师一起学嵌入式操作系统
category_bar: true
date: 2021-05-24 12:48:12
tags: ['cs', 'review', 'eos', 'os']
categories:
- 技术
- Course
---

## 任务管理

### 任务概念

- 任务是一个程序运行的实体，资源占用的基本单位，也可能是系统调度的基本单元。
- 动态性、并发性、异步独立性。
- 一个任务也称作一个线程。

### 任务描述

```C
OSTCBStkPtr
OSTCBNext
OSTCBPrev
OSTCBDly
OSTCBStat
OSTCBPrio

OSTCBX
OSTCBY
OSTCBBitX
OSTCBBitYCOPY
```

### 任务状态

睡眠状态、就绪状态、运行状态、等待状态、中断服务状态。

![image-20210518104737047](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518104737047.png)

### 任务创建

![OSTaskCreat](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/OSTaskCreat.png)

```c
INT8U  OSTaskCreate (void (*task)(void *p_arg), void *p_arg, OS_STK *ptos,INT8U prio)
{
    OS_STK    *psp;
#if OS_CRITICAL_METHOD == 3               
     OS_CPU_SR  cpu_sr = 0;
#endif
    OS_ENTER_CRITICAL();   
    if (OSIntNesting > 0) {
           OS_EXIT_CRITICAL();
           return (OS_ERR_TASK_CREATE_ISR);
    }                                                                       
if (OSTCBPrioTbl[prio] == (OS_TCB *)0) {
           OSTCBPrioTbl[prio] = (OS_TCB *)1;   
           OS_EXIT_CRITICAL();
    psp = (OS_STK *)OSTaskStkInit(task, pdata, ptos, 0); 
    err = OS_TCBInit(prio, psp, (OS_STK *)0, 0, 0, (void *)0, 0);
     if (err == OS_NO_ERR) {
                OS_ENTER_CRITICAL();
                OSTaskCtr++;                                      
                OS_EXIT_CRITICAL();
                if (OSRunning == TRUE) {   
                      OS_Sched();
                }
}else {
            OS_ENTER_CRITICAL();
            OSTCBPrioTbl[prio] = (OS_TCB *)0;
            OS_EXIT_CRITICAL();
        }
        return (err);
    }
    OS_EXIT_CRITICAL();
    return (OS_PRIO_EXIST);
}
COPY
```

#### 装入优先级位图

**用优先级初始化参数**

```c
ptcb->OSTCBY = prio >> 3;                                  
ptcb->OSTCBBitY= OSMapTbl[ptcb->OSTCBY];//掩码表中查询
ptcb->OSTCBX = prio & 0x07;
ptcb->OSTCBBitX = OSMapTbl[ptcb->OSTCBX];COPY
```

**更新位图**

```c
OSRdyGrp |= ptcb->OSTCBBitY;        
OSRdyTbl[ptcb->OSTCBY] |= ptcb->OSTCBBitX;COPY
```

### 临界区代码保护

```c
#if  OS_CRITICAL_METHOD==1 
     #define  OS_ENTER_CRITICAL()   (Cli()) 
     #define  OS_EXIT_CRITICAL()      (Sti())  

#elseif   OS_CRITICAL_METHOD == 2  
     #define  OS_ENTER_CRITICAL()   (PushAndCli())  
     #define  OS_EXIT_CRITICAL()     (Pop()) 

#elseif   OS_CRITICAL_METHOD == 3  
     #define  OS_ENTER_CRITICAL()  (cpu_sr = OSCPUSaveSR())
     #define  OS_EXIT_CRITICAL()   (OSCPURestoreSR(cpu_sr))
#endifCOPY
```

#### OS_CRITICAL_METHOD==1

![image-20210518105735334](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518105735334.png)

#### OS_CRITICAL_METHOD==2

![image-20210518105921392](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518105921392.png)

#### OS_CRITICAL_METHOD==3

![image-20210518105953615](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518105953615.png)

### 挂载到就绪队列

```c
#if OS_LOWEST_PRIO <= 63  
    INT8U  y;
    y= OSUnMapTbl[OSRdyGrp];           
    OSPrioHighRdy = (INT8U)((y << 3) + OSUnMapTbl[OSRdyTbl[y]]); COPY
```

### 上下文切换

![image-20210518111852456](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518111852456.png)

#### 相关概念

![image-20210518111924582](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518111924582.png)

#### 上下文切换过程

![image-20210518112053885](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/18/image-20210518112053885.png)

**保存上文**

```asm
OSCtxSw:
    STMFD   SP!, {LR}   ;PC ----------------------------------------（1）
    STMFD   SP!, {R0-R12, LR}   ;R0-R12 LR--------------------------（2）
    MRS     R0,  CPSR   ;Push CPSR                      
    STMFD   SP!, {R0}   
    ;----------------------------------------------------------------------
    ;       OSTCBCur->OSTCBStkPtr = SP
    ;-------------------------------------------------------------------
    LDR     R0, =OSTCBCur
    LDR     R0, [R0]
    STR     SP, [R0]COPY
```

（1）此时想要保存的**PC**已经变化，正好等于LR。如果直接保存，此时的**PC**指向当前正在执行的代码。

（2）此时的**LR**内的值具体是什么分两种情况。

- 主动发起任务切换

  此时两个**LR**的值相等，都保存的是进入上下文切换之前的的**PC**。

- 中断触发任务切换

  第一个**LR**是**IRQ_LR**是进入上下文切换之前的的**PC**，第二个**LR**是**SVC_LR**，是就任务的**LR**。

**获取当前最高优先级任务**

```asm
    ;----------------------------------------------------------------------
    ; OSTCBCur = OSTCBHighRdy;
    ;----------------------------------------------------------------------
    LDR     R0, =OSTCBHighRdy
    LDR     R1, =OSTCBCur
    LDR     R0, [R0]
    STR     R0, [R1]COPY
```

将当前TCP指针指向当前最高优先级任务TCB。

```asm
    ;----------------------------------------------------------------------
    ; OSPrioCur = OSPrioHighRdy;
    ;----------------------------------------------------------------------
    LDR           R0, =OSPrioHighRdy
    LDR           R1, =OSPrioCur
    LDRB              R0, [R0]
    STRB              R0, [R1]  COPY
```

将当前最高优先级赋值到当前任务优先级。

**恢复现场**

```asm
    ;----------------------------------------------------------------------
    ;  OSTCBHighRdy->OSTCBStkPtr;
    ;----------------------------------------------------------------------
    LDR     R0, =OSTCBHighRdy                                            
    LDR     R0, [R0]
    LDR     SP, [R0]
    ;----------------------------------------------------------------------
    ;Restore New task context
    ;----------------------------------------------------------------------
    LDMFD   SP!, {R0}   ;POP CPSR
    MSR     SPSR_cxsf, R0
    LDMFD   SP!, {R0-R12, LR, PC}^
COPY
```

赋值堆栈指针，指向新任务堆栈，先恢复LR，随即出栈。

### 调度点

- 中断服务程序结束的时刻。
- 运行任务因资源缺乏被阻塞的时刻。
- 任务周期的开始或者结束的时刻。
- 高优先级任务就绪的时刻。

### 课后习题

> 什么是任务？任务有哪几种状态？任务和线程有什么区别？
>
> 什么是任务切换？

见任务切换相关概念处。

> 叙述RTOS如何创建一个任务。

**初始化栈**

拿到ptos，模拟压栈，顺序。

满递减堆栈，从高位开始往下生长，最先压任务地址，LR、R12、R11……R0，CPSR，栈顶返回psp

*(--stk) = (int32u) 0;

弹出的时候，先出cpsr，最后任务地址弹到PC。

**初始化任务TCB**

到临界区，去OSTCBFreeList中申请第一个空白TCB。拿到后，往里面放东西

同时XY bitX bitY直接使用prio通过优先级表进行定位查找。
Y = prio >>3（高3位）
bitY = OSMapTbl[Y]
X = prio & 0x07（低3位）
bitX = OSMapTbl[X]

装完后，用上之前那OSTCBPrioTbl[prio] = ptcb 的占位符，然后和OSTCBList里的TCB双向链上（next、prev），作为队首

然后将OSRdyGrp |= bitY 与 OSRdyTbl[Y] |= bitX；OStaskCtr++，退出临界区

OSRdyGrp 表示逻辑上优先级表中有哪些组的优先级有任务就绪。

OSRdyTbl[]表示某一组的8个优先级中有哪些任务就绪

**初始化TCBList**

先清空OSTCBTbl和OSTCBPrioTbl的空间；

然后两个OS_TCB指针ptcb1 ptcb2来对OSTCBTbl链Next；

最后给TCBList = (OS_TCB*)0 与TCBFreeList = &OSTCBTbl[0]；

（真实存在的连续数据结构表就只有）TCB、OSTCBTbl，其他的都是在这个里面的指针串起来的数据结构。

## 任务调度策略

### 提高实时任务响应的措施

- 内核可抢占
- 调度时间的确定性
- 中断处理优化
- 数据结构优化
- 内存管理的确定性

![image-20210519124332664](https://wpcos-1300629776.cos.ap-chengdu.myqcloud.com/Gallery/2021/05/19/image-20210519124332664.png)

## 中断和时间管理

- 硬中断：传统意义的外部中断，触发的响应属于异步事件
- 自陷：软中断，内部中断，内部显式触发
- 异常：CPU自动产生的自陷，如被0除，非法访问等……

### 中断请求处理方式

- 中断作为任务切换
- 中断作为系统调用
- 中断作为前台任务

### ARM裸板中断机制

两级中断机制，第一级（异常向量表，里面放的时B xxx指令，发生异常后PC会被强制转到对应的位置取值，然后跳转到二级）：

- 复位异常：开发板复位时进入管理模式，屏蔽IRQ和FIQ，进入ARM指令模式，PC强制为0，从0处去指执行
- 未定义异常：遇到无法处理的指令时触发，PC强制为0x04
- 软中断异常：SWI……PC强制0x08，进管理
- 预取指异常：……
- 数据终止异常：……
- IRQ：CPU收到外部设备发来的中断请求后，PC强制0x18，进第二级
- FIQ：优先级高于IRQ

#### 2440前后台系统实现

外部中断来了，PC被强转到0x18，然后跳转到服务程序，然后执行服务程序。ISR中间涉及清中断的处理。

中断返回：中断结束后如何返回原程序.

- 进入中断的时候LR的值是当前PC+4，中断返回的时候该是LR-4：`SUB PC，LR，#4`
- CPSR存入当前SPSR。
- CPSR设置为相应中断模式（是否屏蔽I位F为）。
- PC强制更新。
- 中断注册：多个中断源如何区分？

 2440有一个寄存器叫`INTOFFSET`，存中断源分配到的整数，这个整数可以为负数，跳到内部异常

```asm
ldr r0,=INTOFFSET

ldr r0,[r0]

ldr r1,=HandleEINT0;HandleEINT0是一个内存地址，内容对应一个中断服务程序函数入口

ldr pc,[r1,r0 lsl #2];进入二级向量表，进行注册COPY
```

- 状态保存与现场恢复

```asm
ISR
    SUB LR,LR,#4 ;减4  四级流水
    STMFD SP!,{R0-R12,LR} ;保存现场
    MRS R0,SPSR     取spsr
    STMFD SP!,{R0}  保存
    LDR R0,=INTOFFSET
    LDR R0,[R0]
    LDR R1,=HandleEINT0
    ADD R1,R1,R0 LSL #2
    LDR R1,[R1]
    MOV LR,PC
    MOV PC,R1
    LDMFD SP!,{R0}
    MSR SPSR_cxsf,R0
    LDMFD SP!,{R0-R12,LR}
    MOVS PC,LRCOPY
```

### uCOSII中断管理

#### 中断发生与响应

```asm
ISR STMDB SP!,{R0-R2};存r0-r2到当前IRQ模式的堆栈中
    MOV R0,SP   ;R0存着SP
    ADD SP,SP,#12 ;SP+12留3个位置
    SUB R1,LR,#4  ;LR-4放R1
    MRS R2,SPSR   ;SPSR放R2

    MSR CPSE_cxsf，#SVCMODE|NOINT ;转到SVC模式，都是SVC的堆栈

    STMDB SP!,{R0} ;压IRQ_SP
    STMDB SP!,{R3-R12,LR} ;压剩下的到SVC的堆栈
    LDMIA R0!,{R3-R5}   ;出原来IRQ的R0-R2到现在SVC的R3-R5
    STMDB SP!,{R2-R5}   ;再和前面的SPSR压回SVC的栈

    LDR R0,=OSIntNesting ;中断嵌套计数
    LDR R1,[R0]
    ADD R1,R1,#1
    STRB R1,[R0]
    TEQ R1,#1       ;判断是否是1，，如果是1没嵌套，不是就嵌套了
    BNE %F1

    LDR R0,=OSTCBCur 
    LDR R0,[R0]
    STR SP,[R0]     ;sp给到当前TCB的堆栈顶

F1  MSR CPSR_c,#IRQMODE|NOINT 出SVC模式，回到IRQ

    LDR R0,=0X4A000014 
    LDR R0,[R0]
    LDR R1,=HandleEINT0
    MOV LR,PC
    LDR PC,[R1,R0 LSL #2] ;向量表偏移后入口指令给PC，然后执行ISR后回来

    MSR CPSR_c,#SVCMODE|NOINT ;切到SVC
    BL OSIntExit    ;中断返回，有调度点
    LDR R0,[SP],#4  ;出栈，把状态字出到R0
    MSR SPSR_cxsf,R0 ;恢复状态字
    LDMIA SP!,{R0-R12,LR,PC}^ ;全部出栈，恢复COPY
```

#### 中断返回

判断是否嵌套，没有嵌套就从就绪队列中找到最高优先级的任务，把TCB给OSTCBHighRdy，进入中断的上下文切换。

### 课后习题

> 阐述中断的概念，说明中断、自陷、异常之间的区别。
>
> 以一个开源RTOS为例，给出中断的层次结构，并对总流程及关键步骤进行解释。
>
> 开发人员如何在2440的uC环节下实现中断注册？
>
> 裸板中断和uC中断处理有哪些主要区别？
>
> uC的TimeTick函数主要功能是什么？如何实现差分时间链算法？提供代码

## 任务的同步与通信

### 任务之间的通信、ECB结构与初始化

 ECB是一个单独的连续表结构体`OSEventFreeList`，每一个ECB和一个优先级（任务绑定）

 `OS_EventWaitListInit()`：初始化一个ECB的等待队列

 `OS_EventTaskRdy()`：使一个任务进入就绪状态（从等待队列中删除最高优先级，然后将该任务就绪）

 `OS_EventWait()`：使一个任务进入事件的等待队列

 `OS_EventTo()`：因为超时是而使得一个任务进入就绪状态

### 信号量机制

 同步信号量（初始0，加）、互斥信号量（初始1，减）、计数信号量（初始n，加减），不支持中断创建信号量。

 `OSSemCreate(cnt)`：创建一个信号量

 `OSSemPend()`：获取一个信号量

 `OSSemPost()`：释放一个信号量

 `OSSemAccept()`：无等待的获取一个信号量

 `OSSemQuery()`：查询信号量当前状态

### 互斥锁

 `OSMutexCreat()` `OSMutexAccept()` `OSMutexDel()` `OSMutexPend()` `OSMutexPost()` `OSMutexQuery()`

### 邮箱机制

 实现任务之间或者任务与中断之间通信，发送一个指针类型的变量，该指针包含特定消息的数据结构

 `OSMboxCreate(*msg)` `OSMboxDel()` `OSMboxPend()` `OSMboxPost()` `OSMboxAccept()` `OSMboxQuery()`

### 消息队列

 `OSQCreate()` `OSQPend()` `OSQPost()` `OSQPostFront()` `OSQAccept()` `OSQFlush()` `OSQQuery()`