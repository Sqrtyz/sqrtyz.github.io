---
title: 【笔记】计算机组成 (Part III)
category: Notes
date: 2024-01-01
---

## Chapter 4-1 Processor: Single Cycle Ver

### Introduction

基本的思路是，在之前我们学习了 C -> Assembly Language -> Machine Code 的过程。而 CPU 的工作就是执行机器码。例如，对于指令 `add x10, x11, x12`，我们要用 CPU 完成以下工作：

+ 读取 `x11` 和 `x12`

+ 计算两者的和

+ 把和写到 `x10` 中

在本门课程中，我们主要关心以下指令：

|Inst Type|Inst|
|---|---|
|R-Type|Arithmetic (`add`, `sub`, `and`, ...)|
|I-Type|Load Register (`ld`)|
|S-Type|Store Register (`sd`)|
|SB-Type|Branch Jump (`beq`)|
|UJ-Type|Unconditional Jump (`jal`)|

### The Entire Processor

![cod-54](images/cod-54.png)

其实整个一节的关键信息就这一幅图。其中的一些关键点：

+ PC 表示当前的指令位置。

+ Instruction Memory 存放的是系列指令，可以理解为内存中的 Text 段。

+ Registers 寄存器。RISC-V 具有 32 个各 64 位的寄存器。

+ Data Memory 存放的是各种数据，可理解为内存中的 Data 段。

黑色的部分对应 CPU 的 Data Path，蓝色的部分对应 CPU 的 Control Unit。可以看到，Control 部分由 Opcode 全权完成。

接下来根据指令类型的不同，详细分析数据的走向。注意下面的图片仅作示意，不一定严谨。有的图片还有订正。

### Processor Datapath Details

#### 1. R-Type

![cod-55](images/cod-55.png)

#### 2. I-Type (ld)

![cod-58](images/cod-58.png)

**[ATTENTION]** 图片有点问题，指令中的 `rs1` 连到寄存器的 `rs2` 上了，应当连到 `rs1` 上。

+ 其中 `20{inst[31]}` 是在做符号位拓展，因为本来也支持立即数为负的情况。

#### 3. S-Type (sd)

![cod-57](images/cod-57.png)

#### 4. SB-Type (beq)

![cod-56](images/cod-56.png)

+ 这里 ALU 执行的是 sub 运算，然后根据 `zero` 判断是否相等。如果 `zero` 为 1（beq 条件满足）且 `branch` 为 1（本条指令为 beq），那么就开跳。

#### 5. UJ-Type (jal)

![cod-59](images/cod-59.png)

+ 注意是 PC+4 进入到 `Write Data` 中。

### Controller

回顾一开始整个单周期 CPU 的图片，我们再贴一次：

![cod-54](images/cod-54.png)

根据 Opcode，我们需要通过 Controller 给出以下 8 个具体信号：

+ R/W - RegWrite (0/1): 控制是否向寄存器里面写入。
+ R/W - MemRead (0/1): 控制是否从内存读取。
+ R/W - MemWrite (0/1): 控制是否向内存中写入数据。
+ MUX - ALUSrc (0/1): 控制 ALU 下侧的数据来源，0 表示 rs2，1 表示 ImmGen。
+ MUX - Branch (0/1): 表示当前指令是否为 branch jumping，用于控制下一个 PC。0 表示不为 branch，1 表示为 branch。结合 Zero 进行食用。
+ MUX - Jump (0/1): 控制 PC 是否进行无条件跳转。例如当前指令为 `jal` 时该值为 1。
+ MUX - MemtoReg (2 bits): 控制返回到 write address 中的数据来源。分别表示 PC+4，内存读出数据，ALU 计算结果。
+ ALU op (2 bits): 用于指导 ALU 进行的运算类型。后续会过一次 ALU Control。

**接下来专门讲一下 ALU 的具体控制**。可以看到，ALU 的控制可以视为「两级」：

+ 第一级：根据 ALU op 将不同类别的指令进行基础区分。

+ 第二级：相同 ALU op 的基础上，再根据 funct3 和 funct7 对不同运算的指令进行区分（这部分 funct 从上图的 `inst[30,14:12]` 线钻进去）。

![cod-60](images/cod-60.png)

或许可以回顾一下 ALU：

![cod-9](images/cod-9.png)