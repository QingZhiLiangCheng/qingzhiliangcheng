---
{"created":"2026-05-27T22:02","updated":"2026-06-01T10:13","dg-publish":true,"permalink":"/math/考研线性代数/Topic 12 二次型及其标准形/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2026-05-27T22:02","updated":"2026-06-01T10:13"}}
---

### 二次型相关概念
**二次型及其矩阵表示**
二次型其实就是就是微积分中的一个多元函数，这个多元函数的特点是：每一项都是二次的 有平方项 有混合项
$$f(x_1, x_2, x_3) = x_1^2 + 5x_2^2 - 4x_3^2 - 2x_1x_2 + 6x_2x_3$$
这样的多元函数在代数中的语言叫 二次型！像上面的这个例子有三个自变量 就叫做 三元二次型
对于这个多元函数 怎么用多元函数的语言来表示？ -- 三个矩阵的乘法
$$
\begin{align}
&= \begin{bmatrix} x_1 & x_2 & x_3 \end{bmatrix} 
\begin{bmatrix}
 1 & -1 &  0 \\
-1 &  5 &  3 \\
 0 &  3 & -4
\end{bmatrix}
\begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix}\\
&=x^TAx
\end{align}
$$
中间的这个矩阵叫做二次型矩阵 规律就是 平方项系数排在了主对角线上 混合项系数倍2除放在对应的位置上
具体是怎么化简到矩阵乘法的形式的 -- 因式分解 将$x_1x_2$平均分开 一项和$x_1$组合 一项和$x_2$组合
$$
\begin{aligned}
& x_1^2 + 5x_2^2 - 4x_3^2 - 2x_1x_2 + 6x_2x_3 \\
= \,& x_1^2 - x_1x_2 - x_1x_2 + 5x_2^2 + 3x_2x_3 + 3x_2x_3 - 4x_3^2 \\
= \,& x_1(x_1 - x_2) + x_2(-x_1 + 5x_2 + 3x_3) + x_3(3x_2 - 4x_3) \\
= \,& \begin{bmatrix} x_1 & x_2 & x_3 \end{bmatrix}
\begin{bmatrix}
x_1 - x_2 \\
-x_1 + 5x_2 + 3x_3 \\
3x_2 - 4x_3
\end{bmatrix}\\
=& \begin{bmatrix} x_1 & x_2 & x_3 \end{bmatrix} 
\begin{bmatrix}
 1 & -1 &  0 \\
-1 &  5 &  3 \\
 0 &  3 & -4
\end{bmatrix}
\begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix}
\end{aligned}
$$

能满足$x^TAx=f(x_1,x_2,x_3)$的A有很多个  但是对称矩阵只有一个 只有这个对称矩阵叫做二次型！

**标准形**
二次型只有平方项 没有混合项 -- 标准形
那对应的二次型矩阵就是个对角矩阵！
$$
\begin{aligned}
& x_1^2 + 5x_2^2 - 2x_3^2 \\
& \Lambda = \begin{bmatrix} 1 & & \\ & 5 & \\ & & -2 \end{bmatrix} \\
\end{aligned}
$$
**规范形**
如果标准型的平方项系数只有三种可能：+1，-1，0 -- 这样的标准形又叫规范形
$$
x_1^2 + x_2^2 + x_3^2 \quad , \quad x_1^2 + x_2^2 - x_3^2\quad , \quad x_1^2-x_2^2
$$


**正惯性指数 负惯性指数**
标准型 正平方项和负平方项的个数！

**二次型的秩**
矩阵A的秩 就叫 二次型的秩

**坐标变换 线性变换**
将自变量中的x 换成y -> x的多元函数 换成 y的多元函数
$$
\begin{aligned}
& \begin{cases}
x_1 = c_{11}y_1 + c_{12}y_2 + c_{13}y_3 \\
x_2 = c_{21}y_1 + c_{22}y_2 + c_{23}y_3 \\
x_3 = c_{31}y_1 + c_{32}y_2 + c_{33}y_3
\end{cases}
\quad |C| \neq 0
\end{aligned}
$$
坐标变换只有一个要求 |C|≠0
用代数的语言来描述 就是矩阵乘法
$$
\begin{aligned}
\begin{bmatrix} x_1 \\ x_2 \\ x_3 \end{bmatrix} &= 
\begin{bmatrix}
c_{11} & c_{12} & c_{13} \\
c_{21} & c_{22} & c_{23} \\
c_{31} & c_{32} & c_{33}
\end{bmatrix}
\begin{bmatrix} y_1 \\ y_2 \\ y_3 \end{bmatrix} \\
x &= Cy,C可逆!
\end{aligned}
$$
这不就是C矩阵做线性变换吗！
坐标变换常用在没有平方项的二次型再用配方法化成标准形之前需要做一个坐标变换的辅助工作，如
$$
f=x_1x_2+2x_1x_3
$$
想把平方项做出来 最常见的一种方式就是用a+b a-b乘出来
也就是说$x_1=y_1+y_2$, $x_2=y_1-y_2$ 那还有个$x_3$ 为了保证行列式不为0 最简单的方法就是$x_3=y_3$  -- 因为前两个式子已经保证了有一个二阶子式不为0
$$\text{令 } \begin{cases} x_1 = y_1 + y_2 \\ x_2 = y_1 - y_2 \\ x_3 = y_3 \end{cases}$$
$$C = \begin{bmatrix} 1 & 1 & 0 \\ 1 & -1 & 0 \\ 0 & 0 & 1 \end{bmatrix}\neq0$$
$$\begin{aligned} f &= (y_1 + y_2)(y_1 - y_2) + 2(y_1 + y_2)y_3 \\ &= y_1^2 - y_2^2 + 2y_1y_3 + 2y_2y_3 \end{aligned}$$
这样就出现了平方项 然后可以配方化成标准形

现在我们来聚焦于对一个二次型做一次坐标变换后的影响
对$f(x_1,x_2,x_3)=x^TAx$做一次任意的坐标变换$x=Cy$
$$\begin{aligned} f(x_1,x_2,x_3)&=x^TAx\\&= (Cy)^T A (Cy) \\ &= y^T C^T A C y \\ &= y^T B y \end{aligned}$$
其中 $B=C^TAC$
$$\begin{aligned} B^T &= (C^T A C)^T \\ &= C^T A^T (C^T)^T \\ &= C^T A C \\ &= B \end{aligned}$$
B是个对称矩阵 给二次型又写成了一个新的二次型的矩阵表示
二次型和二次型矩阵是一一对应的 我们可以把二次型矩阵看作是二次型的一个形态 而C实际上是做了线性变换 那实际上二次型就是在不同参照系（或者说不同基底）下的同一事物的不同形态！
A和B满足$B=C^TAC$ 其中C是坐标变换中的可逆矩阵 这个关系叫做**合同**！

### 合同
如 $C^T A C = B$, 其中 C可逆矩阵 称矩阵 A 和 B 合同，记  $A \simeq B$
C是可逆矩阵 所以$r(B)=r(C^TAC)=r(AC)=r(A)$ 所以线性变化是不改变矩阵的秩的

$$\begin{aligned}\text{如 } A \simeq B, B \simeq C, \text{ 则 } A \simeq C. \\\end{aligned}$$
$$\quad P_1^T A P_1 = B, \quad P_2^T B P_2 = C, \quad P_2^T (P_1^T A P_1) P_2 = C $$

合同的一个必要条件
$$C^TAC = B$$
$$|B| = |C^TAC| = |C^T| \cdot |A| \cdot |C|$$
$$= |C|^2 \cdot |A|$$
所以得出一个合同的必要条件 $|A|,|B|$同号
### 惯性定理
$$A \simeq B \iff x^T A x \text{ 与 } x^T B x \text{ 正、负惯性指数相同.}$$



### 化标准形及规范形
#### 配方法
任何一个二次型$f(x)=x^TAx$均可通过配方法化成标准形以及规范形！
配方法实际上就是一种可逆线性变换或者说可逆的代换
标准型实际上是个对角阵
那也就是说对于$f=x^TAx$, 存在一个C使得$f=(Cy)^TACy=y^TC^TACy$ 其中$C^TAC=\Lambda$
那也就是说任何的实对称矩阵A 必存在可逆矩阵C 使得$C^TAC=\Lambda$

其实化标准形的核心就是去找那个可逆的C 或者说要求C的行列式不为0 一种好的方法就是找一个三角矩阵 这样能保证行列式不为0 -- 所以我们在操作的时候 我们要把$x_1$先都放在一起配方 把$x_1$处理的干干净净 这样在配方$x_2$的时候就没有$x_1$了 这样就保证了C是一个三角矩阵
![Pasted image 20260530181143.png\|300](/img/user/accessory/Pasted%20image%2020260530181143.png)

![Pasted image 20260530181217.png\|400](/img/user/accessory/Pasted%20image%2020260530181217.png)
![Pasted image 20260530181250.png\|400](/img/user/accessory/Pasted%20image%2020260530181250.png)

![Pasted image 20260530181331.png\|400](/img/user/accessory/Pasted%20image%2020260530181331.png)

如果化规范形
![Pasted image 20260530181557.png\|400](/img/user/accessory/Pasted%20image%2020260530181557.png)

#### 正交变换
那如$C$是个正交矩阵，即$C^T=C^{-1}$ 那实际上可以通过正交变换$x=Qy$把一个二次型$f(x)=x^TAx$化成标准形，即$f=(Qy)^TA(Qy)=y^TQ^TAQy=y^T(Q^TAQ)y$
$Q^TAQ=Q^{-1}AQ = \Lambda$
而这就是相似对角化的过程！
那$B=\Lambda=Q^TAQ$ ，B和A既合同 也相似！
求C这里是个Q 也就是求特征向量 求标准型的矩阵$\Lambda$ 就是求特征值


