---
{"created":"2025-10-20T10:13","updated":"2025-10-20T15:01","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 29 相似矩阵 若尔当型/","dgPassFrontmatter":true,"noteIcon":""}
---

### 正定矩阵相关
正定矩阵来源于[[math/Linear Algebra/Lecture 16 投影矩阵，最小二乘#最小二乘法\|Lecture 16 投影矩阵，最小二乘#最小二乘法]]，其中出现了$A^TA$,其作用是将长方形的m by n矩阵变成了n by n的矩阵，**$A^TA$矩阵是半正定矩阵**，证明如下：
$$
x^T(A^TA)x=(x^TA^T)(Ax)=(Ax)^T(Ax)
$$
而A是一个矩阵，x是一个列向量，A左乘x仍然是一个向量，这是之前我们学过的矩阵实际上是一种坐标轴，描述着线性变换的关系。
那实际上$(Ax)^T(Ax)$那就是一行乘一列，实际上就是对两个Ax的对应坐标相乘，实际上就是向量Ax的模长的平方，即
$$
x^T(A^TA)x=||Ax||^2\geq 0
$$
所以方阵$A^TA$是半正定矩阵
如果矩阵A的零空间只有零向量，换句话说Ax=0只有唯一解x=0,那么对于任意的$x\neq0$ 的任意向量，$||Ax||^2>0, A^TA$为正定矩阵

**对于正定矩阵A的逆矩阵$A^{-1}$, 也是正定矩阵**，证明如下：
原矩阵A为正定矩阵，所以其各个特征向量$\lambda$都是正数，而逆矩阵$A^{-1}$的特征值是原矩阵A特征值的倒数，即$\frac{1}{\lambda}$,那么也是正数，所以逆矩阵$A^{-1}$也是正定矩阵
为什么逆矩阵$A^{-1}$的特征值是A特征值的倒数？ 证明如下：
$$
\begin{aligned}
Ax&=\lambda x\\
A^{-1}Ax&=A^{-1}\lambda x\\
x&=A^{-1}\lambda x\\
\frac{1}{\lambda}x&=A^{-1} x,满足定义
\end{aligned}
$$

**矩阵A和矩阵B是正定矩阵，它们的和A+B的结果矩阵也是正定矩阵**，证明如下：
如果矩阵A和矩阵B是正定矩阵，对任意向量x都满足
$$
\left\{
\begin{aligned}
x^TAx>0\\
x^TBx>0
\end{aligned}
\right.
$$
矩阵乘法法则(满足分配率)，可得$x^T(A+B)x=x^TAx+x^TBx>0$
### 相似矩阵
如果存在n by n的矩阵A,B 以及一个矩阵M具有可逆矩阵$M^{-1}$,使得$B=M^{-1}AM$成立，则两个方阵A和B相似
其实这个式子我们早就接触过，比如我们对角化的式子$S^{-1}AS=\Lambda$,其中$\Lambda$就是A的一个相似矩阵
事实上，基于矩阵A我们能够取不同的矩阵B得到一系列的相似矩阵，这样就将这些矩阵联系起来，这些矩阵属于一类矩阵，这一类矩阵中的任意两个矩阵彼此相似，其实$\Lambda$是其中最简单的一个矩阵，这些相似矩阵的共同点在于具有相同的特征值！
证明如下
$$
\begin{aligned}
&Ax=\lambda x,B=M^{-1}AM\\
&A\textcolor{red}{I}x=\lambda x \to A\textcolor{red}{MM^{-1}}x=\lambda x\\
& M^{-1}AMM^{-1}x=\lambda(为常数)M^{-1}x\\
&(M^{-1}AM)M^{-1}x=\lambda M^{-1}x\\
&BM^{-1}x=\lambda M^{-1}x
\end{aligned}
$$
我们可以把$M^{-1}x$看做是一个新的列向量，那么实际上B和A有相同的特征值和不同的特征向量
存在一种特殊情况，如果矩阵A出现了数值相同的特征值(实际上就是出现了线性相关的特征向量)，那么矩阵A无法对角化，也就无法得到相应的对角矩阵$\Lambda$作为相似矩阵
例如对于一个矩阵A，它具有两个相同的特征值$\lambda_1=\lambda_2=4$
那么这两个特征值构成的对角矩阵$B=\begin{bmatrix}4&0\\0&4\end{bmatrix}$并不是与矩阵A相似，并不能归类到一个族Family，这个对角阵单独分成一类，这是因为对于任意可逆矩阵M, 都有$M^{-1}BM=4M^{-1}IM=4I=B$
而矩阵A与其他的具有相同特征值的矩阵归为另一个Family，如$\begin{bmatrix}4&1\\0&4\end{bmatrix}$,$\begin{bmatrix}5&1\\-1&3\end{bmatrix}$,$\begin{bmatrix}4&0\\11&4\end{bmatrix}$ ...

### 若尔当标准型
在上面这一系列矩阵$\begin{bmatrix}4&1\\0&4\end{bmatrix}$,$\begin{bmatrix}5&1\\-1&3\end{bmatrix}$,$\begin{bmatrix}4&0\\11&4\end{bmatrix}$ ...中，其中任意两个矩阵都相似，其中最简单的那一个叫做若尔当型(若尔当标准型)，这其实算是一种对角矩阵的更一般形式(?)
对于下面的矩阵A，是一个若尔当型
$$
A=\begin{bmatrix}
0&1&0&0\\
0&0&1&0\\
0&0&0&0\\
0&0&0&0\\
\end{bmatrix}
$$
其特征值都是$\lambda_i=0$秩为rank=2，所以零空间为4-2=2，它的特征向量缺失了两个
对于另一个矩阵B，它具有相同的特征值，而且特征向量也是缺失两个，它与矩阵A相似
$$
B=\begin{bmatrix}
0&1&7&0\\
0&0&1&0\\
0&0&0&0\\
0&0&0&0\\
\end{bmatrix}
$$
而考察以下矩阵
$$
C=\begin{bmatrix}
0&1&0&0\\
0&0&0&0\\
0&0&0&1\\
0&0&0&0\\
\end{bmatrix}
$$
虽然它也具有相同的特征值，而且特征向量也是缺失两个，但是它与矩阵A不相似，这里是根据矩阵卡划分的若尔当块Jordan Blocks进行判定的
若尔当块(Jordan block)是一个矩阵$j_i$它在对角线上是重复的特殊值，左下元素都是0，右上紧挨着对角线的元素都是1，其余都是0
若尔当理论是指每一个方阵A都相似于一个若尔当矩阵J，而这个若尔当矩阵是由若干个若尔当块Jordan Blocks构成的。可以对原矩阵按照若尔当块进行划分，如果两个矩阵是由不同的若尔当块构成的，那么即使他们矩阵有相同的特征值和特征向量，也不能说他们是相似矩阵
$$
J=
\begin{bmatrix}
J_1 & 0 &\cdots &0\\
0&J_2& \cdots &0\\
\vdots & \vdots& \ddots &\vdots\\
0&0&\cdots&J_d
\end{bmatrix}
$$
对于A和C
![Pasted image 20251020145655.png|400](/img/user/accessory/Pasted%20image%2020251020145655.png)
若按照若尔当块进行划分，得到不同的结构，所以两个矩阵并不相似
当然如果方阵有n个线性独立的特征向量，则方阵对角化，那么它所对应的若尔当矩阵J是特征值矩阵(对角矩阵)$\Lambda$
