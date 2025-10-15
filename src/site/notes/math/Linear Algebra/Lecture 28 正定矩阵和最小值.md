---
{"created":"2025-10-15T09:40","updated":"2025-10-15T11:35","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 28 正定矩阵和最小值/","dgPassFrontmatter":true,"noteIcon":""}
---

### 判断正定矩阵
下面是四个判定矩阵是正定矩阵的充分必要条件，其中满足任意一个就能证明是正定矩阵
- 特征值$\lambda_i$全为正数
- 除了矩阵自身的行列式detA为正数，沿着对角线向上的n-1个子方阵的行列式能都为正数
- 主元均为正数
- 对于任意向量x(除了x=0),满足$x^TAx$为正数

以一个2 by 2矩阵为例
$$
A=\begin{bmatrix}a & b\\b&c\end{bmatrix}
$$
- 特征值角度: $\lambda_1>0, \lambda_2>0$
- 行列式角度: $ac-b^2>0$, a>0
- 主元，消元法角度: $A=\begin{bmatrix}a&b\\0&\frac{ac-b^2}{a}\end{bmatrix}$,a>0, $\frac{ac-b^2}{a}>0$
- $x^TAx>0$

正定矩阵在这里把特征值，行列式，主元，消元法等知识都联系起来了！

比如判断矩阵$A=\begin{bmatrix}2&6\\6&18\end{bmatrix}$
从$x^TAx$等式的角度来看
$$
\begin{aligned}
x^TAx=\begin{bmatrix}x_1&x_2\end{bmatrix}
\begin{bmatrix}2&6\\6&18\end{bmatrix}
\begin{bmatrix}x_1\\x_2\end{bmatrix}
\end{aligned}
$$
先计算后两个矩阵，属于对x左乘行变换，即
$$
\begin{aligned}
&=\begin{bmatrix}x_1&x_2\end{bmatrix}
\begin{bmatrix}2x_1+6x_2\\6x_1+18x_2\end{bmatrix}\\
&=2x_1^2+12x_1x_2+18x_2^2
\end{aligned}
$$
需要注意的点是，看最后这个等式的系数和原矩阵A的关系，发现是$f(x_1,x_2)=ax_1^2+2bx_1x_2+cx_2^2$，可以将这个等式看作是矩阵A的<font color="#f79646">二次形式</font>
如果想知道其正负，一个好的方法是进行配方，$2x_1^2+12x_1x_2+18x_2^2=2(x_1+3x_2)^2 \geq 0$ , 但是注意其中当式子为0时，$x_1，x_2$有好多解，不满足除x=0外的任意x都有等式大于0，所以原矩阵A不是正定矩阵。如果看行列式的话，这个矩阵的行列式为0，只有一个主元，这种叫做半正定矩阵，不是正定矩阵

### 最小值
对于一下矩阵$\begin{bmatrix}2&6\\6&7\end{bmatrix}$
其二次形式为$f(x,y)=2x^2+12xy+7y^2$将其配方为$f(x,y)=2(x+3y)^2-11y^2$,并不是对任意的(x,y)都为正数，这个矩阵不是正定矩阵
该函数的集合图像如下
![Pasted image 20251015110825.png|300](/img/user/accessory/Pasted%20image%2020251015110825.png)
这是一个马鞍面，有一种鞍点，在不同方向上观察，会呈现出不同的性质，很容易看出来，一个鞍点是某个方向上的极大值，但也是某个方向上的极小值，最容易观测的方向就是沿着特征向量的方向。

再看矩阵$\begin{bmatrix}2&6\\6&20\end{bmatrix}$
其二次形式为$f(x,y)=2x^2+12xy+20y^2$将其配方为$f(x,y)=2(x+3y)^2+2y^2$,是正定矩阵
他的函数图像像一个碗口一样
![Pasted image 20251015113224.png](/img/user/accessory/Pasted%20image%2020251015113224.png)

如果从几何性质来看，上面的马鞍面做横截面得到的是一个双曲线，而现在的我碗口得到的是一个椭圆
实际上，椭圆跟正定性有关，而双曲线意味着非正定性

其实我们之前在微积分中，函数的最小值是通过一阶导数和二阶导数来算的，特别是像上面这种二元的函数，我们算的是偏导
而在线性代数中，所有的二阶导$f_{xx},f_{xy},f_{yx},f_{yy}$构成一个二阶矩阵
$$
\begin{bmatrix}
f_{xx}&f_{xy}\\
f_{xy}&f_{yy}
\end{bmatrix}
$$
该矩阵被称为黑塞矩阵，由于$f_{xy}=f_{yx}$所以这个矩阵是一个对称矩阵
当这个矩阵为正定矩阵时，函数具有极小值