---
{"created":"2026-05-29T16:17","updated":"2026-05-29T21:39","dg-publish":true,"permalink":"/math/考研线性代数/Topic 11 实对称矩阵/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2026-05-29T16:17","updated":"2026-05-29T21:39"}}
---

### 对称矩阵
对称矩阵 Symmetrix Matrices是指满足$A=A^T$的特殊矩阵
特殊矩阵在特征值和特征向量上都会有所反应，特征值和特征向量是我们了解一个矩阵的方法
对于各元素都是实数的对称矩阵会满足一下两条特征
- 特征值都是实数
- 特征向量相互垂直/正交

这种矩阵其实也叫实对称矩阵 实就是实数的意思
我们只讨论实数的情况


### 谱定理
对于一个有n个线性独立的特征向量的矩阵A，可以对角化分解为$A=S\Lambda S^{-1}$
由于对称矩阵的特征向量是相互垂直的，所以实际上特征向量矩阵S是一个正交矩阵，符号为Q
那么对于对称矩阵A的对角化，实际上分解为了$A=Q\Lambda Q^{-1}$
那其实对于实对称矩阵来说 一定和它的对角化矩阵相似 因为特征向量相互垂直或者说正交
对于正交矩阵来说，其特性是$Q^{-1}=Q^T$，所以实际上$A=Q\Lambda Q^T$  
之所以用最后这个公式，是因为我们矩阵的置换操作比求逆操作更简单
将矩阵A分解为$Q\Lambda Q^T$称为Spectral Theorem 谱定理

如果一个矩阵对角化能够分解为$Q\Lambda Q^T$的形式，那这个矩阵就是对称矩阵
证明如下:
$$
\begin{aligned}
&A=Q\Lambda Q^T\\
&两边转置，有A^T=(Q\Lambda Q^T)^T\\
&即A^T=(Q^T)^T\Lambda^T Q^T = Q\Lambda Q^T
\end{aligned}
$$
如果将谱定理进一步展开
$$
\begin{aligned}
A&=Q\Lambda Q^T\\
&=\begin{bmatrix}|&|& &|\\q_1&q_2&\cdots& q_n\\|&|& &|\end{bmatrix}
\left[\begin{array}{rrrr}
\lambda_1 & 0 & \cdots & 0 \\
0 & \lambda_2 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots\\
0 & \cdots & 0 & \lambda_n
\end{array}\right]
\begin{bmatrix} -&q_1^T&-\\-&q_2^T&-\\ &\vdots&\\-&q_n^T&-\end{bmatrix}
\end{aligned}
$$
前两个矩阵相乘可以看做是对最左边这个矩阵做了列变换，即
$$
Q\Lambda =\begin{bmatrix}|&|& &|\\\lambda_1q_1&\lambda_2q_2&\cdots& \lambda_nq_n\\|&|& &|\end{bmatrix}
$$

$$
(Q\Lambda)Q^{T}=\lambda_1q_1q_1^T+\lambda_2q_2q_2^T+\cdots+\lambda_nq_nq_n^T
$$
这里面的每一项 $q_1q_i^T$都是一个投影矩阵，因为它满足$P^T=P,P^2=P$

### 实对称矩阵通过正交矩阵对角化
实际上谱定理告诉我们 我们可以通过正交矩阵将实对称矩阵对角化，即$Q^TAQ=\Lambda$
注意啥叫正交矩阵：标准向量组的向量都放到一起，这个矩阵叫做标准正交矩阵Q！ From[[math/考研线性代数/Topic 6 正交和投影\|Topic 6 正交和投影]]
其实仍然去求特征值 特征向量
对于实对称矩阵来说 不同特征值的特征向量是一定正交的！证明如下：
$$\begin{align}
Ax_1 = \lambda_1 x_1\\
Ax_2 = \lambda_2 x_2
\end{align}
$$
我们希望去证明$x_1,x_2$点乘为0，即$x_1^Tx_2=0$
给第二个式子左乘$x_1^T$，把得$x_1^Tx_2$凑出来
$$
x_1^TAx_2=\lambda_2x_1^Tx_2
$$
如果把左乘的$x_1^T$和A放在以前 并且转置 把$Ax_1$带进去得到
$$(Ax_1)^T x_2 = (\lambda_1 x_1)^T x_2 = \lambda_1 (x_1^T x_2)$$
那事实上这两个式子就得到了
$$\lambda_2 (x_1^T x_2) = \lambda_1 (x_1^T x_2)$$
提取公因式
$$(\lambda_1 - \lambda_2)(x_1^T x_2) = 0$$
而前提是两个特征值不相等 所以只能$x_1^Tx_2=0$！

这告诉我们 如果我们在对实对称矩阵A进行对角化的时候 如果求出不相同的特征值 那么他们对应的特征向量一定是正交的 这个时候只需要单位化就可以了！

那如果存在相同的特征值
如果存在足够多的线性无关的特征向量
![Pasted image 20260529172555.png\|300](/img/user/accessory/Pasted%20image%2020260529172555.png)
实际上我们按照自由变量求出来的线性无关的基础解系这是这个平面的一个普普通通的基向量 可能垂直 也可能不垂直
如果不垂直 那就需要施密特正交化呐！ 施密特正交化见[[math/考研线性代数/Topic 6 正交和投影#Gram-Schmidt 正交化\|Topic 6 正交和投影#Gram-Schmidt 正交化]]
但是如果自己求特征向量 我们可以通过一定的手段 求出正交的特征向量来！
![Pasted image 20260529213942.png\|400](/img/user/accessory/Pasted%20image%2020260529213942.png)



### 利用正交求特征向量
![Pasted image 20260529204317.png\|500](/img/user/accessory/Pasted%20image%2020260529204317.png)
a比较好说 用$α_1和α_2$正交一下就求出来了
而$α_3$需要与$α_1和α_2$正交 列出来一个方程组 解方程组得到所有可能的向量
![Pasted image 20260529204443.png\|300](/img/user/accessory/Pasted%20image%2020260529204443.png)
