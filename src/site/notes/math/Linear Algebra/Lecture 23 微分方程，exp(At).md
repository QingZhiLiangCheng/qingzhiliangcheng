---
{"created":"2025-10-06T13:27","updated":"2025-10-09T18:32","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 23 微分方程，exp(At)/","dgPassFrontmatter":true,"noteIcon":""}
---


### 微分方程组
两个变量$u_1,u_2$随着时间t变换(即它们都是关于t的函数，他们的变化率如下)
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
### 使用线性代数求解微分方程组
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
值得注意的是，微分方程通解的结果$u(t)=c_1e^{\lambda_1t}x_1+c_2e^{\lambda_2t}x_2$可以与上一个Lecture中查分方程的通解$u=c_1\lambda_1^kx_1+c_2\lambda_2^kx_2$对比，其中，微分方程通解的特征值是指数形式的，而差分方程中通解特征值是幂形式的

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


### 变化趋势与稳定性
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

### 对角化解耦
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

### 指数矩阵
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
![6d4367d105b9ab9e9c5caf58849902ec.jpg|350](/img/user/accessory/6d4367d105b9ab9e9c5caf58849902ec.jpg)
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
