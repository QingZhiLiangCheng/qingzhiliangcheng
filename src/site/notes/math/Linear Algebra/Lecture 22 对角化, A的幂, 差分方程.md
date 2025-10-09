---
{"created":"2025-10-04T16:20","updated":"2025-10-09T13:28","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 22 对角化, A的幂, 差分方程/","dgPassFrontmatter":true,"noteIcon":""}
---

### 矩阵对角化
Lecture21中学习了求解特征向量和特征值的方法，而特征向量和特征值的一个作用就是对角化矩阵
$$
S^{-1}AS=\Lambda
$$
上面公式中，S是由n个线性独立的特征向量作为列向量构成的特征向量矩阵，$\Lambda$就是对角阵，是n个特征值作为对角线上的元素的方阵
> [!Question] 证明
> 这是对上面式子的一个证明
> 如果矩阵A有n个线性独立的特征向量，$x_1,x_2,...,x_n$ , 就可以罗列成一个矩阵，我们把这个矩阵叫做特征向量矩阵(eigenvector matrix)S.
> 如果将A矩阵与S矩阵相乘会得到$A\begin{bmatrix}\\x_1&x_2&...&x_n\\\\\end{bmatrix}$
> 而我们又知道$Ax=\lambda x$,那么实际上这里把矩阵乘法理解为，每一个向量在经过矩阵A的线性变换的作用后变成一个新的列向量然后罗列成回矩阵，即
> $$
> AS=A\begin{bmatrix}\\x_1&x_2&...&x_n\\\\\end{bmatrix}=
> \begin{bmatrix}\\\lambda_1 x_1&\lambda_2 x_2&...&\lambda_n x_n\\\\\end{bmatrix}
> $$
> 然后我们希望将这个矩阵分解，把特征向量放在一起，将特征值放在一起，这里是利用矩阵乘法中右乘对列向量线性组合的性质，把矩阵分解成了一个特征向量矩阵和一个对角线是特征值的矩阵，而后面这个矩阵就叫做对角矩阵
> $$
> \begin{bmatrix}\\\lambda_1 x_1&\lambda_2 x_2&...&\lambda_n x_n\\\\\end{bmatrix}=\begin{bmatrix}\\x_1&x_2&...&x_n\\\\\end{bmatrix}\left[\begin{array}{rrrr}
>         \lambda_1 & 0 & \cdots & 0 \\
>         0 & \lambda_2 &  & 0 \\
>         \vdots & & \ddots & \vdots\\
>         0 & \cdots & 0 & \lambda_n
>     \end{array}\right]
> $$
> 即
> $$
> AS=S\Lambda
> $$
> 这时候通过乘$S^{-1}$就能得到
> $$
> \begin{align}
> S^{-1}AS=\Lambda\\
> A=S\Lambda S^{-1}
> \end{align}
> $$

对于得到的第一个式子，实际上就是对一个矩阵A对角化的一个过程
对于得到的第二个式子，一方面这给出了分解矩阵A的新方法，现在就有三种分解A的方法
- 消元: $A=LU$ L是下三角矩阵，U是上三角矩阵
- 正交：$A=QR$，Q是正交矩阵，R是上三角矩阵
- 对角化：$A=S\Lambda S^{-1}$

另一方面这给出了一种求解A的幂的一种新的方法

另外，要注意的当矩阵A的特征值的$\lambda_i$互不相同，也就是说A必有n个线性线性独立的特征向量（这个lecture中没有给出证明，需要看课本），A才能对角化
而对于矩阵存在重复特征值时，则矩阵 A 不一定存在 n 个线性独立的特征向量，需要计算出具体的特征向量才可以判断
比如对于单位矩阵I来说，他的特征值都为1，但是代入方程$(I-\lambda I)x=0$求解特征向量的过程中，由于矩阵有n个自由变量，所以方程有n个特殊的解，而且是线性独立的


### A的幂
前面提到了，我们得到了$A=S\Lambda S^{-1}$ , 这给了我们一个求解A的幂的一个很简单的方法
$$
A^2=S\Lambda S^{-1}S\Lambda S^{-1}=S\Lambda (S^{-1}S)\Lambda S^{-1}=S\Lambda^2 S^{-1}
$$具体到$\Lambda$矩阵中每个元素而言，对角线的特征值也都取了平方
换句话说，实际上，$A^2x=A(Ax)=\lambda Ax=\lambda^2 x$，把$A^2$看做一个整体，对于这个整体来说，他的特征向量还是x没变，特征值变成了$\lambda^2$
事实上这个性质可能扩展到更高阶也同样成立，$A^k=S\Lambda^k S^{-1}$

### 差分方程
Difference equation(差分方程，也叫做Recurrence relation, 递推关系式)是一种递推地定义一个序列的方程式，即序列的每一项目被定义为前若干项的函数。
其中一阶差分方程是指递推式中，只包含前后的两项关系，例如 $x_n=kx_n$；而二阶差分方程是指递推式中，包含前后共计三项的关系，例如斐波那契数是由递推关系 $x_{n+2}=x_{n+1}+x_{n}$ 所定义的
使用矩阵的幂的分解公式，可以一阶对等比递推公式进行「拆解」简化，以求出通项公式

假设向量$u_k$满足差分方程$u_{k+1}=Au_k$,且已知首项$u_0$,可以用矩阵A的特征值和特征向量表达向量$u_k$的通项公式
对于$u_0$,可以写成空间内特征向量的线性组合的形式（特征向量之间线性无关）
$$
u_0 = c_1x_1+c_2x_2+ ...+c_nx_n
$$
$$u_1=Au_0 = c_1\textcolor{red}{Ax_1} +c_2\textcolor{red}{Ax_2}+ ...+c_n\textcolor{red}{Ax_n}=c_1\textcolor{red}{\lambda_1 x_1}+c_2\textcolor{red}{\lambda_2 x_2}+...c_n\textcolor{red}{\lambda_n x_n}
$$
$$
u_2=A^2u_o=c_1\lambda_1^2x_1+c_2\lambda_2^2x_2+...+c_n\lambda_n^2x_n
$$
$$
u_k=A^ku_o=c_1\lambda_1^kx_1+c_2\lambda_2^kx_2+...+c_n\lambda_n^kx_n
$$

如果用对角矩阵的形式写，实际上就是
$$
u_k=S\Lambda^kC
$$
其中S矩阵是特征向量矩阵，$\Lambda^k$为特征值矩阵，C为系数矩阵

### 斐波那契数列
下面来看斐波那契数列的例子，我们实际上都对斐波那契数列很熟悉了，0,1,1,2,3,5,8，,1,... 所满足的递推关系式$F_{k+2}=F_{k+1}+F_{k}$,这是一个二阶差分方程，这里用了一个小trick，通过加了一个方程，变成方程组，来变成一阶的差分方程，而方程组又能用矩阵表示，则有
$$
\left\{
\begin{array}{l}
    F_{k+2}=F_{k+1}+F_k \\
    F_{k+1}=F_{k+1} 
\end{array}
\right.
$$
$$
\begin{bmatrix}F_{k+2}\\F_k\end{bmatrix}=
\begin{bmatrix}1&1\\1&0\end{bmatrix}
\begin{bmatrix}F_{k+1}\\F_k\end{bmatrix}
$$
如果令$u_k=\begin{bmatrix}F_{k+1}\\F_k\end{bmatrix}$,那么有
$$
u_{k+1}=\begin{bmatrix}1&1\\1&0\end{bmatrix} u_k=Au_k
$$
通过以上的变换将二阶差分方程转换为一阶差分方程,然后就可以利用上面$u_k=S\Lambda^kC$的结论，求前面矩阵A的特征向量和特征值，求得$u_k$的通项公式，从而求得$F_k$的通项公式
先求特征向量和特征值
![Pasted image 20251004194940.png](/img/user/accessory/Pasted%20image%2020251004194940.png)
然后求得c1，c2
然后Fk就是c2那一项，因为uk的第一个分量是Fk+1,第二个分量是Fk
