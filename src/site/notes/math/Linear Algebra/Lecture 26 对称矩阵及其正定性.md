---
{"created":"2025-10-13T10:00","updated":"2025-10-13T10:36","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 26 对称矩阵及其正定性/","dgPassFrontmatter":true,"noteIcon":""}
---

### 对称矩阵
对称矩阵 Symmetrix Matrices是指满足$A=A^T$的特殊矩阵
特殊矩阵在特征值和特征向量上都会有所反应，特征值和特征向量是我们了解一个矩阵的方法，比如上一节课的Markov Matrices(马尔可夫矩阵)，它会有一个值为1的特征值...
对于各元素都是实数的对称矩阵会满足一下两条特征
- 特征值都是实数
- 特征向量相互垂直/正交

这部分证明我觉得蛮难的，所以就没整理

### 谱定理
对于一个有n个线性独立的特征向量的矩阵A，可以对角化分解为$A=S\Lambda S^{-1}$
由于对称矩阵的特征向量是相互垂直的，所以实际上特征向量矩阵S是一个正交矩阵，符号为Q
那么对于对称矩阵A的对角化，实际上分解为了$A=Q\Lambda Q^{-1}$
对于正交矩阵来说，其特性是$Q^{-1}=Q^T$，所以实际上$A=Q\Lambda Q^T$
之所以用最后这个公式，是因为我们矩阵的置换操作比求逆操作更简单
将矩阵A分解为$Q\Lambda Q^T$成为Spectral Theorem 谱定理

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

### 主元与特征值的符号的关系
对于给定矩阵，我们知道它的特征值是实数，现在我们关心正负的问题，因为特征值的正负很重要，比如对于微分方程来说，特征值的正负决定了状态是否稳定
对于任意一个实对称矩阵 `A`，通过不进行行交换的高斯消元法得到的主元（pivots）中，正、负、零的个数，与该矩阵的特征值中正、负、零的个数完全相同。
这个的证明好像涉及到二次型的内容，这里也是先不去深究了
这玩意叫做西尔维斯特惯性定理
### 正定性
正定矩阵 Positive definite是特征值都为正数的对称矩阵
根据前面的主元与特征值符号的关系，可以知道正定矩阵的主元也是正数
同样，我们可以根据矩阵的主元的正负性来判断一个矩阵是否为正定矩阵

矩阵的特征值的积等于矩阵的行列式，所以正定矩阵的特征值也是正数
但反过来不一定成立，也就是说，矩阵的行列式为正数并不能推断它就是正定矩阵
例如矩阵$A=\begin{bmatrix}-1&0\\0&{-3}\end{bmatrix}$的行列式为正数，但是特征值都是负数，所以它不是正定矩阵
如果想根据行列式来判断矩阵是否正定，还需要还需要验证（沿着对角线向上的）n−1（其中 1≤k≤n）个「子方阵」的行列式是否都为正
例如在这个例子中，还需要查看矩阵$\left[ -1 \right]$的行列式的正负