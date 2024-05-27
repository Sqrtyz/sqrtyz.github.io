+++
title = '【笔记】数据库系统 (Part III)'
date = 2024-05-01T07:07:07+01:00
+++

## Lecture 8. Storage and File Structure

### Global Overview



### Overview of Physical Storage Media

感觉这部分和计组重了。

<p><img src="images/dbs-46.png" alt="dbs-46" width="70%"></p>

**Primary storage**: Fastest but volatile

**Secondary storage** (辅助存储器，联机存储器): non-volatile, moderately fast access time

**Tertiary storage** (三级存储器，脱机存储器): non-volatile, slow access time

计算机本身基本上只考虑了上两级。

第三级就类似于磁带、CD-ROM 这种玩意儿，所以叫「脱机存储器」。

### Magnetic Disks

<p><img src="images/dbs-47.png" alt="dbs-47" width="60%"></p>

+ Structure

    + **Platter** 盘子。一个磁盘一般有 4-16 个盘子。

    + **Track** 轨道。对应图上不同半径的环。

    + **Sector** 扇区。每个 track 又被分割为若干 sectors。

    + **Cylinder** 所有 platter 同一半径的 track 集合。

    + **Read-write head** 读写头。

+ Measure Performance of Disks

    + Seek time (寻道时间)
        
        工作：arm 找到正确的 track

    + Rotational latency time (旋转等待时间)
    
        工作：磁盘旋转使得正确的 sector 出现在 head 下

    + Data-transfer time (数据传输时间)

        工作：数据传输。

    + Mean time to failure (MTTF, 平均故障时间)

+ Optimization of Disk-Block Access

    1. Block 读取

        一次读取整块数据（不止是精确索要的数据），方便后续 Memory 快速使用邻近数据。

    2. 磁盘臂调度算法

        【电梯算法】考虑读写头在 inner track 和 outer track 之间移动的情景。读写头应当尽可能减小更改方向的次数。同方向先处理，要掉头的话先把当前方向处理完了再说。


### RAID

+ RAID, RAID 0, RAID 1, 拆分方式

    RAID: Redundant Arrays of Independent Disks (独立磁盘冗余阵列) 

    <p><img src="images/dbs-48.png" alt="dbs-48" width="60%"></p>

    相比一个单独的 disk，RAID 同时具有

    + 更高速度：多硬盘并行。比如 RAID 0 中 4 个硬盘可以并行读取数据。理论读时间变为 1/4。

    + 更高可靠：采取冗余设计。比如 RAID 1 中同样的数据存两份。

    拆分方式：

    + Bit-level striping (比特级拆分)

        拆的非常细。一个 byte 拆成 8 个 bit，分别存到 8 个 disk 中。虽然相比单个 disk 读取速度快了 8 倍，但是寻道实际上是慢了的。

    + Block-level striping (块级拆分)

        拆分单位不再是一个 bit，而是若干个 byte 的集合（统称一个 block）。如果有 $n$ 个硬盘，第 $i$ 块会被存储在第 $(i \bmod n) + 1$ 个硬盘中。

+ 其他类型 RAID

    + RAID 2

        <p><img src="images/dbs-51.png" alt="dbs-51" width="50%"></p>

        采用比特级拆分。Disk 4/5/6 存储的是 Hamming Code 校验码，并非单纯的数据备份。

    + RAID 3

        <p><img src="images/dbs-52.png" alt="dbs-52" width="40%"></p>

        采用 byte 级拆分。最后一个 disk 存储的是奇偶校验码（异或）。如果一个 disk 里面的数据爆了，直接把剩下的几个对应位置异或即可恢复。

    + RAID 4

        <p><img src="images/dbs-53.png" alt="dbs-53" width="40%"></p>

        采用 block 级拆分。最后一个 disk 存储的是奇偶校验码（异或）。如果一个 disk 里面的数据爆了，直接把剩下的几个对应位置异或即可恢复。

        【RAID 3 vs. RAID 4】RAID 3 更擅长长序列读取，RAID 4 更擅长随机的小范围读取。


    + RAID 5

        <p><img src="images/dbs-54.png" alt="dbs-54" width="40%"></p>

        采用 block 级拆分。和 RAID 4 的差异在于奇偶校验码采取分布式存储。

    + RAID 6

        <p><img src="images/dbs-55.png" alt="dbs-55" width="40%"></p>

        和 RAID 5 类似：block 级拆分、奇偶校验码分布存储。唯一的区别在于使用更多的空间来存储奇偶位，通过更冗余的设计保证更高的可靠性。

    RAID 1 和 RAID 5 是目前最为常用的。


### Storage Access

【知识迁移】类比计组同时期学习的 memory-cache 调度策略，数据库这里讲的是 disk-memory 调度策略。很多方面是类似的，但也有细微的不同。

+ Buffer Concepts

    <p><img src="images/dbs-49.png" alt="dbs-49" width="60%"></p>

    <p><img src="images/dbs-50.png" alt="dbs-50" width="60%"></p>

    一些定义：
    
    + block: 一块连续的硬盘空间
    
    + page: 一个 block 在 buffer 中的体现

    + frame: 一单位的 buffer pool

+ Buffer Management

    从 buffer 中读取信息。如果发现 block 已在，则直接读取；反之则需要将数据从硬盘转移到 buffer 中。

    若 buffer 中没有空间存储这个新的 page，则需要进行覆盖。如果被覆盖的旧块被修改过，则需要先写回硬盘再覆盖；反之则可以直接覆盖。

    关于覆盖：

    + Buffer Replacement Strategies

        LRU：覆盖最近一段时间用的最少的块。在某些情况下可能不优，比如 natural join。

        <p><img src="images/dbs-58.png" alt="dbs-58" width="30%"></p>

        MRU：覆盖最近一段时间用的最多的块。看起来比较扯淡，但是适合用于一个 block 短期内只用一次的情景。

    + 辅助策略 | Pinned Block

        如果一个 block 正在被某个事务使用，则将其钉住 (pinned)，不允许删除。

    + 辅助策略 | Toss-immediate

        用后立即丢弃：某些 block 我们用完之后直接从 buffer 中删除。

### Record & File Organization

首先是 record 的存储方式。我们讨论定长和不定长两种情况。

+ Fixed-length Records

    所有数据在硬盘里都是固定长度的。在这种存储方式下 access 会更为便捷。

    一个比较大的问题是删除。我们不大可能删除了一行之后把所有后面的行向前平移 1 个单位。一个解决方案是 free list:

    <p><img src="images/dbs-61.png" alt="dbs-61" width="60%"></p>

    这样重新利用被删除的行就会比较方便。

+ Variable-Length Records

    采取可变长度方式来存储 records 时，我们将一个 record 分为两部分：前面的 fixed-length info 部分和后面的 variable-length info 部分。如下图所示。

    对于一个长度固定的属性（例如 int, char, date），它的实际值直接存储在 record 的前半部分；对于一个长度不固定的属性（例如 string），他的实际值存储在 record 的后半部分，但我们在前半部分建立了长度为 4 byte 的 (offset, length) 数值对来对其进行定位。两部分之间用一个 Null bitmap 分隔。

    <p><img src="images/dbs-63.png" alt="dbs-63" width="60%"></p>

    当然这只是一个 record 的存储。考虑表格中的多个 records，由于各个 records 长度不尽相同，我们采取下图所示的方法存储。即前面存 (size, pointer)，后面存实际数据，两头向中间靠拢地存储。

    <p><img src="images/dbs-62.png" alt="dbs-62" width="60%"></p>

接下来考虑一个更大的角度，即整个文件（或者说 records 之间）是如何存储的。

+ Sequential File Organization

    字面意思的顺序存储……

    <p><img src="images/dbs-64.png" alt="dbs-64" width="40%"></p>

+ Multi-table Clustering File Organization

    像这样把左边的两个表聚合成右边的一个表。这样做的好处在于大大简化了 natural join 时的工作量。

    <p><img src="images/dbs-65.png" alt="dbs-65" width="80%"></p>

    但是如果我要访问一个单表，可能会有点麻烦。例如，如果要访问 department 表格，开销比原来的表格要大。一个解决方案是搞个指针：

    <p><img src="images/dbs-66.png" alt="dbs-66" width="80%"></p>

+ B+ / Hashing File Organization

    具体参照 Lecture 9。


### Column-Oriented Storage

按列存储。

<p><img src="images/dbs-67.png" alt="dbs-67" width="60%"></p>

Benefits:

+ Reduced IO if only some attributes are accessed
+ Improved CPU cache performance 
+ Vector processing on modern CPU architectures

Drawbacks:
+ Cost of tuple reconstruction
+ Cost of tuple deletion and update

甚至可以行列混合存储：

<p><img src="images/dbs-68.png" alt="dbs-68" width="30%"></p>

Stripe $i$ 相当于对行进行划分，而每个 stripe 内部其实又是按列存储的。


## Lecture 9. Indexing and Hashing

### Indexing Intro

+ Index 基本结构：search-key + pointer

    前者是索引的键值，后者指向数据库中的某个存储空间。

+ Index 基本分类

    + 顺序索引 (ordered indices): 索引中 search keys 按某种顺序排列好。

    + 哈希索引 (hash indices): 索引中 search keys 遍布于各个 hash buckets 中，查询时通过某个哈希函数定位到具体的 bucket。

### Ordered Indices

+ Basic Concepts

    1. sequentially ordered file (顺序排序文件)：

        这个概念是相对于某个 search key 而言的。如果整张表格对于某个 search key 是顺序排列的，则称其为相应的顺序排序文件。

    2. primary index (主索引):

        又称 clustering index。与对应的数据文件本身的排列顺序相同的索引称为主索引。显然，此时整个文件是相对于 primary index 的顺序排序文件。

        主索引的搜索键通常是，**但不一定是** primary key。

    3. secondary index (辅助索引):

        一个表格当然可以有多个索引。那些不是主索引的索引就称为辅助索引。

        注意：
        
        + 辅助索引不能使用稀疏索引，这意味着每条记录都必须有指针指向。不然这个索引显然会失去意义。

        + 如果一个索引存在重复项，则需要使用 bucket 结构。

          <p><img src="images/dbs-69.png" alt="dbs-69" width="70%"></p>

    4. dense index (稠密索引):

        <p><img src="images/dbs-56.png" alt="dbs-56" width="60%"></p>

       稠密索引：对于表格中相关属性的每个键值，都在索引中有所出现。

    5. sparse index (稀疏索引):

        <p><img src="images/dbs-57.png" alt="dbs-57" width="60%"></p>

        稀疏索引：对于表格中相关属性的每个键值，不一定索引中有所出现。

        如果目前有一个稀疏索引，想要找到对应的 tuple，则可以先根据稀疏索引找到一个大致的位置，然后往后一点一点搜索。

        注意：

        + 为了提高效率，设计时一般 sparse index 中一个值就指向一整个 block。

        + Sparse index 只能用于对应的顺序排序文件（不然没法找）；当然 dense index 则可以用于顺序和非顺序文件。

    6. multi-level index (多级索引):

        <p><img src="images/dbs-70.png" alt="dbs-70" width="40%"></p>

+ Index Modification

    + Deletion

        考虑删除某条 record $R$ 并维护索引 $I$。
        
        如果 $I$ 是稠密索引：假设 $R$ 对应索引中的 $I_k$ 项。

        1. 假设 $I_k$ 只指向 $R$ 一个 record，删除 $I_k$ 及 $I_k \to R$。

        2. 假设 $I_k$ 指向包含 $R$ 在内的多个 record，且 $I$ 是辅助索引，则删除 $I_k \to R$ 这一指针。

        3. 假设 $I_k$ 指向包含 $R$ 在内的多个 record，且 $I$ 是主索引（此时未采取 bucket 处理重复！毕竟是主索引所以不怎么需要。），则不需要执行任何索引删除操作。除非 $R$ 对应了 $I_k$ 指向的第一个 record，此时 $I_k$ 需要指向下一个 record。

        如果 $I$ 是稀疏索引（当然只能是主索引）：

        1. 假设 $R$ 并未对应索引中的任何项，则直接删掉对应 record 后无事发生。

        2. 假设 $R$ 对应到了索引中的 $I_k$ 项，则把 $I_k$ 指向相邻的下一条 record。特别地，如果下一条 record 已经对应了索引中的 $I_{k+1}$ 项，则直接删除 $I_k \to R$ 的指针。

    + Insertion

        类似推一下？注意由于 index 是存在硬盘中的「block 单位」中的，有的时候一个 block 存不下当前的所有 index，就会开一个新的 block。此时称这个新声明的 block 为 overflow。随着时间的推移，可能会有一些 block 碎片产生（类似于磁盘碎片），所以需要定期整理这些存储 index 的 block。


### B+ Indexing Tree

<p><img src="images/dbs-71.png" alt="dbs-71" width="80%"></p>

熟练掌握 B+ 树的插入和删除！具体算法略去。

注意，对于一个 $n$ 阶的 B+ 树，非叶节点儿子数目应在 $\lceil\frac{n}{2}\rceil \sim n$ 之间；叶子所存放的值数量应在 $\lceil\frac{n-1}{2}\rceil \sim n-1$ 之间（此部分与 Wikipedia 定义一致）。

通过归纳可知，B+ 树的叶子节点一定深度相等。

为了提高效率，设计时一般让一个 B+ 树节点就对应一个 disk block（一般 4KB）。这种假设下一般 $n \approx 100$。

### Hashing Indices

+ Static Hashing

    <p><img src="images/dbs-59.png" alt="dbs-59" width="70%"></p>

    使用 bucket 来应对哈希冲突。

    偶尔，一个 bucket 可能还不够用（如上图 bucket 3）。bucket overflow 的时候则需要增加新的 bucket。
    
### Write-optimized Indices: LSM Tree

<p><img src="images/dbs-60.png" alt="dbs-60" width="70%"></p>

参见 [知乎文章](https://zhuanlan.zhihu.com/p/415799237) 动态演示。基本思路是：

+ update 主要在内存里面捣腾，同时使用类似于 lazy tag 的机制来避免硬盘操作，内存修改积累到一定程度后，再写回到硬盘。

+ query 的话则比较暴力，先在内存里面找，找不到再去硬盘里面找（所以 LSM 适用于多写少查的场景）。

### Multiple-Key Access

考虑这样一个情景，我们希望根据多个 attributes 进行选择：

```sql
select account-number
from account
where branch-name = "Perryridge" and balance = 100
```

+ Using Indices on Single Attributes

    如果使用的仍然是基于单属性的索引，有两种思路完成多属性联合查询：
    
    1. 根据其中的一个属性索引查，然后一个个测试是否满足另外一个属性

    2. 使用两个属性索引查，查完之后存下来，最后求交

+ Using Indices on Multiple Attributes

    当然也有可能使用基于多属性的索引，在上面的例子中，即为 `(branch-name, balance)`。

    在查询 `branch-name = "Perryridge" and balance < 100` 时，这个索引依然会保持较高效率。

    但在查询 `branch-name < "Perryridge" and balance = 100` 时，这个索引的查询效率则会较低（更多的索引查找次数、由于调用 block 可能会访问更多的无关项）。

+ Bitmap Indices

    使用 bitmap 存储索引，类似于 one-hot 码：

    <p><img src="images/dbs-72.png" alt="dbs-72" width="70%"></p>

    适用于 domain 较小的情况。查询时位运算即可。


## Lecture 10. Query Processing


### Overview

+ Basic Steps of Querying

    <p><img src="images/dbs-73.png" alt="dbs-73" width="80%"></p>

    Execution plan 定义了「具体的查询方式、算法」，比如：

    <p><img src="images/dbs-74.png" alt="dbs-74" width="70%"></p>

    Optimizer 则意在优化某些查询操作。下图的查询中显然右侧更优。 

    <p><img src="images/dbs-75.png" alt="dbs-75" width="70%"></p>

+ Query Efficiency Measurement

    为了便于分析，接下来只考虑两部分时间：

    + $t_T$ - time to transfer one block
    + $t_S$ - time for one seek

    如果进行了 $b$ 次 block transfer 和 $S$ 次 seek，总时间花费为 $bt_T+St_S$。

### Selection Operation

#### Basic Algorithms without Index

+ A1. Linear Search

    直接线性扫描所有 block，依次测试每个 tuple 看是否符合条件。

    时间开销估计为 $b_r$ 次 block transfer（$b_r$ 为对应表格的总 block 数目）加上 $1$ 次 seek。
    
    特别地，如果 selection 是基于某个 key attribute 进行的，时间开销估计变为 $b_r / 2$ 次 block transfer 加上 $1$ 次 seek。

+ A2. Binary Search

    假设 selection 是等号比较，并且选择的 attribute 是有序的。

    时间开销估计为 $\lceil \log_2(b_r) \rceil$ 次 block transfer 和 $\lceil \log_2(b_r) \rceil$ 次 seek。

    前面是考虑 attribute 是 super key 的情况。如果不是 super key 意味着可能有多条 tuple 符合条件，则 block transfer 次数变为 $\lceil \log_2(b_r) \rceil + \lceil \frac{sc(A,r)}{f_r} \rceil - 1$（其中 $sc(A,r)$ 表示满足选择条件的记录数，$f_r$ 表示每个 block 的记录数，下同）。

#### Selections Using Indices and Equality

所有的索引考虑 B+ 树 **稠密索引**。如果 selection 是基于属性 A 的，那么使用的索引也一定是 A 对应的索引。

+ A3. Index Scan: Primary Index, Equality on Key

    Selection 是基于 **具有主索引的属性**，同时这个属性是一个 super key。

    $\text{Cost} = (h_i+1)(t_T+t_S)$，其中 $h_i$ 表示索引树高。

+ A4. Index Scan: Primary Index, Equality on Non-Key

    Selection 是基于 **具有主索引的属性**，但这个属性不是 super key。下图展示了这一情形。

    $\text{Cost} = h_i(t_T+t_S) +t_S +bt_T$，其中 $h_i$ 表示索引树高，$b=\lceil \frac{sc(A,r)}{f_r}\rceil$ 表示抽取出来的 block 数目。

    <p><img src="images/dbs-76.png" alt="dbs-76" width="70%"></p>

+ A5-1. Index Scan: Secondary Index, Equality on Key

    Selection 是基于 **具有辅助索引的属性**，同时这个属性是一个 super key。

    $\text{Cost} = (h_i+1)(t_T+t_S)$，其中 $h_i$ 表示索引树高。

+ A5-2. Index Scan: Secondary Index, Equality on Non-Key

    Selection 是基于 **具有辅助索引的属性**，同时这个属性不是 super key。

    $\text{Cost} = (h_i+n)(t_T+t_S)$，其中 $h_i$ 表示索引树高，$n$ 为符合条件的 tuple 数目。这种情况要用 bucket。

#### Selection Involving Comparisons

+ A6. Primary Index, Comparison

    基于主索引的比较，分为两种情况：

    1. $\sigma_{A \ge v}(r)$，使用 index 找到第一个满足 $A \ge v$ 的 tuple，然后线性扫描到末尾。
    2. $\sigma_{A \le v}(r)$，直接线性扫描，直到不满足条件。

+ A7. Secondary index, Comparison

    基于辅助索引的比较，分为两种情况（回顾 B+ 树索引的结构！）：

    1. $\sigma_{A \ge v}(r)$，使用 index 找到第一个满足 $A \ge v$ 的 tuple，然后 **在 index leaves 上** 线性扫描到末尾。
    2. $\sigma_{A \le v}(r)$，直接在 **在 index leaves 上** 线性扫描，直到不满足条件。

#### Complex Selections

+ A8. Conjunctive Selection Using Single Index

    当索引都是单值索引时，采取策略：第一步从 $n$ 个条件 $\theta_1, \cdots, \theta_n$ 中选择代价最小的 $\theta_i$ 先执行，结果放入内存。第二步对这些元组施行其他条件选择（此时采取线性遍历，因为子表不再具有索引）。

+ A9. Conjunctive Selection Using Composite Index

    如果索引有复合索引，有可能可以一遍搞定。

+ A10-1. Conjunctive Selection by Intersection of Identifiers

    对每个条件 $\theta_i$ 采用对应的索引找出符合要求的 tuples。最后对每个 $\theta_i$ 选出的 tuples 取交。
    
    如果某些 $\theta_i$ 不具有对应索引，则先对那些有索引的 $\theta_i$ 找 tuples，取交；而后对取交结果逐个测试「没有索引的 $\theta_i$」看是否符合条件。类似于 A8。

+ A10-2. Disjunctive Selection by Union of Identifiers

    对每个条件 $\theta_i$ 采用对应的索引找出符合要求的 tuples。最后对每个 $\theta_i$ 选出的 tuples 取并。

    **注意只能在每个 $\theta_i$ 都具有索引时采用！** 只要有一个 $\theta_i$ 不具有索引，显然直接线性扫描全表最优。


### External Sort-Merge

介绍一种 DBS 中常用的外部排序算法：外部归并排序。假设内存大小为 $m$ 个单位。以 $m=3$ 为例（图片来源 [CSDN](https://blog.csdn.net/qq_29611575/article/details/103587798)）：

1. $m$ 个元素一组，执行任意 $n^2$ 排序算法：

    <p><img src="images/dbs-77.png" alt="dbs-77" width="70%"></p>

2. $m-1$ 个组归并为一组：

    <p><img src="images/dbs-78.png" alt="dbs-78" width="70%"></p>

    $m$ 单位大小的内存，其中 $m-1$ 个用于存储 $m-1$ 个组的待排序项，$1$ 个存比较结果。注意结果存储到 disk 的新开辟空间中。

3. 重复步骤 2。

+ 总流程

    <p><img src="images/dbs-79.png" alt="dbs-79" width="70%"></p>

+ 时间开销分析

    1. 总共产生的 merge pass 为 $\lceil \log_{m-1} (b_r / m)\rceil$。其中 $b_r$ 是 relation $r$ 所占用的 block 总数目，$m$ 是 memory 中能容纳下的 block 数目。

    2. 首先是 data transfer 部分。Pass 0，即单元素组内排序，需要的开销为 $2b_r$。Pass 1 & 2，即组间归并排序，每一轮归并需要的开销也为 $2b_r$。

        特别地，我们 **忽略最后一次写回 disk 的开销**，这是因为最后一步往往不需要写回 disk，而是直接反馈给父进程。这样一来，总的 data transfer 开销为

        $$\left (2\lceil \log_{m-1} (b_r / m)\rceil+1 \right)b_r$$

    3. 然后是 seek 部分。Pass 0，即单元素组内排序，需要的开销为 $2\lceil b_r / m \rceil$。对应上图的 R1, R2, R3, R4 各需要进行一次寻道（看起来左边只需要 seek 一次，但注意磁头只有一个）。

        Pass 1 & 2，即组间归并排序，每一轮归并需要的开销为 $2\lceil b_r / b_b \rceil$。注意其中 $b_b$ 表示一次性转移的 block 大小 (如下图所示)，它的存在是为了减少 seek 的开销，但同时实际上也增加了归并的轮数。

        <p><img src="images/dbs-83.png" alt="dbs-83" width="40%"></p>
        
        依然是忽略最后一次写回 disk 的开销，这样一来，总的 seek 开销是

        $$2\lceil b_r / m \rceil+ (2\lceil \log_{\lfloor m / b_b\rfloor - 1} (b_r / m)\rceil-1 )  \lceil b_r / b_b \rceil$$

        有的时候我们认为 $b_b = 1$（不对 seek 进行优化），则总 seek 开销为

        $$2\lceil b_r / m \rceil+ (2\lceil \log_{m-1} (b_r / m)\rceil-1 ) b_r$$


### Join Operations

注意，和 sorting 部分一样，时间开销都忽略了最后一次 write 的开销！

1. Nested-Loop Join | 简单直接

    <p><img src="images/dbs-80.png" alt="dbs-80" width="70%"></p>

    时间开销分析：

    + 最坏情况：buffer 只能存下一个 block (per relation)

        对于每个内循环，都需要进行 $1$ 次 seek 和 $b_s$ 次 transfer。这是显然的。
        
        对于外循环，需要进行 $b_r$ 次 seek 和 $b_r$ 次 transfer。(为什么也要 $b_r$ 次 seek？即使 $r$ 是线性存储的，但磁头只有一个。中间磁头被内循环打断去找 $s$ 了，回来需要重新找 $r$ 里面的 block)。

        总和：$n_rb_s+b_r$ 次 block transfer 和 $n_r+b_r$ 次 seek。
    
    + 最优情况：更小的那个 relation 恰好可以存在一个 block 里面

        总共只需要 $b_r+b_s$ 次 block transfer 和 $2$ 次 seek。

2. Block Nested-Loop Join | 按块比较

    <p><img src="images/dbs-81.png" alt="dbs-81" width="70%"></p>

    时间开销分析：

    + 最坏情况：buffer 只能存下一个 block (per relation)

        一样，对于每个内循环，都需要进行 $1$ 次 seek 和 $b_s$ 次 transfer。

        对于外循环，仍然是 $b_r$ 次 seek 和 $b_r$ 次 transfer。

        不一样的是，内循环只执行 $b_r$ 次。总和为 $b_rb_s+b_r$ 次 block transfer 和 $2b_r$ 次 seek。

    + 最优情况：更小的那个 relation 恰好可以存在一个 block 里面

        总共只需要 $b_r+b_s$ 次 block transfer 和 $2$ 次 seek。

    + 中间情况：具有 $M$ 个 buffer。安排其中 $M-2$ 个存外关系 $r$，$1$ 个存内关系 $s$，$1$ 个存输出。

        总共需要 $\lceil b_r / (M-2)\rceil \cdot b_s+b_r$ 次 transfer 和 $2\lceil b_r / (M-2)\rceil$ 次 seek。这个式子的得来其实就是把 $r$ 里面的 $M-2$ 个块合成一个来看。

3. Indexed Nested-Loop Join | 考虑索引

    还是考虑外表 $r$ 和内表 $s$ 作自然连接。但是 $s$ 具有一个索引。

    策略：For each tuple $t_r$ in the outer relation $r$, use the index to look up tuples in $s$ that satisfy the join condition with tuple $t_r$.

    时间开销分析：

    + 最坏情况：buffer 只给外表 $r$ 留有一个 block 的空间

        总时间开销：$b_rt_T+b_rt_S+n_rc$。其中 $c$ 表示查一个 disk 中的索引所需时间。

4. Sort-Merge Join | 排序归并连接

    <p><img src="images/dbs-82.png" alt="dbs-82" width="50%"></p>

    使用类似于归并排序的思路进行连接。双指针技巧。

    + 时间开销分析：

        假设每个表各有一个容量为 $b_b$ blocks 的 buffer 用于转移。若两个表关于交集项都是有序的，则需要 $b_r+b_s$ 次 block transfer 和 $\lceil b_r/b_b \rceil+\lceil b_s/b_b \rceil$ 次 seek。

        若两个表初始无序，则需要加上排序代价。

    + Hybrid Merge Join (混合归并连接)

        考虑自然连接一个有序表格 $A$ 和一个无序表格 $B$，但是无序表格 $B$ 对于连接属性 $attrs$ 建立了辅助索引。一种思路是对 $A$ 和 $B$ 的 B+ 树索引叶子进行归并排序连接。但是由于索引的叶子只存储实际 tuple 的地址，不存储 tuple 的值，实时地再去 access tuples 会耗费额外的 disk seek time。

        所以考虑第一步只连接「$A$ 的实际 tuples」和「$B$ 索引叶子上的地址」；第二步对连接结果按「$B$ 索引叶子上的地址」进行排序；第三步把连接结果中「$B$ 索引叶子上的地址」替换为「$B$ 中的实际 tuples」。这样一来只有第三步会额外花费一次 seek。

5. Hash Join | 哈希连接

    <p><img src="images/dbs-84.png" alt="dbs-84" width="60%"></p>

    基本思路是，将交集属性 $JoinAttrs$ 通过哈希函数 $h$ 映射到 $\\{0,1,2,\cdots,n\\}$。然后只需要在哈希值相等的组之间枚举比较。

    + 具体操作

        1. (partitioning) 用哈希函数 $h$ 划分 $s,r$。

        2. (build & probe) 对于每个 $i$，将 $s_i$ 整体读入内存并建立 in-memory hash index。注意这个 hash 函数应该与之前的 $h$ 有所不同。

            从 disk 一个一个读入 $r_i$ 的 tuple。对于某个 tuple $t_r \in r_i$，在刚刚建立的 in-memory hash index 中找到匹配的 tuple $t_s \in s_i$。连接它们。

    + $n$ 的选择

        如上所述，我们需要把一个 $s_i$ 整体读入内存。这说明我们希望内存大小能容纳下同一个哈希值的全部 block。

        $n$ 一般选择为 $\lceil b_s / m\rceil \cdot f$，其中系数 $f \approx 1.2$。这样一来每个 partition 的大小就比较合适。

    + 时间开销分析（假设不发生 hash-table overflow，且 non-recursive partitioning）

        + $3(b_r+b_s)+4n$ 次 block transfer

            最开始 partitioning 贡献 $2(b_r+b_s)$ 次。

            而后 build & probe 会对每一组 partition 进行遍历，贡献 $(b_r+b_s)$ 次 block transfer。
            
            但注意每一组 partition 不一定刚好和 blocks 完全 fit，最坏情况单关系的每一个 partition 都额外贡献 $2$ 次 (partitioning write & build-probe read) block 读取。这部分最坏情况的总额外贡献为 $4n$。

            仍然忽略了最后一步的 write 开销。
        
        + $2(\lceil b_r/b_b\rceil + \lceil b_s/b_b \rceil)+2n$ 次 seek
        
            最开始 partitioning 贡献 $2(\lceil b_r/b_b\rceil + \lceil b_s/b_b \rceil)$ 次，其中 $b_b$ 是 input/output buffer 所能容纳的 block 数目。

            而后 build & probe 会对每一组 partition 进行线性遍历，贡献 $2n$ 次 seek。

### Evaluation of Expressions

如果考察多个 operation 的组合，例如

$$\Pi_{customer-name} (( \sigma_{balance<2500}(account)) \bowtie depositor)$$

我们通常有两种实现复合操作的方法：

1. 实体化（Materialization）：不同操作泾渭分明，单元操作完成之后写回 disk，然后读出来继续下一次操作。

2. 流水线（Pipelining）：某一操作的结果直接传给上游操作，甚至当这一操作还没完全完成时就可以进行。

回顾开始的这张图，

<p><img src="images/dbs-74.png" alt="dbs-74" width="70%"></p>

Merge join 这里就可以使用 pipeline，但 hash join 则无法使用。