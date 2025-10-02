---
{"created":"2025-10-02T12:45","updated":"2025-10-02T17:31","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 21 特征值和特征向量/","dgPassFrontmatter":true,"noteIcon":""}
---

### 特征值和特征向量Overview
依然是对于方阵来说的一个重要的主题，特征值和特征向量
这节课来学习什么是特征值和特征向量以及怎么求解特征值和特征向量，下节课讲应用
**什么是特征向量和特征值？**
我们都知道，对于Ax来说，实际上是对x做用了一个矩阵，这意味着是对x做了A这样的线性变换变成了Ax，而很多时候，给定一个矩阵A，x再经过A的变换之后的向量Ax是与向量x有一定夹角的，但是存在一些向量x，经过变换后的Ax与x平行，这一些向量就叫**特征向量**
什么叫平行？就是Ax与x有一定的倍数关系，用方程来表示就是$Ax=\lambda x$.
$\lambda$是一个系数，这个系数就叫做**特征值**

**Example 1: 我们来看一下矩阵A为投影矩阵P的情况**
对于普通向量b来说，在投影矩阵的作用下会得到Pb落在投影平面中，显然普通向量b并不是特征向量
只有当b本身就已经处于投影平面中，Pb才可能和b是同向的，即Pb=1b，因此，投影平面中的所有向量都是投影矩阵的特征向量，特征值为1
除此之外，投影平面的法向量，即我们所说的误差e，Pe=0，任何向量与0向量都是平行的，因此，投影平面的所有法向量也同样是投影矩阵的特征向量，特征值为0

**Example 2: $A=\begin{bmatrix}0& 1\\1& 0\end{bmatrix}$**
$A=\begin{bmatrix}0& 1\\1& 0\end{bmatrix}$是一个二阶置换矩阵，经过这个矩阵处理的二维向量x，其元素会互相交换，即$\begin{bmatrix}x_1\\x_2\end{bmatrix}$会变成$\begin{bmatrix}x_2\\x_1\end{bmatrix}$ ,如果想让交换前后的两个向量平行
- A有特征值为1的特征向量(即经过矩阵交换元素前后仍然不变)，特征向量为$\begin{bmatrix}1\\1\end{bmatrix}$
- A有特征值为-1的特征向量(即经过矩阵交换元素前后方向相反)，特征向量为$\begin{bmatrix}1\\-1\end{bmatrix}$

**这里老师提前讲了一些特征值的例子**
- 矩阵对角线元素之和成为矩阵的迹，矩阵的迹等于特征值之和
- 对称矩阵其特征向量互相垂直

$$
\displaylines{
设\ \lambda_1\ 和\ \lambda_2\ 是对称矩阵\ A\ 的两个不同的特征值，对应的特征向量分别为\ x_1\ 和\ x_2\\
\therefore Ax_1=\lambda_1x_1,\ Ax_2=\lambda_2x_2\\
对\ Ax_1=\lambda_1x_1\ 左乘\ x_2^T\ 得：\quad x_2^TAx_1=\lambda_1x^T_2x_1\qquad\\
x^T_2Ax_1=(Ax_2)^Tx_1=(\lambda_2x_2)^Tx_1=\lambda_2x^T_2x_1=\lambda_1x^T_2x_1\\
也即\ (\lambda_1-\lambda_2)x^T_2x_1=0\\
又\because \lambda_1\ne\lambda_2\ \\\therefore x^T_2x_1=0\\
\therefore 对称矩阵的特征向量正交。
}
$$



### 求解特征值和特征向量 Ax=λx
对于方程$Ax=\lambda x$来说，有两个未知数，我们需要一个技巧从一个方程中解出两个未知数
首先需要移项得$(A-\lambda I)x=0$ 
如果这个新方程有非零解x(因为要求特征向量不可为零向量)，需要$A-\lambda I$为奇异矩阵，用行列式来说的话，就是行列式为0，即$det(A-\lambda I)=0$
这样一来就没有x了，这个$det(A-\lambda I)=0$也叫做特征方程，求解特征值λ然后带回$(A-\lambda I)x=0$ 求解零空间就是特征向量
**example: $A=\begin{bmatrix}3& 1\\1& 3\end{bmatrix}$**
![bf6ade716b45e492d080b89746ea39a6.jpg|400](/img/user/accessory/bf6ade716b45e492d080b89746ea39a6.jpg)
这里有一个重要的点事在求解$(3-\lambda)^2-1=0$的时候所得到的$\lambda^2-6\lambda+8=0$, 其中一次项系数-6和矩阵的迹3+3有关，常数项的8与矩阵A的行列式有关
矩阵的特征值之和等于矩阵的迹
矩阵的特征值之积等于矩阵的行列式
这两条性质的证明需要利用到韦达定理，韦达定理的证明
$$
\displaylines{
给定方程\ a_nx^n+a_{n-1}x^{n-1}+a_{n-2}x^{n-2}+\cdots+a_1x+a_0=0\ \ \ (1)\\
假设上述方程的有根\ x_1,x_2,\cdots,x_n，那么给定方程可改写为如下形式：\\
\qquad\qquad a_n(x-x_1)(x-x_2)(x-x_3)\cdots(x-x_n)=0\ \ \ \ \qquad(2)\\
根据初高中多项式展开的知识可知：\\对于所有括号只取第一项\ x，则有\ C^n_nx^n=x^n\\所以\ x_n\ 的系数为\ C^n_na_n=a_n，与\ (1)\ 中相符。\\同理，所有括号中我们对其中\ n-1\ 个取第一项，那么\ x^{n-1}\ 的系数为 \\-a_n(x_1+x_2+...+x_n)，也即\ (1)\ 中 \ a_{n-1}=-a_n(x_1+x_2+...+x_n)。\\
\therefore -\frac{x^{n-1}的系数}{x^n的系数}=-\frac{a_{n-1}}{a_{n}}=-\frac{-a_n(x_1+x_2+...+x_n)}{a_n}=(x_1+...+x_n)
\\对应到一元二次方程\ ax^2+bx+c=0\ 里即\ x_1+x_2=-\frac{b}{a}。\\
同理，所有括号中我们都取第二项，那么我们将得到常数项\ (-1)^na_nx_1x_2...x_n\\
也即\ (1)\ 中\ a_0=(-1)^na_nx_1x_2...x_n。\\
\therefore \frac{常数项}{x^n的系数}=\frac{a_0}{a_n}=\frac{(-1)^na_nx_1x_2...x_n}{a_n}=(-1)^nx_1x_2...x_n\\
\therefore  (-1)^n\frac{常数项}{x^n的系数}=x_1x_2...x_n\\
对应到一元二次方程\ ax^2+bx+c=0\ 里也即\ x_1x_2=(-1)^2\frac{c}{a}=\frac{c}{a}
}
$$
证明矩阵的特征值之和等于矩阵的迹
$$
\displaylines{
证明：设\ A=\begin{bmatrix}a_{11}&a_{12}&a_{13}&\cdots&a_{1n}\\
a_{21}&a_{22}&a_{23}&\cdots&a_{2n}\\a_{31}&a_{32}&a_{33}&\cdots&a_{3n}\\
\vdots&\vdots&\vdots&\ddots&\vdots\\
a_{n1}&a_{n2}&a_{n3}&\cdots&a_{nn}\end{bmatrix}，则\\
\det(A-\lambda I)=\begin{vmatrix}a_{11}-\lambda &a_{12}&a_{13}&\cdots&a_{1n}\\
a_{21}&a_{22}-\lambda&a_{23}&\cdots&a_{2n}\\a_{31}&a_{32}&a_{33}-\lambda&\cdots&a_{3n}\\
\vdots&\vdots&\vdots&\ddots&\vdots\\
a_{n1}&a_{n2}&a_{n3}&\cdots&a_{nn}-\lambda\end{vmatrix}\\
按行列式的定义可以将其展开成\ n!\ 项非零行列式相加的形式\\除了\ 
(a_{11}-\lambda)(a_{22}-\lambda)\cdots(a_{nn}-\lambda)\ 项含有所有\ (a_{11}-\lambda),(a_{22}-\lambda),\cdots,(a_{nn}-\lambda)\\
的\ n\ 个因子外，其他项最多仅含\ (a_{11}-\lambda)(a_{22}-\lambda)...(a_{nn}-\lambda)\ 这些因子中的\ n-2\ 个\\比如\ a_{12}a_{21}(a_{33}-\lambda)...(a_{nn}-\lambda)。\\
\therefore \lambda^n\ 和\ \lambda^{n-1} 只可能来自包含所有因子的\ (a_{11}-\lambda)(a_{22}-\lambda)...(a_{nn}-\lambda)\ 项\\
\therefore \det(A-\lambda I)=(a_{11}-\lambda)(a_{22}-\lambda)\cdots(a_{nn}-\lambda)+\cdots\\=(-1)^n\lambda^{n}+(-1)^{n-1}(a_{11}+a_{22}+\cdots+a_{nn})\lambda^{n-1}+\cdots\\
于是\ 可知\det(A-\lambda I)\ 的\ \lambda^{n-1}\ 的系数为(-1)^{n-1}(a_{11}+a_{22}+...+a_{nn})
\\令\ \det(A-\lambda I)=0\ 可得特征方程，根据根与系数关系可知：\\特征值之和=特征方程的根之和=-\frac{\lambda^{n-1}项的系数}{\lambda^{n}项的系数}\\
综上，\lambda_1+\lambda_2+...+\lambda_n(特征值之和)=(a_{11}+a_{22}+...+a_{nn})
}
$$
证明矩阵的特征值之积等于矩阵的行列式
$$
\displaylines{
证明：设\ A=\begin{bmatrix}a_{11}&a_{12}&a_{13}&\cdots&a_{1n}\\
a_{21}&a_{22}&a_{23}&\cdots&a_{2n}\\a_{31}&a_{32}&a_{33}&\cdots&a_{3n}\\
\vdots&\vdots&\vdots&\ddots&\vdots\\
a_{n1}&a_{n2}&a_{n3}&\cdots&a_{nn}\end{bmatrix}，则\\
\det(A-\lambda I)=\begin{vmatrix}a_{11}-\lambda &a_{12}&a_{13}&\cdots&a_{1n}\\
a_{21}&a_{22}-\lambda&a_{23}&\cdots&a_{2n}\\a_{31}&a_{32}&a_{33}-\lambda&\cdots&a_{3n}\\
\vdots&\vdots&\vdots&\ddots&\vdots\\
a_{n1}&a_{n2}&a_{n3}&\cdots&a_{nn}-\lambda\end{vmatrix}\\
按行列式的定义可以将其展开成\ n!\ 项非零行列式相加的形式\\这\ n!\ 项中必然存在不包含
任何诸如\ (a_{11}-\lambda),(a_{22}-\lambda),\cdots,(a_{nn}-\lambda)的因子的非零行列式\\
这样的行列式自然也就不包括\ \lambda\ 项。\\
\det(A-\lambda I)=\underbrace{(a_{11}-\lambda)(a_{22}-\lambda)\cdots(a_{nn}-\lambda)}_{(-1)^n\lambda^{n}+(-1)^{n-1}(a_{11}+a_{22}+...+a_{nn})\lambda^{n-1}}+\underbrace{(\cdots)}_{其他项,包含\ \lambda^{n-2}\ 到\ \lambda\ 的项}+\underbrace{(C)}_{常数项,不包含任何\ \lambda\ 项}\\
于是可知\ \lambda^n\ 的系数为\ (-1)^n， 令\ \lambda=0，则有\ \det(A)=C。\\
令\ \det(A-\lambda I)=0\ 可得特征方程，根据根与系数的关系可知：\\
特征值之积=特征方程的根之积=(-1)^n\frac{常数项}{\lambda^n项的系数}\\
也即\ \lambda_1\lambda_2...\lambda_3(特征值之积)=\det(A)
}
$$

第二个点在于如果比较这个例子$A=\begin{bmatrix}3& 1\\1& 3\end{bmatrix}$和前面的例子$A=\begin{bmatrix}0& 1\\1& 0\end{bmatrix}$ A, 特征值，特征向量是有一定关系的
对于A来说，这个例子的A是上个例子A+3I，而特征值刚好差3，特征向量不变
这是很容易证明的
$$
If \space Ax=\lambda x
$$
$$
(A+3I)x=\lambda x+3x=(\lambda+3)x
$$

**example 2: $Q=\begin{bmatrix}0& -1\\1& 0\end{bmatrix}$**
这是逆时针旋转90度的一个矩阵，前面也提到过，矩阵是线性变换完基的位置
其中$\begin{cases}\lambda_1+\lambda_2& =0\\\lambda_1\cdot\lambda_2& =1\end{cases}$,$\det(Q-\lambda I)=\begin{vmatrix}\lambda& -1\\1& \lambda\end{vmatrix}=\lambda^2+1=0$可得到$\lambda_1=i, \lambda_2=-i$
实际上，如果矩阵是对称的或者说接近对称的，那么特征值一般就是实数，如果越不对称，特征值往往是纯虚数
换句话说，就是实数特征值让特征向量伸缩而虚数让其旋转！
到现在为止我们看到，对于好的矩阵（最上面提到的置换矩阵）有实特征值及正交的特征向量，对于不好的矩阵（旋转矩阵）有纯虚的特征值

**example 3: $A=\begin{bmatrix}3& 1\\0& 3\end{bmatrix}$**
这是一个三角矩阵
$\det(A-\lambda I)=\begin{vmatrix}3-\lambda& 1\\0& 3-\lambda\end{vmatrix}=(3-\lambda)^2=0$,$\lambda _1=3,\lambda_2=3$
两个特征值会得到同一个方程，同样的特征向量
这个矩阵是一个退化矩阵，我们只能找到一个方向上的特征向量而不是两个。对于一个退化矩阵，重复的特征值在特殊情况下可能导致特征向量的短缺
