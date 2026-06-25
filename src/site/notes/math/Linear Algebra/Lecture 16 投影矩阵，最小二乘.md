---
{"created":"2025-09-20T10:40","updated":"2025-09-20T16:11","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 16 投影矩阵，最小二乘/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2025-09-20T10:40","updated":"2025-09-20T16:11"}}
---

### 投影矩阵
上一节课已经讲过投影矩阵$P=A(A^TA)^{-1}A^T$ , 投影矩阵P作用于向量b，会将向量b投影到A的列空间中，现在来看两个极端的例子
**b在矩阵A的列空间中**
所谓的b在矩阵A的列空间中，是说b是矩阵中A的各列的线性组合，而其实在矩阵的乘法中，右乘其实就是对列的线性组合，也就说Ax是对A各列的线性组合，所以如果b在矩阵A的列空间中，意味着Ax=b，那么
$$
Pb=A(A^TA)^{-1}A^Tb = A(A^TA)^{-1}A^TAx=AA^{-1}(A^T)^{-1}A^TAx=AIx=Ax=b
$$
-> b在矩阵A的列空间中 => Pb=b

**b垂直于矩阵A的列空间**
如果A垂直于矩阵A的列空间中，根据前面学的正交补，那么b是A的左零空间中的一个向量，也就是说$A^Tb=0$ 
$Pb=A(A^TA)^-1A^Tb=A(A^TA)^{-1}0=0$

其实从上面的例子能看出来，向量b是能分解成两个向量的，一个向量在A的列空间中，一个向量在A的左零空间中(合理hh)，而对于$P=A(A^TA)^{-1}A^T$这个矩阵是把b投影到A的列空间中来说，就是保留A列空间中的分量p，去掉左零空间中的分量e
![Pasted image 20250920105436.png\|500](/img/user/accessory/Pasted%20image%2020250920105436.png)

b=e+p, e=b-p = b-Pb=(I-P)b -> I-P就是A的左零空间上的投影矩阵！

### 最小二乘法
仍然是上节课介绍过的例子
![Pasted image 20250917105819.png\|400](/img/user/accessory/Pasted%20image%2020250917105819.png)
这三个点代入这条直线的方程 $\begin{align}C+D=1\\C+2D=2\\C+3D=2\end{align}$ 写成矩阵形式$\begin{bmatrix}1&1\\1&2\\1&3\end{bmatrix}\begin{bmatrix}C\\D\end{bmatrix}=\begin{bmatrix}1\\2\\3\end{bmatrix}$，这显然是无解的
首先我们要明白最小误差是什么，我们定义误差是$A\hat x-b=e$ ， 以误差向量的模长的平方来作为误差和，即$|A\hat{x}-b|^2 = |e|^2 = e_1^2+e_2^2+e_3^2=（C+D-1)^2+(C+2D-2)^2+(C+3D-2)^2$ , 我们要求其最小平方和(最小二乘)
在线性代数中，$A\hat x$其实也是A的列空间中的向量，那么$A\hat x-b$是列空间的向量与b向量相见，其实就是e向量，如果想让e的模长的平方最小，就是让b对列空间做投影，该投影得到的Pb是列空间中距离b最近的向量，也就是说，其实是求解$A \hat x=Pb$!
$$
\begin{align}
A\hat{x}=Pb=A(A^TA)^{-1}A^Tb\\
方程两边同乘以A^T有: \space A^TA(A^TA)^{-1}A^Tb = A^Tb\\
A^TA\hat x =A^T b
\end{align}
$$
然后求得$A^TA,A^Tb$，能够得到
$$
\begin{bmatrix}3 & 6\\6&14\end{bmatrix}\begin{bmatrix}\hat C\\ \hat D\end{bmatrix}=\begin{bmatrix}5\\11\end{bmatrix}
$$
写成方程就是
$$
\begin{align}
3\hat C + 16 \hat D =5\\
6\hat C + 14 \hat D =11
\end{align}
$$
呃呃其实你会发现这个式子和对上面的最小二乘求偏导得到的结果是一样的！
也就是说这是等价的！

### 标准正交向量组
有一种线性无关的情况是比较特殊的：互相垂直的各列一定是线性无关的
更特殊的，如果垂直的是单位向量(标准正交)，比如$\begin{bmatrix}1\\0\\0\end{bmatrix},\begin{bmatrix}0\\1\\0\end{bmatrix},\begin{bmatrix}0\\0\\1\end{bmatrix}$,这些向量所组成的向量组一般被称为标准正交向量组，标准正交向量组中的向量互相垂直（正交）且为单位向量（标准）
同样标准正交向量还有$\begin{bmatrix}cos 𝜃 \\sin 𝜃\end{bmatrix},\begin{bmatrix}-sin 𝜃 \\cos 𝜃\end{bmatrix}$
