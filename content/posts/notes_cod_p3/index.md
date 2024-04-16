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

#### 2. I-Type (ONLY ld)

![cod-58](images/cod-58.png)

**[ATTENTION]** 图片有点问题，指令中的 `rs1` 连到寄存器的 `rs2` 上了，应当连到 `rs1` 上。

+ 其中 `20{inst[31]}` 是在做符号位拓展，因为本来也支持立即数为负的情况。

#### 3. S-Type (ONLY sd)

![cod-57](images/cod-57.png)

#### 4. SB-Type (ONLY beq)

![cod-56](images/cod-56.png)

+ 这里 ALU 执行的是 sub 运算，然后根据 `zero` 判断是否相等。如果 `zero` 为 1（beq 条件满足）且 `branch` 为 1（本条指令为 beq），那么就开跳。

#### 5. UJ-Type (ONLY jal)

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



## Chapter 4-2 Processor: Pipeline Ver

### Intro

+ 不同指令耗时不一

    例如在下图中，假设各部分的操作时长为：Memory Access, 200ps; ALU, 200ps; RegFile Access, 100ps。
    
    那么执行一次 `ld` 指令耗时为 800ps。执行一次 `sd` 指令耗时为 700ps。

    <p><img src="images/cod-61.png" alt="cod-61" width="80%"></p>

+ Pipelining Analogy

    一种优化思路是从瓶颈（例如 `ld`）下手。然而我们还有另外一种优化思路——将【单周期 CPU】改为【流水线 CPU】。所谓流水线，可以参照这幅图：

    <p><img src="images/cod-62.png" alt="cod-62" width="70%"></p>

    对应到 CPU 中，我们可以尝试把其分为若干个区域（或步骤），让每个区域同时工作，实现整体的流水线工作。

### RISC-V Pipeline Intro

RISC-V CPU 可以划分为以下五个阶段：

1. **IF**: Instruction fetch from memory
2. **ID**: Instruction decode & read from register
3. **EX**: Execute operation or calculate address (ALU Part)
4. **MEM**: Access memory operand
5. **WB**: Write result back to register

然后，一种简单的思路就是，我让五个阶段「流水线式」地工作，下图地两张图生动地说明了这一原理：

<p><img src="images/cod-63.png" alt="cod-63" width="90%"></p>

<p><img src="images/cod-64.png" alt="cod-64" width="90%"></p>

理想情况下，这一转变将让 CPU 提速 5 倍（因为有 5 个阶段，当然实际不会到达这个数值，看上图的下半部分可以理解；而且后面还有各种 hazards）。

### Hazards

中文意为「冒险」，表示流水线 CPU 实现中可能遇到的各种容易出问题的情况。

#### 1. Structure Hazard

可以想象，如果 data memory 和 instruction memory 是在同一部分的话，在 pipeline 的情形下，有的周期会调用同一块内存，这将产生冲突。

好在 RISC-V 的设计将 data memory 和 instruction memory 分隔开，所以我们几乎不用考虑 Structure Hazard。

#### 2. Data Hazard

+ Data Hazard

    有的时候执行某一个指令，要求前一个指令完成数据写入。考虑这样的两个相邻指令：

    ```ass
    add x19, x0, x1
    sub x2, x19, x3
    ```

    <p><img src="images/cod-65.png" alt="cod-65" width="90%"></p>

    add 指令的 WB 必须要在 sub 指令的 ID 之前执行（原因显然），所以两个指令之间无法再「紧密地排列在流水线上」，中间需要隔两个 bubble。

    【OBSERVATION】观察上上张图，WB 的写 reg 是在上半 cycle，ID 的读 reg 是在下半 cycle。这种设计的缘由其实在这里有所体现。

+ Forwarding

    解决这种情况的一种方案是 Forwarding。大概思路是有的时候我们不必等待一个数据被写入 reg，然后才再次使用；而是可以直接用某种 extra connection 来直接高效地使用。下图演示了 Forwarding 的解决方案。

    <p><img src="images/cod-66.png" alt="cod-66" width="90%"></p>

    然而，并非所有 Data Hazard 都可以用 Forwarding 解决。考虑这样的情形：

    <p><img src="images/cod-67.png" alt="cod-67" width="90%"></p>

    显然 sub 已经没办法再提前一个 cycle 了，不然 sub 的 EX 和 ld 的 MEM 将处在同一 cycle，而 sub 的 EX 需要 ld 的 MEM 提供前提数据。

    不过，也可以通过修改汇编代码来规避这一问题——

    <p><img src="images/cod-68.png" alt="cod-68" width="90%"></p>