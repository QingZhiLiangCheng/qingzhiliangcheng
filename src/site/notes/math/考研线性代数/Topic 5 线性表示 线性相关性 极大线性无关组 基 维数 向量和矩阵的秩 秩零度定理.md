---
{"created":"2026-05-03T21:38","updated":"2026-05-26T16:04","dg-publish":true,"permalink":"/math/考研线性代数/Topic 5 线性表示 线性相关性 极大线性无关组 基 维数 向量和矩阵的秩 秩零度定理/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2026-05-03T21:38","updated":"2026-05-26T16:04"}}
---

### 线性表示与线性相关性
#### 线性相关性的概念
Vectors $x_2,x_2,x_3,...,x_n$ are independent if no combination gives zero vector(except the zero combination).
换句话说，如果$x_1,x_2,x_3...x_n$存在**不全零**的线性组合为零向量就是线性相关，如果不存在，就是线性无关
$$
k_1 x_1+k_2x_2+...+k_nx_n = 0，(k_1,k_2,...,k_n \neq0) \to indepent
$$

#### 线性相关性的理解1 -- 线性表示
其实对上面这个式子我们很容易进行移项并且除以系数
$$
-\frac{k_1}{k_m}x_1+...-\frac{k_{m-1}}{k_m}x_{m-1}-\frac{k_{m+1}}{k_m}x_{m+1}-...-\frac{k_n}{k_m}x_n = x_m
$$
换句话说其实就是其中一个向量可以被其他的向量的线性组合表示，这叫$x_m$能被那些向量线性表示！
这其实很好理解，假设一个二维平面中有三个向量，那么一定有其中的两个向量可以线性组合表示出第三个向量的反向量。其实这其中的本质在于，其实两个向量就能确定出这个平面中的任意一个向量，也就是说，只要两个向量就能确定出这个平面，而第三个向量就是多余的

#### 线性相关性的理解2 -- 方程组
其实线性相关性是向量组的一个概念，是向量组的线性相关或线性无关，而不是矩阵的线性相关性，但是我们可以把向量组看做一个矩阵, 与矩阵的性质(零空间，秩)联系起来
也就是说，把所有的x向量放到一起，看做Ax=0中的A, 而k其实就是Ax=0的解x
$$\begin{pmatrix} | & | & & | \\ x_1 & x_2 & \dots & x_n \\ | & | & & | \end{pmatrix} \begin{pmatrix} k_1 \\ k_2 \\ \vdots \\ k_n \end{pmatrix} = 0$$
如果有解，且不是零向量，那么就说明一定有一组k可以满足线性相关的那个式子
而前面我们学过了，对于Ax=0是唯一解还是多解的核心在于A有没有自由变量 或者说多余的列
- 如果有自由变量，即有非零解，这就说明一定有自由变量的线性组合构成零空间，换句话说就是一定存在向量k 满足 矩阵A中的向量线性组合构成零向量 -> 线性相关
- 反之，线性无关


其实你会发现事实上零空间做的事儿就是告诉我们怎么组合这些向量才线性相关

#### 线性相关性的理解3 -- 秩
如果从秩的角度看 如果$\begin{pmatrix} | & | & & | \\ x_1 & x_2 & \dots & x_n \\ | & | & & | \end{pmatrix}$ 线性无关，即这些向量刚好能够张成整个n维空间
比如有n+1个元素 那就是n+1维空间中的一个n维子空间？
换句话说 就是秩一定是是n的！

#### 判断线性相关性的方法
**Approach 1: 如果有零向量 一定线性相关**
因为其余向量乘0 零向量取任意非零值 线性组合为0向量 满足线性相关性不全为0的定义

**Approach 2: 如果有一组向量成比例 整个向量组线性相关**

**Approach 3: 如果向量的个数大于向量的维度， 线性相关**

**Approach 4: 行列式为0**
一般比如三个三维向量 用的比较多

**Approach 5: 通过矩阵的秩判断**
$r(α_1,...,α_n)=n$

**Approach 6: 理解2中对应方程组的解的情况**

**Approach 7: 通过向量间加加减减化成了已知相关性的向量组**
比如已知$α_1,...,α_n$的线性相关性 将要求的向量变换出了$α_1,...,α_n$

**Approach 8:**
将$α_1,...,α_n$的加加减减通过乘法 左行或者右列写成$α_1,...,α_n$与一个矩阵相乘的形式，通过秩来判断

...

#### 线性无关的证明 证什么
线性无关的核心在于 有且仅有当k全为0的时候 式子$k_1 α_1+k_2α_2+...+k_nα_n = 0$成立
所以**一个证明的方向**就是证明方程组有且只有零解 -- 换句话说 那不就相当于证明行列式不为0  证明系数矩阵满秩

**另一个证明的方向**是 如果我知道$α_1,...,α_n$的线性相关性 将要求的$α_1,...,α_n$加加减减的那个向量组通过左行或者右列的方式 写成矩阵相乘的形式
通过定理$A可逆,r(AB)=r(B)$来通过要证向量组的秩来证明！
比如
![Pasted image 20260525210010.png\|400](/img/user/accessory/Pasted%20image%2020260525210010.png)

**第三个证明的方向**是：当然也可以把要求的$α_1,...,α_n$加加减减的那个向量组 通过加加减减 变回$α_1,...,α_n$的形式，比如
![Pasted image 20260525210054.png\|400](/img/user/accessory/Pasted%20image%2020260525210054.png)


#### 线性表示
如果向量β能被$α_1,α_1,...,α_n$线性表示，也就是说，存在一组k，能够使得$k_1α_1+k_2α_1+...+k_nα_n=β$，也就是说，方程组$[\boldsymbol{\alpha}_1, \boldsymbol{\alpha}_2, \cdots, \boldsymbol{\alpha}_m] \begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_m \end{bmatrix} = \boldsymbol{\beta}$有解，也就是说，β实际落在左边矩阵A的列空间之中！和秩结合起来就是$r(\boldsymbol{\alpha}_1, \boldsymbol{\alpha}_2, \cdots, \boldsymbol{\alpha}_m) = r(\boldsymbol{\alpha}_1, \boldsymbol{\alpha}_2, \cdots, \boldsymbol{\alpha}_m, \boldsymbol{\beta})$

#### 向量组之间的线性表示
向量组 (I) $\alpha_1, \alpha_2, \cdots, \alpha_s$，(II) $\beta_1, \beta_2, \cdots, \beta_t$。
若 $\forall \alpha_i$，$\alpha_i$ 均可由 $\beta_1, \beta_2, \cdots, \beta_t$ 线性表出，则称向量组 (I) 可由 (II) 线性表出
若 $\exists \alpha_i$，$\alpha_i$ 不能由 $\beta_1, \beta_2, \cdots, \beta_t$ 线性表出，则称向量组 (I) 不能由 (II) 线性表出
如果向量组 (I) 和 (II) 可以互相线性表出，则称向量组等价
两个向量组等价的一定是r(I)=r(II)秩相等  但是秩相等不一定等价

若 $r(\text{I}) = r(\text{II})$，且 (I) 可由 (II) 线性表出 $\iff$ (I) 与 (II) 等价
若 $r(\text{I}) = r(\text{II}) = r(\text{I}, \text{II})$ $\iff$ (I) 与 (II) 等价




#### 部分组 整体组的关系
若 $k_1, k_2, k_3$ 不全为 $0$，而$k_1 \alpha_1 + k_2 \alpha_2 + k_3 \alpha_3 = 0$成立 -> $\alpha_1, \alpha_2, \alpha_3$ 相关 
那么，对 $k_1, k_2, k_3, 0, 0$ 仍不全为 $0$，即
$$k_1 \alpha_1 + k_2 \alpha_2 + k_3 \alpha_3 + 0\alpha_4 + 0\alpha_5 = 0$$
仍成立。
即：若 $\alpha_1, \alpha_2, \alpha_3$ 相关 $\implies \alpha_1, \alpha_2, \alpha_3, \alpha_4, \alpha_5$ 必相关。
即 部分组相关 -> 整体组相关 但注意整体相关无法保证部分组相关
但如果取逆否命题 若整体组无关 -> 部分组无关

#### 缩短组和延伸组的关系
对于一个只有零解的方程
$$\begin{cases}
x_1 + 2x_2 = 0 \\
x_1 - 5x_2 = 0
\end{cases}$$
我们给他加上两个方程 仍然是只有零解
$$\begin{cases}
x_1 + 2x_2 = 0 \\
x_1 - 5x_2 = 0\\
x_1 + 10x_2 = 0 \\
-3x_1 - 7x_2 = 0
\end{cases}$$
实际上前两个方程就已经够了 第三个第四个方程相当于没用
即
$$\begin{pmatrix} 1 \\ 1 \end{pmatrix} \begin{pmatrix} 2 \\ -5 \end{pmatrix}无关 \to \begin{pmatrix} 1 \\ 1 \\ 1 \\ -3 \end{pmatrix}  \begin{pmatrix} 2 \\ -5 \\ 10 \\ -7 \end{pmatrix}无关$$
即 若 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 无关，则其延伸组 $\tilde{\alpha}_1, \tilde{\alpha}_2, \cdots, \tilde{\alpha}_s$ 必无关
逆否命题 若 $\tilde{\alpha}_1, \tilde{\alpha}_2, \cdots, \tilde{\alpha}_s$ 有关，其缩短组$\alpha_1, \alpha_2, \cdots, \alpha_s$必有关

其实这里考虑一下我们在求解方程组的基础解系时的手段，假设$x_2,x_4,x_5$是自由变量，我们取值的手段是
![Pasted image 20260525160601.png\|300](/img/user/accessory/Pasted%20image%2020260525160601.png)
因为这么取是最简单的 线性无关的三个向量 再补充上$x_1, x_3$ 实际上就是延伸组 一定线性无关

#### 一些定理
如 $n$ 维向量 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 线性无关，$\alpha_1, \alpha_2, \cdots, \alpha_s, \beta$ 线性相关，则向量 $\beta$ 可由 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 线性表出，且表示法唯一。

定理 向量组 $\alpha_1,\alpha_2\cdots,\alpha_s (s \ge 2)$ 线性相关 $\iff \exists \alpha_i$ 可由其余的向量线性表出。


### 张成空间
vectors $v_1,...,v_l$ span a space means: The space consists of all combination of those vectors.
事实上我们早就在列空间中提到过空间的概念，我们把矩阵A中的向量张成的空间叫矩阵A的列空间，其实就是向量组有张成空间的性质
这里指的张成的空间事实上是包含这些向量空间中最小的一个空间 -- 意味着并不是l个向量就一定是l维空间
换句话说实际上就是这个空间是这些向量的线性组合

### 极大线性无关组 向量组的秩
在向量组 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 中，若有 $r$ 个向量$\alpha_{i_1}, \alpha_{i_2}, \cdots, \alpha_{i_r}$ 线性无关，再添加任一向量 $\alpha_j \ (j = 1, 2, \cdots, s)$，向量组 $\alpha_{i_1}, \alpha_{i_2}, \cdots, \alpha_{i_r}, \alpha_j$ 就线性相关，则称 $\alpha_{i_1}, \alpha_{i_2}, \cdots, \alpha_{i_r}$ 是向量组 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 的一个极大线性无关组
也就是说实际上 除了选出来的向量 剩下的每一个向量都能被选出来的极大线性无关组线性表示！
也就是是说 极大线性无关组是向量组α中 能表示其他向量的一组向量 这不就是一组基嘛！
对于同一组向量 向量组的极大线性无关组实际上是不唯一的 但是个数是相同的 这个个数 叫 向量组的秩
其实这个极大线性无关组的定义里面就包含着前面的一个定理 -- 如 $n$ 维向量 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 线性无关，$\alpha_1, \alpha_2, \cdots, \alpha_s, \beta$ 线性相关，则向量 $\beta$ 可由 $\alpha_1, \alpha_2, \cdots, \alpha_s$ 线性表出，且表示法唯一

在求方程组的时候 如果有无穷多解 我们怎么表示无穷多解 -- 实际上就是求的极大线性无关组 极大线性无关组的线性组合可以表示零空间中的其他向量！

现在有两组向量$A=(\alpha_1, \alpha_2, \cdots, \alpha_s),B=(β_1, β_2, \cdots, β_s)$ 如果我们对A做一些行变换记作E，即$EA=B$, 换句话说$α_1$在E的作用下变成了$β_1$,...
那如果$(\alpha_1, \alpha_2, \cdots, \alpha_s)$线性相关 那就是存在一组不全为0的k，使得
$$k_1 \alpha_1 + k_2 \alpha_2 + \cdots +k_s \alpha_s  = 0$$
这其实也是方程
$$\begin{pmatrix} | & | & & | \\ α_1 & α_2 & \dots & α_s \\ | & | & & | \end{pmatrix} \begin{pmatrix} k_1 \\ k_2 \\ \vdots \\ k_s \end{pmatrix} = 0$$
如果现在左乘一个P矩阵， 那就是
$$P\begin{pmatrix} | & | & & | \\ α_1 & α_2 & \dots & α_s \\ | & | & & | \end{pmatrix} \begin{pmatrix} k_1 \\ k_2 \\ \vdots \\ k_s \end{pmatrix} = 0$$
$$\begin{pmatrix} | & | & & | \\ β_1 & β_2 & \dots & β_s \\ | & | & & | \end{pmatrix} \begin{pmatrix} k_1 \\ k_2 \\ \vdots \\ k_s \end{pmatrix} = 0$$
也就是说同样是这一组不全为0的k，使得
$$k_1 β_1 + k_2 β_2 + \cdots +k_s β_s  = 0$$
这告诉我们如果α线性相关 经过行变换 β也线性相关, 并且组合系数相同

这给了我们一条回答极大线性无关组 线性表处的一条通路！

用行变换得到的β 能得到原来α的极大线性无关组和线性表示的倍数


eg:
![Pasted image 20260525215554.png\|500](/img/user/accessory/Pasted%20image%2020260525215554.png)
![Pasted image 20260525215513.png\|500](/img/user/accessory/Pasted%20image%2020260525215513.png)

![Pasted image 20260525215534.png\|400](/img/user/accessory/Pasted%20image%2020260525215534.png)
### 向量的基
Basis  for a space is a sequence of vectors with
- they are independent
- they span all the space

实际上就是极大线性无关组
告诉子空间的基 -- 就相当于是告诉了子空间的全部有用信息

### 矩阵的秩
前面提到过 极大线性无关组的个数 叫做矩阵的秩 张宇老师的书上也是这么讲的
在我学的MIT的Linear Algebra中第一次提到秩 说的是 秩是主元的个数
也有过 秩实际上就是矩阵中的非零行的个数
在李永乐老师教材和大二学的教材上 秩是由子式引出的 -- 首先在m * n矩阵A中，任取k行和k列 位于行和列的交叉点上的$k^2$个元素按其在矩阵A中的次序可构成一个k接行列式，这个行列式就是A的一个k阶子式 -- 而矩阵的秩：A中存在r阶子式不等于零 高于r阶的子式均等于零 -- 矩阵A的秩为r
其实这个定义也就是用行列式定义的
这个也意味着 我们最多能跳出r个完全独立的行和列
这样就全串起来了

**三秩相等 矩阵的秩=行秩=列秩**
矩阵的秩=行向量组的秩=列向量组的秩
其实理解这个定理最简单的方法就是 矩阵就是行向量或者列向量的堆叠 矩阵的秩用主元理解 列向量组和行向量组的秩用极大线性无关组的个数理解 而对于一个矩阵主元所在的行和列 就是那个基或者极大线性无关组的向量！

**矩阵秩的判断**
行列式
相关和无关
Ax=0有非零解 r(A)<n; n-r(A)
特征值


$$0 \leqslant r(A_{m \times n}) \leqslant \min(m, n)$$
$$r(A^T) = r(A), \quad r(A^T A) = r(A)$$
$$r(kA) = r(A), \quad k \neq 0$$
$$r(A + B) \leqslant r(A) + r(B)$$
$$r(AB)\leq min(r(A),r(B))$$
$$
如果P,Q可逆，r(PAQ)=r(A),r(PA)=r(A),r(AQ)=r(A)
$$
$$A-m\times n,如果r(A)=n,则r(AB)=r(B)$$

$$A-m\times n, B-n\times s且AB=O,则r(A)+r(B)\leq n$$
以后碰到AB=O 除了要想到行列式|A||B|=0 -> |A|或|B|=0
还要想到 B中的每个列向量啊都是Ax=0的解
还要想到这个定理 $r(A)+r(B)\leq n$
证明：其实Ax=0的零空间的维度就是自由向量的个数 就是n-r(A) 而B所有列向量所组成的空间 要么有很多个 就是这个解空间的维度 要么就是其中的一个解空间（因为B可能只有几个向量） -- $r(B)\leq n-r(A)$ -> $r(A)+r(B)\leq n$

伴随矩阵的秩
$$r(A^*) = \begin{cases} n, & r(A) = n \\ 1, & r(A) = n-1 \\ 0, & r(A) < n-1 \end{cases}$$
证明见[[math/考研线性代数/Topic 7 行列式#伴随矩阵\|Topic 7 行列式#伴随矩阵]]



### 维数 秩零度定理
Every basis for the sapces has the same number of vectors -- 这个number叫做空间的维数

假设有一个有一个矩阵A
$$
A=\begin{bmatrix}
1&2&3&1\\
1&1&2&1\\
1&2&3&1
\end{bmatrix}
$$
这四个向量显然构成了矩阵A的列空间(因为定义就是这样的)，但这四个向量却不是这个列空间的基，因为他们线性相关
这个列空间的一个基 -- 事实上就是主元列的两个向量 或者 事实上还有别的
所以其实rank(A)除了是主元列的个数之外，还是列空间的维度 -- 注意不是矩阵的维度，是列空间的维度 dimC(A)
在A中存在两个自由列column3, column4 那么其实很容易写出Ax=0的零空间$c_1\begin{bmatrix}-1\\-1\\1\\0\end{bmatrix}+c_2\begin{bmatrix}-1\\0\\0\\1\end{bmatrix}$
事实上，这两个特殊解的线性组合是解空间 -- 所以这是零空间的基 -- 零空间的维度是2 dimN(A)=2
！！！！dimN(A) = n-r
这就是所谓的**秩零度定理**