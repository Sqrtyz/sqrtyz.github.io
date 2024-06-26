---
title: 【笔记】信息理论 (Part II)
category: Notes
tag: Unfinished
date: 2024-05-01
---

## Chapter 3 无损信息传输

+ 回顾信息传输模型

  <p><img src="images/it-23.png" alt="it-23" width="70%"></p>

+ 信道的抽象数学模型

  <p><img src="images/it-37.png" alt="it-37" width="70%"></p>

  其中 $W$ 可视为原始信息编码后的结果，$x^n$ 进入信道，$y^n$ 是接收方从信道中获得的信号，$\hat{W}$ 是接收方对信道信号的解读，它同时是对 $W$ 的估计。

### Lec 6 信道容量

信道容量定义为输出序列对不同输入序列所提供的最大互信息，即

$$C = \lim\limits_{n \to \infty} \frac{1}{n} \max\limits_{P(X^n)} \left[I(X_1X_2\cdots X_N;Y_1Y_2\cdots Y_N) \right]$$

注意 $\max$ 这里枚举的是不同的输入概率分布。

#### 6.1 DMC Intro

对于离散无记忆信道 (DMC, 定义见上)，$I(X_1X_2\cdots X_N;Y_1Y_2\cdots Y_N) \le \sum_{n=1}^N I(X_n;Y_n)$

其中等号在输入为独立随机序列时达到。证明如下

<p><img src="images/it-38.png" alt="it-38" width="60%"></p>

（注意这些仅仅对 DMC 成立。特别地，当输入独立随机时，必有输出也独立随机，进而有 $H(Y)=\sum\limits_{i=1}^n H(Y_i)$ 的等号成立）


因此，当输入独立随机时，有

$$C = \lim\limits_{n \to \infty} \frac{1}{n} \max\limits_{P(x^n)} \left[I(X_1X_2\cdots X_N;Y_1Y_2\cdots Y_N) \right] = \max\limits_{P(X)} I(X;Y)$$

这将大大简化接下来的讨论难度。


#### 6.2 DMC Examples


+ 无噪信道

  <p><img src="images/it-39.png" alt="it-39" width="80%"></p>

+ 无损信道

  <p><img src="images/it-40.png" alt="it-40" width="80%"></p>

+ 确定信道

  <p><img src="images/it-41.png" alt="it-41" width="80%"></p>

+ 无用信道

  <p><img src="images/it-42.png" alt="it-42" width="80%"></p>

+ 复制信道

  <p><img src="images/it-43.png" alt="it-43" width="80%"></p>

+ 二元对称信道 BSC

  <p><img src="images/it-44.png" alt="it-44" width="80%"></p>

+ 二元除删信道 BEC

  <p><img src="images/it-45.png" alt="it-45" width="80%"></p>

#### 6.3 信道的组合

+ 平行信道（积信道）

  <p><img src="images/it-46.png" alt="it-46" width="80%"></p>

+ 开关信道（和信道）

  <p><img src="images/it-47.png" alt="it-47" width="80%"></p>

  证明似乎有些复杂。

+ 级联信道

  <p><img src="images/it-48.png" alt="it-48" width="80%"></p>

+ 实例：两个对称信道的组合

  假设有两个对称信道

  <p><img src="images/it-49.png" alt="it-49" width="60%"></p>

  1. 积组合

      <p><img src="images/it-50.png" alt="it-50" width="80%"></p>

  2. 和组合

      <p><img src="images/it-51.png" alt="it-51" width="80%"></p>

  3. 级联组合

      <p><img src="images/it-52.png" alt="it-52" width="70%"></p>


### Lec 7 离散无记忆信道的容量定理

回顾之前所提及的，我们要求出某个信道的「容量」，即求 $C = \max\limits_{\\{Q_k\\}} I(X;Y)$。

这里 $\\{Q_k\\}$ 描述的是概率空间。约束条件是 $Q_k \ge 0, \\ \sum\limits_{k=0}^{K-1} Q_k=1$。

#### 7.1 凸优化

+ 函数为凸的条件：Hessian 矩阵半正定

  $$H(x)=\left(\begin{array}{cccc}
  \frac{\partial^2 f(x)}{\partial x_1{ }^2} & \frac{\partial^2 f(x)}{\partial x_1 \partial_2} & \cdots & \frac{\partial^2 f(x)}{\partial x_1 \partial x_K} \newline
  \frac{\partial^2 f(x)}{\partial x_2 \partial x_1} & \frac{\partial^2 f(x)}{\partial x_2{ }^2} & \cdots & \frac{\partial^2 f(x)}{\partial x_2 \partial x_K} \newline
  \vdots & \vdots & \ddots & \vdots \newline
  \frac{\partial^2 f(x)}{\partial x_K \partial x_1} & \frac{\partial^2 f(x)}{\partial x_K \partial x_2} & \cdots & \frac{\partial^2 f(x)}{\partial x_K{ }^2}
  \end{array}\right)$$

+ KKT 条件：取得最值的通用必要条件

  可以视作拉格朗日乘数法的拓展。拉格朗日乘数法解决的优化问题只有等号约束，而 KKT 条件加上了不等号约束。对于优化问题

  Minimize $f(x)$
  
  Subject to: $g_i(x) \leq 0, h_j(x)=0$

  则取得最值的必要条件是

  $$\begin{aligned}
  & \nabla f\left( x^* \right)+\sum_{i=1}^m \mu_i \nabla g_i\left( x^* \right)+\sum_{j=1}^l \lambda_j \nabla h_j\left( x^* \right)=0 \newline
  & h_j\left( x^* \right)=0, \text { for all } j=1, \ldots, l \newline
  & g_i\left( x^* \right) \leq 0, \text { for all } i=1, \ldots, m \newline
  & \mu_i \geq 0, \text { for all } i=1, \ldots, m \newline
  & \mu_i g_i\left( x^* \right)=0, \text { for all } i=1, \ldots, m
  \end{aligned}$$

  前两条是拉格朗日乘数法也有的，后三条由不等式约束产生。

+ 概率空间情形的凸优化

  可以根据 KKT 条件，也可以通过数学直觉推断出概率空间情形的凸优化解条件。

  1. 非负空间的最值

      $$f'(x)=0, \forall x_k > 0$$
      $$f'(x)<0, \forall x_k = 0$$

  2. 概率空间的最值

      $$f'(x)=\lambda, \forall x_k > 0$$
      $$f'(x)<\lambda, \forall x_k = 0$$

      其中 $\lambda$ 是拉格朗日乘数，其值由约束 $h(x)=\sum\limits_k x_k = 1$ 产生。

#### 7.2 离散无记忆信道的容量定理

定理：概率分布 $\\{Q_0,Q_1,\cdots,Q_{K-1}\\}$ 达到概率转移矩阵 $P$ 的离散无记忆信道容量 $C$ 的充要条件为（等价于）：

$$I(X=k;Y)=C, \forall Q_k > 0$$
$$I(X=k;Y)<C, \forall Q_k = 0$$

其中 $I(X=k;Y)$ 表示通过信道传送字符 $X = k$ 时，信道的输入与输出之间可获得的互信息的期望值。即

$$I(X=k ; Y)=\sum_{j=0}^{J-1} p(j \mid k) \left[I(y_j) - I(y_j \mid x_k) \right] = \sum_{j=0}^{J-1} p(j \mid k) \log \frac{p(j \mid k)}{p(j)}$$

不难发现，$I(X;Y)=\sum\limits_k Q_k I(X=k ; Y)$。

回到原定理。我们其实是要在一个概率空间中找到 $I(X;Y)$ 的最值。考虑拉格朗日乘数法：

$$
\begin{gathered}
J\left(\left\\{Q_k\right\\}\right)=I(X ; Y)-\lambda\left(\sum_{k=0}^{K-1} Q_k\right)=\sum_{k=0}^{K-1} Q_k I(X=k ; Y)-\lambda\left(\sum_{k=0}^{K-1} Q_k - 1\right) \newline \newline
\frac{\partial J\left(\left\\{Q_k\right\\}\right)}{\partial Q_k}\left\\{\begin{array}{l}
=0, \forall Q_k>0 \newline
<0, \forall Q_k=0
\end{array}\right.
\end{gathered}
$$

对 $Q_k$ 求导，得（可以证明第二项的值为 $1$，详见课件）

$$\begin{gathered}
\frac{\partial J\left(\left\\{Q_k\right\\}\right)}{\partial Q_k}=I(X=k ; Y)+\sum_{l=0}^{K-1} Q_l \frac{\partial I(X=l ; Y)}{\partial Q_k}-\lambda \newline
=I(X=k ; Y)-\lambda + 1 \end{gathered}
$$

如果我们令 $C = \lambda - 1$，就可以得到定理内容：

$$\begin{gathered}
I(X=k ; Y)=C, \forall Q_k>0 \newline
I(X=k ; Y)<C, \forall Q_k=0 \end{gathered}
$$

+ 定理的理解

  取到离散无记忆信道的容量的充要条件是对于好的 $I(X = k; Y)$，其值均相等，对于不好的 $I(X = k; Y)$，其值均为 $0$。在这种条件下，就有

  $$C = I(X;Y) = \sum\limits_k Q_k I(X=k ; Y)$$


#### 7.3 SP Case - 对称离散无记忆信道

离散无记忆信道的转移概率矩阵可表示为

$$\mathbf{P}=\{p(j \mid k)\}=\left[\begin{array}{cccc}
p(0 \mid 0) & p(1 \mid 0) & \cdots & p(J-1 \mid 0) \newline
p(0 \mid 0) & p(1 \mid 1) & \cdots & p(J-1 \mid 1) \newline
\vdots & \vdots & \ddots & \vdots \newline
p(0 \mid K-1) & p(1 \mid K-1) & \cdots & p(J-1 \mid K-1)
\end{array}\right]$$

+ 若 $P$ 中每一行都是第一行的一个置换，则该信道关于输入对称。

+ 若 $P$ 中每一列都是第一列的一个置换，则该信道关于输出对称。

+ 若一个信道既关于输入对称，又关于输出对称，则该信道是 **对称的**。

  $$
  \begin{aligned}
  P & =\left(\begin{array}{llll}
  0.2 & 0.1 & 0.4 & 0.3 \newline
  0.3 & 0.1 & 0.4 & 0.2 \newline
  0.2 & 0.4 & 0.1 & 0.3 \newline
  0.3 & 0.4 & 0.1 & 0.2
  \end{array}\right)
  \end{aligned}
  $$

+ 若一个信道可以按列划分，得到若干个子信道，若划分出的所有子信道均是对称的，则称该信道是准对称的。

  $$
  \begin{aligned}
  \mathbf{P} & =\left(\begin{array}{lll}
  0.8 & 0.1 & 0.1 \newline
  0.1 & 0.1 & 0.8
  \end{array}\right)
  \end{aligned}
  $$

可以证明，**达到准对称离散无记忆信道容量的输入分布为均匀分布**。基本思路是当 $Q_k = \frac{1}{K}$ 时可以推出 $I(X=k;Y)$ 都相等。

根据定义，若信道关于输入对称，则（其中 $k$ 任取）：

$$I(X;Y)=H(Y) - H(Y|X) = H(Y) + \sum\limits_{j=0}^{J-1} p(j|k) \log p(j|k)$$

通过上述结论，我们可以获得关于信道容量在特殊情形下的性质：

1. 若信道关于输入对称，则（其中 $k$ 任取）：

    $$C \leq \log J + \sum\limits_{j=0}^{J-1} p(j|k) \log p(j|k)$$

2. 若信道同时关于输入和输出对称（即信道对称），则有 $H(Y) = \log J$，同时再根据上面均匀分布那个结论，于是（其中 $k$ 任取）：

    $$C = \log J + \sum\limits_{j=0}^{J-1} p(j|k) \log p(j|k)$$

+ Examples

    + K 元对称信道的容量：直接用上面第二个公式就可以算。

        <p><img src="images/it-53.png" alt="it-53" width="70%"></p>

    + 除删信道（BEC）：由于 BEC 是准对称的，所以当输入等概时取到信道容量。

		<p><img src="images/it-54.png" alt="it-54" width="70%"></p>

	+ 模 K 加法信道

		<p><img src="images/it-55.png" alt="it-55" width="70%"></p>

#### 7.4 General Case - 转移概率矩阵可逆信道

对称信道由于均匀分布的那个结论所以方便计算。考虑更一般的情况，给定矩阵 $P$，如何求出取到信道容量的 $\\{Q_k\\}$ 分布和信道容量 $C$？

如果 $P$ 可逆并且假设 $Q_k > 0$，这个问题是可解的：

<p><img src="images/it-56.png" alt="it-56" width="80%"></p>

由此线性方程解出 $\beta_j$。

注意到 $\sum\limits_{j=0}^{J-1} w_j = 1, w_j = 2^{\beta_j-C}$，所以 $C = \log (\sum\limits_{j=0}^{J-1} 2^{\beta_j})$。

再根据 $C$ 和 $\beta_j$，回过头来解 $w_j$ 以及 $Q_i$。

+ 课件上有例子。


### Lec 8 离散无记忆信道的编码定理


### Lec 9 加性高斯噪声（AWGN）信道