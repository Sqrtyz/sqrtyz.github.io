---
title: 【笔记】计算机组成 (Part IV)
category: Notes
date: 2024-05-01
---

## Chapter 5 Memory Hierarchy

### Introduction

#### Transistor 晶体管

<p><img src="images/cod-84.png" alt="cod-84" width="80%"></p>

类似于一个开关。栅极为 1 时两侧的 N 型半导体硅连通，input 连接到 output。


#### Basic Memory Types

+ SRAM (Static Random Access Memory)

    六个晶体管。static 是指这种存储器只需要保持通电，里面的数据就可以永远保持。但是当断点之后，里面的数据仍然会丢失。
    
    SRAM 速度较快，但是成本更高，占用的空间更多。所以像诸如 CPU 的高速缓存，才会采用 SRAM。

    <p><img src="images/cod-85.png" alt="cod-85" width="40%"></p>


+ DRAM (Dynamic Random Access Memory)

    一个电容加一个晶体管。由于 DRAM 使用电容存储，所以必须隔一段时间 refresh 一次，否则因为不断的微小漏电可能导致数据丢失。
    
    DRAM 速度较慢，但是成本更低。所以可以用于作为 main memory。

    <p><img src="images/cod-86.png" alt="cod-86" width="40%"></p>

+ Disk Storage

    <p><img src="images/cod-98.png" alt="cod-98" width="80%"></p>

    + Structure

        + **Platter** 盘子。一个磁盘一般有 4-16 个盘子。

        + **Track** 轨道。对应图上不同半径的环。

        + **Sector** 扇区。每个 track 又被分割为若干 sectors。

        + **Cylinder** 所有 platter 同一半径的 track 集合。

        + **Read-write head** 读写头。

    + Average Read Time of Disks

        基本方法为考虑四部分：

        1. 寻道时间 Seek time
        2. 旋转等待时间 Rotational latency
        3. 数据传输时间 Transfer time
        4. 控制用时 Control time

        【例题】Given 512B sector, 15,000 rpm, 4ms average seek time, 100MB/s transfer rate, 0.2ms controller overhead. Calculate average read time.
        
        1. **4ms** seek time
        2. $\frac{1}{2} \times \frac{15000}{60}$ = **2ms** rotational latency（1/2 表示期望）
        3. $\frac{512B}{100MB/s}$ = **0.005ms** transfer time
        4. **0.2ms** controller delay

        Total: **6.2ms**

+ Flash Storage

    中文为闪存。比硬盘快了 100~1000 倍。Flash 又分为 NOR flash 和 NAND flash。
    
    我们熟知的 SSD（固态硬盘）一般就使用闪存存储数据。

#### Memory Hierarchy

Hierarchies bases on memories of different speeds and size.

金字塔越往上，memory 更 expensive / small / fast。

<p><img src="images/cod-87.png" alt="cod-87" width="90%"></p>

### Cache Basics

#### Cache Intro - Direct Mapped Cache

+ 基本思路

    <p><img src="images/cod-88.png" alt="cod-88" width="80%"></p>

    **(Cache Block Address)** = **(Memory Block Address)** mod (Number of blocks in the cache)

    注意，一个 block 中不是一个 byte。RISC-V 中我们默认一个 block 的 **最小大小** 为 word (4 byte)。

    为了得知一个 cache block 中究竟存的是哪一个 memory block 的数据（例如上图中，一个灰色块可以对应 8 个灰色块），cache block 还会存下「其所存储的数据」对应的 memory block 地址的「高若干位」。具体地，一个 DMC 的结构如下所示：

+ DMC 的结构

    <p><img src="images/cod-89.png" alt="cod-89" width="60%"></p>

    1. V 表示 Valid bit，表示是否启用。启用为 1，反之为 0。

    2. Tag 即为刚刚所说的，「其所存储的数据」对应的 memory block 地址的「高若干位」。例如，对于上图例 Memory 中的最左边的灰色块，它的数据在 Cache 中存储在 001 的 Data 段，Tag 段对应为 00。

    3. Data 存储的就是数据。如果一个 block 的大小为 1 word，那么 Data 段的长度就是 32 位。

+ DMC 的寻址

    考虑一个情景，CPU 希望从内存中抓一个地址（记得是按 Byte 寻址哦）。这个 64 位的地址将会被分为 TAG / Index / Byte offset。其中 {Tag, Index} 联称 Memory Block Address。

    注意，当一个 block 包含 1 word 时，byte offset 占 2 位；但有时候一个 block 也可以占更多的字（比如 4 word），此时 byte offset 会占更多位（比如 4 位）。

    <p><img src="images/cod-90.png" alt="cod-90" width="80%"></p>

#### Cache Topic 1 - Block Placement

<p><img src="images/cod-91.png" alt="cod-91" width="80%"></p>

除了 Direct Mapped，还有两种常见的映射策略：

+ Fully Associative

    内存中的 block 可以对到 cache 中的任何 block。好处是提高了 cache 的利用率，但是寻址会很麻烦。

+ Set Associative

    介于 Direct Mapped 和 Fully Associative 之间。内存中的 block 可以对到 cache 中 **某个 set** 的任何 block。set 的选取和 block 一致，即 **(Cache Set Address)** = **(Memory Block Address)** mod **(Number of sets in the cache)**
    
    对于 Set Associative，一个 set 如果包含 $k$ blocks，那么这个 cache 又被称为 $k$-way set associative。DM 和 FA 可以视作特殊的 SA。比如 DM 就是 $1$-way SA，FA 就是 $m$-way SA（其中 $m$ 表示 block 数目）。

#### Cache Topic 2 - Block Identification

基本思路就是按照 Direct Mapped 那样。

<p><img src="images/cod-92.png" alt="cod-92" width="50%"></p>

Direct Mapped 的具体寻址方式之前已经介绍。

对于 Fully Associative，寻址方式如下：

<p><img src="images/cod-93.png" alt="cod-93" width="70%"></p>

对于 Set Associative，寻址方式如下：

<p><img src="images/cod-94.png" alt="cod-94" width="80%"></p>

#### Cache Topic 3 - Block Replacement

如果一个 cache 满了，再从内存往里面放东西的话（例如 read miss），我们需要对其中的数据进行替代。

+ 对于 Direct Mapped，没有这方面的担心。因为映射是多对一的，每次要么换要么不换。

+ 对于 Fully Associative 和 Set Associative，则需要考虑换掉哪个 block。一般有如下的策略：

    1. Random replacement 字面意思，随机替换。

    2. Least-recently used (LRU) 最长时间没用的替换掉。

    3. First in, first out (FIFO) 队列思想，最早引入 cache 的替换掉。注意这和 LRU 不一样。

#### Cache Topic 4 - Read and Write Strategy

+ Read Strategy

    + 如果 Read hits，无事发生，这很好。

    + 如果 Read misses，我们需要执行一系列措施。实际上，Read misses 分为 instruction cache miss 和 data cache miss。以 instruction cache miss 为例：

        1. 将目前的 PC 值告诉 Memory，意思是我接下来要读 PC 对应的指令了，但是我在 Cache 部分没找到，所以要去 Memory 部分找。

        2. Memory access 到对应的 32 位指令。

        3. 将对应的指令写进 instruction cache。其中 data 段放入指令本身，tag 段放入高位地址（ALU 来计算高位），valid bit 要设为 1。

        4. 重启 PC 指令的 fetching，这次我们可以在 instruction cache 读取到它。

+ Write Strategy

    + Write Hit Strategy

        1. Write-back 只向缓存中写入数据，回头找个合适的时机写入内存。好处是很快，但是会导致数据的 inconsistent。

            此外，Write-back 不能直接丢弃 cached data（例如由 read miss 引起的替换），这是因为其中的值可能并未被同步到 memory。故而 cache 的 control bit 需要使用两位：valid bit【依然和原来一样，表示是否数据有效】和 dirty bit【表示是否同步到 memory 中，未同步则为 0】。如果替换时发现被替换者 dirty bit 为 0，则需要先将被替换者写入内存。

        2. Write-through 则同时向缓存和内存写入数据。好处是可以保证数据的 consistency，但是速度会较慢。

            对于 Write-through，我们可以放心地丢弃 cached data，因为其中的值和 memory 始终保持同步。此类 cache 的 control bit 只有 valid bit 一位。

    + Write Stall and Write Buffers

        **Write stall** 指的是在 Write-through 中，CPU 必须等待 write to memory 完成所导致的 bubble。

        **Write buffer** 如下图所示。对于 Write-through 的策略，由于理论上我们需要时刻把 cache 和 memory 保持同步，而这会耗费大量的时间。所以考虑 cache 同步到 memory 时，我们先把它写到一个 buffer 里面（这很快），写完后 CPU 继续干自己的活，buffer 则慢慢地把数据同步到 memory 中。

        <p><img src="images/cod-95.png" alt="cod-95" width="80%"></p>

    + Write Miss Strategy

        1. 若 Write Hit Strategy 采取 Write-back，则 miss 时一般采取 Write allocate 策略（理论上也可以采取 Write around 策略）。

            Write allocate：**如果 Write misses，先把对应的内存数据读到缓存中**，转换为 Write hits 的情况再后续处理。
            
            注意，由于一个 block 可能包含若干个 word，所以我们也有必要这样做。考虑情景：本来一个 block 对应的 4 words 分别为 [A B C D]，考虑只写其中的一个 word。如果 write miss 发生，cache 中的对应 block 变为 [X E X X]（其中 [X X X X] 是对应 cache entry 的原始值），后面写到内存中，变为 [X E X X]，但实际上应该是 [A E C D]。

        2. 若 Write Hit Strategy 采取 Write-through，则 miss 时一般采取 Write around 策略（理论上也可以采取 Write allocate 策略）。

            Write around：**如果 Write misses**，直接绕过 cache 把对应的数据写入 memory。想来确实也可以这么干。

#### Cache-Memory Data Transfer

之前提到，如果发生 read miss 或者 write miss，则需要在 cache 与 memory 之间进行数据传输。对于不同的 Cache-Memory 结构，其传输效率也不一。

<p><img src="images/cod-96.png" alt="cod-96" width="80%"></p>

假设耗时（假设 Block size 为 4 words）：

|Work|CC Cost|
|---|---|
|send the address|1|
|DRAM access initiated|15|
|transfer a word of data|1|
|send the address|1|

1. One-word-wide memory organization

    最基本的架构。在以上假设下，读取一个 block 的耗时为 $1 + 4 \times (1+15)=65$ CC

2. Wide memory organization

    更宽的总线和内存。在以上假设下，读取一个 block 的耗时为 $1 + (1+15)=17$ CC

    然而，随之而来的代价是硬件设计的要求提高（最直观的，占空间变多了）

3. Interleaved memory organization

    内存分成几个 bank，这样一来内存部分可以同步进行 initialize。在以上假设下，读取一个 block 的耗时为 $1 + 4 \times 1 + 15=20$ CC


### Measure and Improve Cache Performance

#### 一些指标

+ Average Memory Assess Time (AMAT)

    = hit time + miss time

    = hit time + miss rate $\times$ miss penalty

+ CPU Time (更新定义)
    
    = CPU execution clock cycles + Memory-stall clock cycles

+ Memory-stall clock cycles 

    = Number of instructions $\times$ miss rate $\times$ miss penalty

    (Also can be written as) = Read-stall cycles + Write-stall cycles

+ Read-stall cycles 

    = number of read instructions $\times$ read miss rate $\times$ read miss penalty

+ Write-stall cycles **(For Write-through strategy)**

    = number of write instructions $\times$ write miss rate $\times$ write miss penalty + write buffer stalls

+ 如果忽略 write buffer stalls，Read-stall cycles 和 Write-stall cycles 理论上可以合并。统一为

    Memory-stall clock cycles ＝ Memory access instructions $\times$ Miss rate $\times$ Miss penalty

#### Example Quiz

<p><img src="images/cod-97.png" alt="cod-97" width="60%"></p>

#### Miss Penalty 一图流

<p><img src="images/cod-99.png" alt="cod-99" width="80%"></p>

### L1 and L2 Cache Hierarchy

回顾之前的金字塔图，我们可以将 Cache 分为 L1 Cache 和 L2 Cache（两者均采用 SRAM，唯一的区别在于大小不同，L1 Cache 略快于 L2 Cache）。

如果 L1 Cache miss 了，就去 L2 Cache 读取；如果再 miss，就去 DRAM。

考虑这样一道计算题：

+ CPI of 1.0 on a 5GHz machine 

+ Initial: 2% miss rate, 100ns DRAM access

+ Adding L2 Cache: 5ns access time, decreases miss rate to 0.5%

### Virtual Memory

+ Basic Concepts

    和之前印象中的 virtual memory 有点区别。似乎不是拿 disk 当 memory 用，据说 virtual memory 有很多所指。

    不管这些。我们接下来讨论的 virtual memory 实际上就是一个介于 CPU 和「实际地址」之间的「中介」。在实际过程中，CPU 只抛出虚拟地址，根据虚拟地址得到实际内存地址再去访问 cache memory (SRAM) / main memory (DRAM) / disk。

    <p><img src="images/cod-100.png" alt="cod-100" width="80%"></p>

+ Fetching Physical Address Method I: Page Table

    一般来说 page table 位于 main memory 中，就像一般的内存那样存储了从 virtual address 到 physical address 的映射。
    
    如果某个数据不在 physical memory 中，则 virtual address 映射到的会是 disk address。此时称发生了 page fault，同时这会导致极大的 miss penalty。

    <p><img src="images/cod-101.png" alt="cod-101" width="70%"></p>

    下图展示了具体如何利用 page table 将 virtual address 映射到 physical address 的方法。前半部分拿进去查表，后半部分 offset 保持不变。当然，这只是 page table hit 的情况。

    <p><img src="images/cod-102.png" alt="cod-102" width="80%"></p>

+ Fetching Physical Address Method II: TLB

    为了加速从 virtual address 找 physical address 这一过程，我们可以添加一个 TLB (Translation-lookaside Buffer) 模块。

    TLB 可以视作一个「关于 page table 的 fully-associative cache」。注意 TLB 只存储映射到 physical memory 的情形。

    <p><img src="images/cod-103.png" alt="cod-103" width="80%"></p>

+ Whole Structure

    考虑 TLB 后，memory data 的获取流程如下（图中未显示 page table 的情形，不过应该比较好脑补）。

    <p><img src="images/cod-104.png" alt="cod-104" width="80%"></p>

    <p><img src="images/cod-105.png" alt="cod-105" width="80%"></p>

+ 理解检测

    <p><img src="images/cod-106.png" alt="cod-106" width="80%"></p>


## Chapter 6 Storage, Networks and Other Peripherals

### Introduction

+ Typical I/O Devices

    <p><img src="images/cod-107.png" alt="cod-107" width="80%"></p>

+ Three Characters of I/O

    1. Behavior
    
        行为。例如是做 input，还是 output，还是 storage。

    2. Partner
        
        对象。交互对象，例如 mouse 的 partner 是人，network 的 partner 是机器。

    3. Data Rate

        I/O device 和 main memory/processor 之间的数据传输速度峰值。

+ I/O Performance Measurement: **Throughput** and **Response Time**

### Disk Storage and Dependability

+ Availability Measurement

    1. MTTF (Mean Time to Failure)

        平均故障时间。即一个部件期望的无故障运行时长。

    2. MTTR (Mean Time to Repair)

        平均修复时间。即一个部件发生故障后期望的修复所需时长。

    3. MTBF (Mean Time Between Failures) = MTTF + MTTR
    4. Availability = MTTF / (MTTF + MTTR)

+ Reliability Measurement

    考虑一个情景：我们能否使用非常多的小 disk 来组成大 disk，进而缩短 disk 和 CPU/Memory 之间的速度差距？

    <p><img src="images/cod-108.png" alt="cod-108" width="60%"></p>

    答案是否定的。因为 (MTTF of N disks) = (MTTF of 1 Disk) / N。如此设计会让 disk 的可靠性大幅下降。

    实际上 reliability 的衡量建立于 availability 的衡量之上：

    1. AFR (annual failure rate) = percentage of devices to fail per year

        = (365 $\times$ 24) hours / MTTF in hours

    2. "nines of availability" per year (中间是 availability，右边是 meaning)

        <p><img src="images/cod-109.png" alt="cod-109" width="60%"></p>


+ Magnetic Disk

    详见 Chapter 5: Introduction: Basic Memory Type 部分。

+ Flash Storage

    详见 Chapter 5: Introduction: Basic Memory Type 部分。

+ RAID

    <p><img src="images/cod-110.png" alt="cod-110" width="60%"></p>

    DBS 部分已有介绍。这边只作简要补充。

    + RAID 0: No Redundancy (Skipped)

    + RAID 1: Disk Mirroring/Shadowing (Skipped)

    + RAID 2: Error Correction Code (Skipped, *UNUSED* now)

    + RAID 3: Bit-Interleaved Parity Disk

        <p><img src="images/cod-113.png" alt="cod-113" width="50%"></p>

        其中 0, 1, 2, ..., 23 是数据的实际顺序。但是每个方框只包含了一个 bit。

    + RAID 4: Block-Interleaved Parity Disk

        <p><img src="images/cod-111.png" alt="cod-111" width="70%"></p>

        其中 0, 1, 2, ..., 23 是数据的实际顺序。每个方框只包含了一个 block。
        
        【写入分析 1】一次 Logical Write 将会涉及到 2 次 Physical Read 和 2 次 Physical Write（如上右图所示）。
        
        【写入分析 2】对比 RAID 3 和 RAID 4 的 small writes（绿色部分示意，假设 8bit）。RAID 3 需要进行 4 次串行的「Main Disk + Parity Disk」的物理读取和写入（无法并行是因为 Parity Disk 在 4 次中都需要被访问）；而 RAID 4 由于 8bit 都位于 block 0 中，所以只需要一次「Main Disk + Parity Disk」的物理读取和写入。

    + RAID 5: Block-Interleaved Distributed Parity Disk

        <p><img src="images/cod-112.png" alt="cod-112" width="80%"></p>

        其中 0, 1, 2, ..., 23 是数据的实际顺序。

        【写入分析 3】这种分布式设计允许更为激进的并行写入。例如：我要 logical write D0 和 D5。对于 RAID 5，这 $2 \times 2$ 个 disks **互不干扰**，可以同时进行；但是对于 RAID 4，$P$ 校验位使用同一个 disk，logical write 必须分两波次进行。

    + RAID 6: P + Q Redundancy

        在 RAID 5 的基础上，使用两个 Redundancy Disk。

    + Comparison

        1. 一般而言，RAID 3 比 RAID 4 更擅长长序列读取，RAID 4 比 RAID 3 更擅长小范围读取；
        2. RAID 3 在 small writes 上具有最低的 throughput；RAID 3. 4. 5 在 large writes 上具有几乎一致的 throughput。

### Buses

#### Buses Basics

+ Buses

    总线是多条线的组合，用于进行各模块之间的数据传输。

    分为 control lines 和 data lines，其中 data lines 可以传输地址和具体数据。

+ Bus Transactions

    1. Output 流程（CPU 指示内存向 devices 中写出数据）

        <p><img src="images/cod-114.png" alt="cod-114" width="80%"></p>

    2. Input 流程（CPU 指示内存从 devices 中读取数据）

        <p><img src="images/cod-115.png" alt="cod-115" width="70%"></p>

#### Asynchronous Data Fetching: Handshaking Protocol

考虑一个情形：某个 I/O Device 希望从 memory 中读取数据。在「同步」和「异步」的情况下，读取方法有所差异。接下来介绍异步读取的「握手协议」：

<p><img src="images/cod-116.png" alt="cod-116" width="90%"></p>

橙色表示 I/O Device 的信号，黑色表示 memory 的信号。Ack 相当于一条辅助信号线，用于告诉对方「我收到了你的请求 / 回应」。

1. When memory sees the ReadReq line, it reads the address from the data 
bus, begin the memory read operation，then raises Ack to tell the device 
that the ReadReq signal has been seen.

2. I/O device sees the Ack line high and releases the ReadReq data lines.

3. Memory sees that ReadReq is low and drops the Ack line.

4. When the memory has the data ready, it places the data on the data lines 
and raises DataRdy.

5. The I/O device sees DataRdy, reads the data from the bus , and signals that 
it has the data by raising ACK. 

6. The memory sees Ack signals, drops DataRdy, and releases the data lines.

7. Finally, the I/O device, seeing DataRdy go low, drops the ACK line, which 
indicates that the transmission is completed.

【例题】

> Assume: **The synchronous bus** has a clock cycle time of 50 ns, and each bus transmission takes 1 clock cycle. **The asynchronous bus** requires 40 ns per handshake. The data portion of both buses is 32 bits wide.
> 
> Question: Find the **bandwidth** for each bus when reading one word from a 200-ns memory.

+ Synchronous Case

    1. Send the address to memory : 50ns 
    2. Read the memory : 200ns
    3. Send the data to the device : 50ns
    
    Thus, the total time is 300 ns. So, the bandwidth = 4bytes/300ns = 13.3MB/s

+ Asynchronous Case

    回顾之前的握手协议图。

    Step 1: 40ns

    Step 2, 3, 4: $\max$(2 $\times$ 40ns + 40ns, 200ns) = 200ns

    Step 5, 6, 7: 3 $\times$ 40ns = 120ns

    所以读取一个 word 的总时间为 $360\text{ns}$。转换成带宽即为 $11.1$ MB/s。

#### Bus Arbitration

当总线被多个 I/O Device 申请使用时，需要有人完成调度。一般承担这个工作的是 processor。

四个常见的调度模式：

+ daisy chain
+ centralized
+ self selection
+ collision detection

#### Bus Bandwidth Computation

+ 例题

    <p><img src="images/cod-117.png" alt="cod-117" width="80%"></p>

    Suppose we have a system with the following characteristic:

    1. A memory and bus system supporting block access of (4~16) 32-bit words.

    2. A 64-bit synchronous bus clocked at 200 MHz, with each 64-bit transfer taking 1 clock cycle, and 1 clock cycle required to send an address to memory.

    3. Two clock cycles needed between each bus transaction.（每次利用总线读取一个 block 视作一个 transaction，每个 transaction 之间需要空 2 个周期）
     
    4. A memory access time for the first four words of 200ns; each additional set of four words can be read in 20 ns. Assume that a bus transfer of the most recently read data and a read of the next four words can be overlapped.

    Q1. Find the **sustained bandwidth** and the latency for a read of 256 words for transfers that use **4-word blocks** and for transfers that use **16-word blocks**. 
    
    Q2. Also compute **effective number of bus transactions** per second for each case.

+ 解答（4-word block case）

    For each block, it takes

    1. **1 CC** to send the address to memory
    2. 200ns/(5ns/cycle) = **40 CC** to read memory
    3. **2 CC** to send the data from the memory
    4. **2 CC** needed between each bus operation.

    This is a total of **45 CC**.
    
    Since there are 256/4 = 64 blocks, the transfer of 256 words takes 45 $\times$ 64 = 2880 CC. 
    
    The latency for the transfer of 256 words is: 2880 cycles $\times$ (5ns/cycle) = 14,400 ns.

    Final answer:

    + Number of bus transactions per second: 
    
        $64 \text{ transactions} \times \frac{1 \text{second}}{14,400 \text{ns}} = 4.44 \text{M transactions/s}$.

        注意，当使用 4-word block 进行传输时，一次 256-word transfer 实际上包含了 64 次 bus transactions。

    + Bandwidth

        $1024 \text{bytes} \times \frac{1 \text{second}}{14,400 \text{ns}} = 71.11 \text{ MB/s}$.

+ 解答 (16-word block case)

    For each block, it takes

    1. **1 CC** to send the address to memory
    2. 260ns/(5ns/cycle) = **52 CC** to read memory
    3. **2 CC** to send the data from the memory **(Overlap Considered)**
    4. **2 CC** needed between each bus operation.

    This is a total of **57 CC**.
    
    Since there are 256/16 = 16 blocks, the transfer of 256 words takes 57 $\times$ 16 = 912 CC. 
    
    The latency for the transfer of 256 words is: 912 cycles $\times$ (5ns/cycle) = 4,560 ns.

    Final answer:

    + Number of bus transactions per second: 
    
        $16 \text{ transactions} \times \frac{1 \text{second}}{4,560 \text{ns}} = 3.51 \text{M transactions/s}$.

    + Bandwidth

        $1024 \text{bytes} \times \frac{1 \text{second}}{4,560 \text{ns}} = 224.56 \text{ MB/s}$.

### Interfacing I/O Devices to the Memory, Processor, and OS

+ I/O Characteristics

    1. (shared) 被多个程序共享
    2. (interrupts) 使用中断进行交互
    3. (complex) 底层控制非常复杂
    
+ I/O Communication Types

    1. 由 OS 发出指令给到 I/O devices（注意不是硬件）
    2. I/O devices 相应指令（无论是否成功完成操作）
    3. 数据传输，必须发生在 I/O devices 和 memory 之中

+ How to Give Commands to I/O Devices?

    【Method 1】Memory-Mapped I/O

    一种巧妙的设计，即每个 I/O 都与某个内存地址建立映射。当需要访问某个 I/O 时，直接用 `lw` 或 `sw` 等指令访问对应的 I/O 即可。

    【Method 2】Special I/O Instructions

    指令集为 I/O 专门设计的指令。例如 `in al, port` 和 `out port, al` 等。

+ Communication with Processor

    1. **Polling（轮寻）**：定期（如每 10ms）去寻访某个 I/O，看其在过去的 10ms 内有无 I/O 请求。若有则执行。

    2. **Interrupt（中断）**：当某个 I/O 发出请求，立刻中断 processor 并赶去执行。

        <p><img src="images/cod-118.png" alt="cod-118" width="80%"></p>

        从上图可以看出，为了 incept data，printer 会抛出 request interrupt。
        
        而每当 printer 抛出一个 request interrupt，都会导致 CPU 产生一个中断（对应 response interrupt），并在这个中断中为 printer 传输数据。

    3. **Direct Memory Access (DMA)**：

        实际上是 Interrupt 下的一个子分支，不过加入了 DMA 的优化。简单理解为，CPU 创建了一个专门处理 I/O 事件的「小跟班」DMA。

        以往 I/O Device 想要和 memory 交互（获取数据），必须要经过 CPU（这是因为 CPU 才能控制 memory）。而这会导致 CPU 被频繁中断，不好。

        优化后，在某一批次的 I/O 之前，CPU 事先创建并初始化一个 DMA，使其代为执行自己职责。这样只有在这一批 I/O 开始和结尾会导致 CPU 产生中断（共计 2 次，少了很多）。

        <p><img src="images/cod-119.png" alt="cod-119" width="60%"></p>

+ Measure the Performance of Polling / Interrupt / DMA

    1. Polling

        + Question:

            <p><img src="images/cod-120.png" alt="cod-120" width="60%"></p>

        + Solution:
        
        <p><img src="images/cod-121.png" alt="cod-121" width="60%"></p>

    2. Interrupt

        + Question: 
        
            Suppose we have the same hard disk and processor we used in the former example, but we used interrupt-driven I/O. The overhead for each transfer, including the interrupt, is 500 clock cycles. Find the fraction of the processor consumed if **the hard disk is only transferring data 5% of the time**.

        + Solution:

            实际上，最后一句话的假设是使得 Interrupt 在绝大多数情况下效率高于 Polling 的根本原因。考虑一个较长的时间段，比如说 1s。在此期间有 0.05s 我们进行 hard-disk I/O。这 0.05s 内我们需要传输 $0.05 \times 4 \text{MB} = 200 \text{KB}$ 数据。一次 data transfer 为 $16 \text{B}$，所以总共会造成 $12,500$ 次中断，损失 $12,500 \times 500 = 6.25 \times 10^6 \text{CC}$。

            一秒钟共 $500 \times 10^6 \text{CC}$，所以损失率为 $1.25\\%$。

    3. DMA

        + Question:

            Suppose we have the same hard disk and processor we used in the former example. 
            
            Assume that the initial setup of a DMA transfer takes 1000 clock cycles for the processor, and assume the handling of the interrupt at DMA completion requires 500 clock cycles for the processor. 
            
            The hard disk has a transfer rate of 4MB/sec and uses DMA. The average transfer from disk is 8 KB.*（这里的意思是，每次 transfer 的数据量大小 8KB。每次 transfer 之间可能相隔较久，所以需要重新初始化 DMA）* Assume the disk is actively transferring 100% of the time. 
            
            Please find what fraction of the processor time is consumed.

        + Solution:

            一次 disk transfer 所需时间为 8KB / (4MB / s) = 2ms。

            由于假设所有时间都在做 disk I/O，所以相当于 1s 内会做 500 批 disk I/O。

            每批 disk I/O 需要重新建立 DMA，同时打断 CPU，这部分总共消耗 1500 CC。

            所以 1s 内会使得 CPU 中断 $7.5 \times 10^5 \text{CC}$。损失率为 $0.2\\%$。