---
{"created":"2025-11-26T21:23","updated":"2025-11-26T21:54","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 33 左右逆和伪逆/","dgPassFrontmatter":true,"noteIcon":""}
---

这里讨论的矩阵是$A_{m\times n}$！
### 左右逆相等
如果矩阵$A_{m\times n}$满秩(行满秩和列满秩 r=m=n)，它具有左右逆，且相同，即$A^{-1}_{left}=A^{-1}_{right}$
矩阵必为方阵！

### 左逆
$A_{m\times n}$列满秩，也就是$r=n<m$, 存在左逆$A^{-1}_{left}$, 满足$A^{-1}_{left}A=I$
$$
A^{-1}_{left}=(A^TA)^{-1}A^T
$$
$A^TA$是方阵，存在$(A^TA)^{-1}$,$(A^TA)^{-1}A^TA=I$
如果将左逆表达式乘到矩阵A的右边，得到的是投影矩阵$P=A(A^TA)^{-1}A^T$
该投影矩阵的作用是将该空间的向量投影到矩阵 A 的**列空间**中

### 右逆
$A_{m\times n}$行满秩，也就是$r=m<n$, 存在左逆$A^{-1}_{right}$, 满足$AA^{-1}_{right}=I$
$$
A^{-1}_{right}=A^T(AA^T)^{-1}
$$
$AA^T(AA^T)^{-1}=I$
如果将右逆乘到矩阵A的左边，则得到的结果矩阵是投影矩阵$P=A^T(AA^T)^{-1}A$
该投影矩阵的作用是将该空间的向量投影到矩阵 A 的**行空间**中

### 伪逆
当一个矩阵满秩时，r=m=n，存在左右逆，则它的零空间和左零空间只有一个零向量
当一个矩阵列满秩r=n，存在左逆，零空间只有一个零向量(对Ax=0未知数x的解只能是0向量)
当一个矩阵行满秩r=m,存在右逆，左零空间只有一个零向量($A^Ty=0$未知数y的解只能是0向量)
那么当一个矩阵的零空间或左零空间**具有非零向量**，则表示该矩阵不可逆（没有左逆或右逆），从求解方程组的角度来理解也是一样的
当矩阵 Am×n 列不满秩且行不满秩时 r<m,r<n，则在矩阵 A 的零空间和左零空间中，除了零向量以外，都还存在非零的向量，则矩阵 A 不可逆。可以将零空间存在着非零向量看作是「毁掉」了逆矩阵的存在性。
如果不把零空间（和左零空间）纳入考虑，将矩阵 A 看作仅作用于行空间中的向量，即通过 Ax 将行空间向量 x（可逆地）映射到列空间中，则存在矩阵可以实现逆方向的映射，该矩阵称为矩阵 A 的伪逆 pseudoinverse，记作$A^+$
![Pasted image 20251126215003.png](/img/user/accessory/Pasted%20image%2020251126215003.png)

### 求解伪逆
求矩阵$A^+$的其中一个方法是通过SVD奇异值分解$A=U\Sigma V^T$  ,只对可逆矩阵求逆，不可逆矩阵求伪逆
其中
$$
\Sigma =
\begin{bmatrix}
\sigma_1 & 0 & 0 & 0 & 0 & \cdots & 0 \\
0 & \sigma_2 & 0 & 0 & 0 & \cdots & 0 \\
0 & 0 & \ddots & & & & \vdots \\
0 & 0 & & \sigma_r & 0 & \cdots & 0 \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0
\end{bmatrix}
$$

$$
\Sigma^{+} =
\begin{bmatrix}
1/\sigma_1 & 0 & 0 & 0 & 0 & \cdots & 0 \\
0 & 1/\sigma_2 & 0 & 0 & 0 & \cdots & 0 \\
0 & 0 & \ddots & & & & \vdots \\
0 & 0 & & 1/\sigma_r & 0 & \cdots & 0 \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0 \\
\vdots & \vdots & & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & 0 & 0 & \cdots & 0
\end{bmatrix}
$$
伪逆的作用和逆矩阵的作用相似，所以 $ΣΣ^+$ 的结果会接近于单位矩阵
$$
\Sigma \Sigma^{+} =
\begin{bmatrix}
1 & 0 & 0 & 0 & \cdots & 0 \\
0 & 1 & 0 & 0 & \cdots & 0 \\
0 & 0 & \ddots & & & \vdots \\
0 & 0 & & 1 & \cdots & 0 \\
\vdots & \vdots & & \vdots & \ddots & \vdots \\
0 & 0 & 0 & 0 & \cdots & 0
\end{bmatrix}
$$
