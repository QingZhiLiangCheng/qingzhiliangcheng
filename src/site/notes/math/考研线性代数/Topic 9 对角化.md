---
{"created":"2025-12-01T11:15","updated":"2026-05-28T21:03","dg-publish":true,"permalink":"/math/考研线性代数/Topic 9 对角化/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2025-12-01T11:15","updated":"2026-05-28T21:03"}}
---

### 矩阵对角化
之前学习了求解特征向量和特征值的方法，而特征向量和特征值的一个作用就是对角化矩阵
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

另外，要注意的当矩阵A的特征值的$\lambda_i$互不相同，也就是说A必有n个线性线性独立的特征向量，A才能对角化
其实根据定义 S要求可逆 那就一定要求特征向量线性无关
那为什么矩阵A的特征值的$\lambda_i$互不相同 那特征向量就一定线性无关呢？
![Pasted image 20260528204158.png\|500](/img/user/accessory/Pasted%20image%2020260528204158.png)
![Pasted image 20260528204225.png\|500](/img/user/accessory/Pasted%20image%2020260528204225.png)

而对于矩阵存在重复特征值时，则矩阵 A 不一定存在 n 个线性独立的特征向量，需要计算出具体的特征向量才可以判断
比如对于单位矩阵I来说，他的特征值都为1，但是代入方程$(I-\lambda I)x=0$求解特征向量的过程中，由于矩阵有n个自由变量，所以方程有n个特殊的解，而且是线性独立的


### A的幂
前面提到了，我们得到了$A=S\Lambda S^{-1}$ , 这给了我们一个求解A的幂的一个很简单的方法
$$
A^2=S\Lambda S^{-1}S\Lambda S^{-1}=S\Lambda (S^{-1}S)\Lambda S^{-1}=S\Lambda^2 S^{-1}
$$具体到$\Lambda$矩阵中每个元素而言，对角线的特征值也都取了平方
换句话说，实际上，$A^2x=A(Ax)=\lambda Ax=\lambda^2 x$，把$A^2$看做一个整体，对于这个整体来说，他的特征向量还是x没变，特征值变成了$\lambda^2$
事实上这个性质可能扩展到更高阶也同样成立，$A^k=S\Lambda^k S^{-1}$


---
### 差分方程 (了解)
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

### 斐波那契数列 (了解)
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


### 微分方程组 (了解)
两个变量$u_1,u_2$随着时间t变换(即它们都是关于t的函数，他们的变化率如下
$$
\left\{
\begin{aligned}
&\frac{du_1}{dt}=-u_1+2u_2\\
&\frac{du_2}{dt}=u_1-2u_2
\end{aligned}
\right.
$$
在初始状态t=0时，$u_1=1,u_2=0$
由于$u_1$的变化率是$-u_1+2u_2$,$u_2$的变化率是$u_1-2u_2$,所以两者的变化是相互影响的，结合初始值，在t=0时，$u_1$的瞬时变化率是-1，可以推测出$u_1$可能是随着时间的增加而减少的，同样，$u_2$的瞬时变化率是1，推测$u_2$可能随着时间的增加而增大
上面这两个变化率等式都是一阶线性微分方程
> [!WARNING] 补充说明
> 微分方程(Differentional Equation)是一个包含未知函数及其导数的方程
> 所谓一阶(First-Order)是指方程中未知函数的最高阶导数的阶数是1.
> 所谓线性(Linear)指的是未知函数及其所有导数都是“一次方”的形式

对于微分方程的解实际上是一个指数的形式，对于指数和系数，实际上就是一个线性代数问题；换句话说，如果一个微分方程处理的好的话，可以使用线性代数的方式求解
### 使用线性代数求解微分方程组 (了解)
令$u=\begin{bmatrix}u_1\\u_2\end{bmatrix}$,上述的微分方程组就能转换为矩阵的形式
$$
\frac{du}{dx}=\begin{bmatrix}-1&2\\1&-2\end{bmatrix}u
$$
在t=0时.$u(0)=\begin{bmatrix}u_1\\u_2\end{bmatrix}=\begin{bmatrix}1\\0\end{bmatrix}$
转换后的等式，是一个一阶常系数齐次线性方程
> [!warning] 补充说明
> 常系数是指乘以未知函数 y 和它的导数 y′ 的系数是常数
> 齐次是指对于方程$y′+ay=q(t)$中$q(t)$为0的情况

对于上面这种一阶常系数齐次方程，它的解$u(t)$为指数的形式
> [!warning] 补充说明
> 对于这种$\frac{dy}{dt}=-ay$的形式，回想一下微积分中学过的所有函数，能够达到导数与自身大小成正比的，好像只有指数函数$e^t,e^{kt}$

对于这个微分方程的通解形式就是$u(t)=c_1e^{\lambda_1t}x_1+c_2e^{\lambda_2t}x_2$
其中$\lambda_1,\lambda_2$是矩阵A的特征值，$x_1,x_2$是矩阵A的特征向量
在课上，教授并没有直接推导这个通解，而是代入特解进行验证
上面的这个通解实际上是有两个向量$c_1e^{\lambda_1t}x_1$和$c_2e^{\lambda_2t}x_2$线性组合构成的，我们可以代入其中一个特解进行验证
对于$\frac{du}{dt}$有$\frac{d}{dt}(e^{\lambda_1t}x_1)=\lambda_1(e^{\lambda_1t}x_1)$，对于Au有$Au=A(e^{\lambda_1t}x_1)$
消去公共部分得$Ax_1=\lambda_1x_1$,而这是特征向量的定义
值得注意的是，微分方程通解的结果$u(t)=c_1e^{\lambda_1t}x_1+c_2e^{\lambda_2t}x_2$可以与查分方程的通解$u=c_1\lambda_1^kx_1+c_2\lambda_2^kx_2$对比，其中，微分方程通解的特征值是指数形式的，而差分方程中通解特征值是幂形式的

接下来的任务就是求解A的特征值和特征向量
$$
A=\begin{bmatrix}-1&2\\1&-2\end{bmatrix}
$$
其实这个一眼就能看出来，这是一个奇异矩阵，或者说是一个不可逆矩阵，0必然是该矩阵的一个特征值，根据迹为-3，能得到另一个特征值为-3(当然也可以通过$det(A-\lambda I)$求解)
最终，得到的特征值和特征向量为
$$
\begin{aligned}
&\lambda_1=0,x_1=\begin{bmatrix}2\\1\end{bmatrix}\\
&\lambda_2=-3,x_2=\begin{bmatrix}1\\-1\end{bmatrix}
\end{aligned}
$$
得到
$$
\begin{aligned}
u(t)&=c_1e^{0t}\begin{bmatrix}2\\1\end{bmatrix}+c_2e^{-3t}\begin{bmatrix}1\\-1\end{bmatrix}\\
&=c_1\begin{bmatrix}2\\1\end{bmatrix}+c_2e^{-3t}\begin{bmatrix}1\\-1\end{bmatrix}
\end{aligned}
$$
代入初始状态，就能求得系数
$$
c_1\begin{bmatrix}2\\1\end{bmatrix}+c_2\begin{bmatrix}1\\-1\end{bmatrix}=\begin{bmatrix}1\\0\end{bmatrix}
$$
转换成方程组实际上就是
$$
\left\{
\begin{align}
&2c_1+c_2=1\\
&c_1-c_2=0
\end{align}
\right.
$$
解得$c_1=\frac{1}{3},c_2=\frac{1}{3}$
所以微分方程的通解为$u(t)=\frac{1}{3}\begin{bmatrix}2\\1\end{bmatrix}+\frac{1}{3}e^{-3t}\begin{bmatrix}1\\-1\end{bmatrix}$
即
$$
\left\{
\begin{align}
&u_1=\frac{2}{3}+\frac{1}{3}e^{-3t}\\
&u_2=\frac{1}{3}-\frac{1}{3}e^{-3t}
\end{align}
\right.
$$


### 变化趋势与稳定性 (了解)
观察上面微分方程的通解，可知它随时间的变化情况：
- 开始状态$u(0)=\begin{bmatrix}1\\0\end{bmatrix}$,即$u_1=1,u_2=0$
- 随着时间的增大$u_1$的值减少，$u_2$的值增大
- 当$t \to ∞$ 时，$u(∞)$趋于稳态$\frac{1}{3}\begin{bmatrix}2\\1\end{bmatrix}$，即$u_1 \to \frac{2}{3},u_2 \to \frac{1}{3}$
在时间增大的全过程中，$u_1$和$u_2$从开始的$u_1=1,u_2=0$变成了后面的$u_1 \to \frac{2}{3},u_2 \to \frac{1}{3}$,可以将次变化看作是$u_1$的部分值 **流向了** 了$u_2$, 并最终趋于稳态！
在这里，稳定性是和系数矩阵A的特征值有关的
稳定性：如果所有的特征值的实数部分都小于零$Re(\lambda)<0$,当$t\to ∞$的时候，函数会趋于0
稳态：如果其中一个特征值的实数部分等于零$Re(\lambda_i)=0$,而其余的特殊值的实数部分都小于零$Re(\lambda)<0$,则当$t\to∞$的时候，函数会趋于一个数$c_ix_i$
这其实就是因为特征值是e上面指数的原因
为什么不考虑虚部？其实也是因为这是e上的指数的原因，这是因为将虚数映射到直角坐标系后，（在几何形状上）它是绕着一个单位圆变化的

我问了一下AI，这与数学中的欧拉公式有关
$$
e^{i\theta}=cos(\theta)+isin(\theta)
$$
而这个映射到直角坐标系中，一个虚数$z=a+bi$可以看作是复平面中的点$(a,b)$,所以对于$e^{i\theta}$来说，实际上就是$(cos\theta,sin\theta)$这个单位圆
对于一个负数来说，趋向于无穷大，实际上就是这个点沿着任意方向远离(0,0)点吧？ 所以说，这里算的就是在直角坐标轴上的距离
也就是说，在我们的解中，对于一个$e^{a+bi}$的值，实际上就是$e^a \times e^{bi}$,后面虚数的这一部分实际上只管震荡和旋转

### 对角化解耦 (了解)
实际上在上面求解系数的过程中
$$
c_1\begin{bmatrix}2\\1\end{bmatrix}+c_2\begin{bmatrix}1\\-1\end{bmatrix}=\begin{bmatrix}1\\0\end{bmatrix}
$$
我们算的是
$$
\begin{bmatrix}2&1\\1&-1\end{bmatrix}
\begin{bmatrix}c_1\\c_2\end{bmatrix}
=u(0)
$$
其中前面的矩阵是矩阵A的特征向量所组成的矩阵S矩阵
也就是说u(0)是特征向量的线性组合，同样的，其他的u也是特征向量的特征组合
也就是说实际上u=Sv，其中v是一个随时间变化的系数向量
矩阵A的作用相当于将$u_1$和$u_2$耦合了，这可能让求解的过程变得更复杂，一个解耦的好的方法是利用矩阵的对角化，即将u表示为A的特征向量的某种线性组合，通过换元可以得到变量之间解耦的微分方程，便于求解。
通过换元后，从原来的对u求导，变成了对v求导
$$
\begin{aligned}
&\frac{du}{dt}=Au\\
&\frac{d(Sv)}{dt}=S\frac{dv}{dt}=ASv\\
&\frac{dv}{dt}=S^{-1}ASv
\end{aligned}
$$
而$S^{-1}AS$就是对角矩阵$\Lambda$，也就是说
$$
\frac{dV}{dt}=\Lambda v
$$
而对角化，完成了解耦，这是因为对角矩阵的特征，对角矩阵只在对角线上存在元素，其他位置的元素都为0
$$
\begin{bmatrix}
  \lambda_1 & 0 & \dots & 0 \\
  0 & \lambda_2 & \dots & 0 \\
  \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & \dots & \lambda_n
\end{bmatrix}
$$
对角矩阵与一个列向量相乘
$$
\begin{bmatrix}
  \lambda_1 & 0 & \dots & 0 \\
  0 & \lambda_2 & \dots & 0 \\
  \vdots & \vdots & \ddots & \vdots \\
  0 & 0 & \dots & \lambda_n
\end{bmatrix}
\begin{bmatrix}
x_1\\x_2\\ \vdots \\x_n
\end{bmatrix}
=\begin{bmatrix}
\lambda_1x_1\\ \lambda_2x_2\\ \vdots \\ \lambda_nx_n
\end{bmatrix}
$$
得到的结果是列向量中每个元素中的变量，都包含原来列向量的相应行对应的元素，换句话说，对于上面的换元，如果将其转换为方程组的形式，每个微分方程中实际上只有一个变量，而不是像原来的每个方程都包含$u_1,u_2$

### 指数矩阵 (了解)
对于换元后所得到的微分方程$\frac{dv}{dt}=\Lambda v$，通解为
$$
v(t)=e^{\Lambda t}v(0)
$$
 

 事实上在我们进行了解耦之后，实际上就是n个单独的微分方程了 $$
\begin{bmatrix}\frac{dv_1}{dt}\\\frac{dv_2}{dt}\end{bmatrix}
=\begin{bmatrix}\lambda_1&0\\0&\lambda_2\end{bmatrix}
\begin{bmatrix}v_1\\v_2\end{bmatrix}
 $$
这个微分方程的形式都是$\frac{dv}{dt}=\lambda_i v$的形式，而我们又知道一般的方程的解，推到过程如下图
![6d4367d105b9ab9e9c5caf58849902ec.jpg\|350](/img/user/accessory/6d4367d105b9ab9e9c5caf58849902ec.jpg)
所以说，每个方程的解应该是$v(t)=v(0)e^{\lambda_i}t$ 实际上,写成矩阵的形式就是
$$
\begin{bmatrix}v_1(t)\\v_2(t)\end{bmatrix}=\begin{bmatrix}v_1(0)\\v_2(0)\end{bmatrix}\begin{bmatrix}e^{\lambda_1t}&0\\0&e^{\lambda_2t}\end{bmatrix}
$$
即$v(t)=e^{\Lambda t}v(0)$
那么对于换元前的微分方程$\frac{du}{dt}=Au$
$$
\begin{align}
&v(t)=e^{\Lambda t}v(0)\\
&换元关系:v(t)=S^{-1}u(t)\\
&\to S^{-1}u(t)=e^{\Lambda t}S^{-1}u(0)\\
&\to u(t)=Se^{\Lambda t}S^{-1}u(0)
\end{align}
$$
$Se^{\Lambda t}S^{-1}$等不等于$e^{At}$?
这用到泰勒公式
$$
e^x=1+x+\frac{x^2}{2}+\frac{x^3}{6}+...+\frac{x^n}{n!}
$$
对于矩阵也同样适用，即
$$
e^{At}=I+At+\frac{({At})^2}{2}+\frac{({At})^3}{6}+...+\frac{({At})^n}{n!}
$$
A化为$\Lambda$
$$
e^{At}=SS^{-1}+S\Lambda S^{-1}t+\frac{S\Lambda^2 S^{-1}{t}^2}{2}+\frac{S\Lambda^3 S^{-1}{t}^3}{6}+...+\frac{S\Lambda^n S^{-1}{t}^n}{n!}
$$
提取$S$和$S^{-1}$
$$
e^{At}=S(I+\Lambda t+\frac{\Lambda^2{t}^2}{2}+\frac{\Lambda^3 {t}^3}{6}+...+\frac{\Lambda^n {t}^n}{n!})S^{-1}
$$
而中间括号的部分就是$e^{\Lambda t}$的展开式
所以$u(t)=Se^{\Lambda t}S^{-1}u(0)=e^{At}u(0)$


