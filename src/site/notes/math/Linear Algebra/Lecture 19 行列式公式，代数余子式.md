---
{"created":"2025-09-27T09:57","updated":"2025-09-27T11:17","dg-publish":true,"permalink":"/math/Linear Algebra/Lecture 19 行列式公式，代数余子式/","dgPassFrontmatter":true,"noteIcon":""}
---

**today's agenda**
寻找行列式的公式求解
- 行列式公式
- 代数余子式

### 行列式公式
**二阶行列式公式**
$\left|\begin{array}{ll}{a} & {b} \\ {c} & {d}\end{array}\right|=a d-b c$
其实学过上一节行列式的性质，我们就能够推到出这个公式来了
$$
\displaylines{
\begin{vmatrix}a& b\\c& d\end{vmatrix}\stackrel{3.b}{=}\begin{vmatrix}a& 0\\c& d\end{vmatrix}+\begin{vmatrix}0& b\\c& d\end{vmatrix}\stackrel{3.b}{=}\begin{vmatrix}a& 0\\c& 0\end{vmatrix}+\begin{vmatrix}a& 0\\0& d\end{vmatrix}+\begin{vmatrix}0& b\\c& 0\end{vmatrix}+\begin{vmatrix}0& b\\0& d\end{vmatrix}\\
其中\begin{vmatrix}a& 0\\c& 0\end{vmatrix}\stackrel{6, 10}{=}0,\quad\begin{vmatrix}a& 0\\0& d\end{vmatrix}\stackrel{7}{=}ad,\quad \begin{vmatrix}0& b\\c& 0\end{vmatrix}\stackrel{2,7}{=}-bc,\quad \begin{vmatrix}0& b\\0& d\end{vmatrix}\stackrel{6,10}{=}0\\
综上，\begin{vmatrix}a& b\\c& d\end{vmatrix}=ad-bc
}
$$
其实这是给出了一个推到所有行列式公式的方法 -- 分解行列式
而分解后的行列式实际上是存在一些列为零的行列式的情况，不为零的行列式正好是一列一个元素(或者是一行一个元素)的排列组合
所以其实很容易能推出三阶行列式的公式
**三阶行列式公式**
$$
\displaylines{
\begin{align}
\begin{vmatrix}a_{11}& a_{12}& a_{13}\\a_{21}& a_{22}& a_{23}\\a_{31}& a_{32}& a_{33}\end{vmatrix}&=\begin{vmatrix}a_{11}& 0& 0\\0& a_{22}& 0\\0& 0& a_{33}\end{vmatrix}+\begin{vmatrix}a_{11}& 0& 0\\0& 0& a_{23}\\0& a_{32}& 0\end{vmatrix}+\begin{vmatrix}0& a_{12}& 0\\a_{21}& 0& 0\\0& 0& a_{33}\end{vmatrix}\\&+\begin{vmatrix}0& a_{12}& 0\\0& 0& a_{23}\\a_{31}& 0& 0\end{vmatrix}
+\begin{vmatrix}0& 0& a_{13}\\a_{21}& 0& 0\\0& a_{32}& 0\end{vmatrix}+\begin{vmatrix}0& 0& a_{13}\\0& a_{22}& 0\\a_{31}& 0& 0\end{vmatrix}\\
&=a_{11}a_{22}a_{33}-a_{11}a_{23}a_{32}-a_{12}a_{21}a_{33}+a_{12}a_{23}a_{31}+a_{13}a_{21}a_{32}-a_{13}a_{22}a_{31}
\end{align}
}
$$
首先这里说明一下，这其实就是我们在一开始学的那个叉叉子的公式，其实这只是推动出来公式后发现的一个规律，这个规律只适用于三阶行列式
能看到其实我们是先确定第一行a11的位置，然后在做其他列其他行的元素的排列组合
换句话说，第一行实际上有n个选择，选择之后就确定了一列，第二行只能有n-1个选择了... 这其实能够得到一般公式
**Big Formula**
$$
\displaylines{
\det A=\sum_{n!} \pm a_{1\alpha}a_{2\beta}a_{3\gamma}\cdots a_{n\omega}, \quad(\alpha, \beta, \gamma, \cdots,\omega)=P_n^n
}
$$

example
$$
\displaylines{
\begin{vmatrix}0& 0& \color{red}1& \color{blue} 1\\0& \color{red} 1& \color{blue} 1& 0\\ \color{red} 1& \color{blue} 1& 0& 0\\\color{blue} 1& 0& 0&  \color{red} 1\end{vmatrix}
}
$$
其实在这个行列式中α只能为3或4，如果选定α为3，实际上只有红色的这一种组合，同样的如果选定α等于4，只有蓝色这一种组合 -- 其余都是行列式
- 对于第一组非零行列式(红色部分)，其对应的排列是(3,2,1,4),变成(1,2,3,4)只需要一步操作，所以符号为-
- 对于第二组非零行列式(蓝色部分)，其对应的排列是(4,3,2,1),变成(1,2,3,4)需要两步从操作，符号为+

通过统计当前排列变换到顺序排列(1,2,3, ... ,n)的操作数可以确定非零行列式的符号，但这种方法显然不实用，难以编程实现且当仅适用于行列式的阶数较小时
一般用逆序数来判断非零行列式的正负号。逆序数就是从左到右遍历当前排列中的每一个数，统计右侧有几个数比自己小，然后求和
逆序数为偶数，称排列为偶排列，非零行列式取+
逆序数为奇数，称排列为奇排列，非零行列式取-
其中的道理在于奇排列做一次交换后即为偶排列，偶排列做一次交换后即为奇排列，初始顺序排列(1,2,3, ... ,n)为偶排列

### 代数余子式
对于上面的3 by 3的矩阵，我们已经得到了行列式公式
$$
\displaylines{
原式=a_{11}a_{22}a_{33}-a_{11}a_{23}a_{32}-a_{12}a_{21}a_{33}+a_{12}a_{23}a_{31}+a_{13}a_{21}a_{32}-a_{13}a_{22}a_{31}
}
$$
可以改成
$$
\displaylines{
a_{11}(a_{22}a_{33}-a_{23}a_{32})+a_{12}(-a_{21}a_{33}+a_{23}a_{31})+a_{13}(a_{21}a_{32}-a_{22}a_{31})\\
=\begin{vmatrix}a_{11}& 0& 0\\0& a_{22}& a_{23}\\0& a_{32}& a_{33}\end{vmatrix}+\begin{vmatrix}0& a_{12}& 0\\a_{21}& 0& a_{23}\\a_{31}& 0& a_{33}\end{vmatrix}+\begin{vmatrix}0& 0& a_{13}\\a_{21}& a_{22}& 0\\a_{31}& a_{32}& 0\end{vmatrix}\\
=a_{11}(\begin{vmatrix} a_{22}& a_{23}\\a_{32}& a_{33}\end{vmatrix})+a_{12}(-\begin{vmatrix} a_{21}& a_{23}\\a_{31}& a_{33}\end{vmatrix})+a_{13}(\begin{vmatrix} a_{21}& a_{22}\\a_{31}& a_{32}\end{vmatrix})
}
$$

其实还是一旦a11选定，剩余因子就只能从剩余的n-1行和n-1列中提取，而剩余的因子正好组成一个行列式, 而这个就叫做代数余子式
改写中括号的部分就是代数余子式，比如$(-a_{21}a_{33}+a_{23}a_{31})$是$a_{12}$的代数余子式
代数余子式(cofactors)是与选定元素配套的，这也是'co-'的意思
选定元素$a_{ij}$的代数余子式为：将原行列式中第i行和第j列抹去后得到的n-1阶行列式，再取正负(i+j偶数为+，奇数为-)
$$
\displaylines{
C_{ij}=(-1)^{i+j}\cdot det(去掉 i 行和j列的一个n-1矩阵)
}
$$
所以根据代数余子式，我们能得到一个新的求解行列式的方法
一直按行展开！而且选行的时候是有一定技巧的

### 总结
实际上现在教授是讲了三种求行列式的方法
- 消元法，化为三角行列式，对角线求行列式
- 找n的阶乘种元素排列的行列式，计算行列式的和
- 代数余子式 按行展开

但实际上考研的要求中会针对别的题型还会有一些其他的求解方法和技巧，比如有的行列式需要把所有列的值都加在第一列...

最后教授实际上还讲了一个三对角矩阵 由1组成的n阶三对角矩阵的行列式值匆匆1阶开始按照1,0,-1,-1,0,1循环，周期为6
$$
\displaylines{
A_{1}=1, A_{2}=\left|\begin{array}{cc}{1} & {1} \\ {1} & {1}\end{array}\right|, A_{3}=\left|\begin{array}{ccc}{1} & {1} & {0} \\ {1} & {1} & {1} \\ {0} & {1} & {1}\end{array}\right|, A_{4}=\left|\begin{array}{cccc}{1} & {1} & {0} & {0} \\ {1} & {1} & {1} & {0} \\ {0} & {1} & {1} & {1} \\ {0} & {0} & {1} & {1}\end{array}\right|,\cdots, 寻找其行列式值的规律
}
$$

实际上满足递推关系 $\begin{vmatrix}A_n\end{vmatrix}=\begin{vmatrix}A_{n-1}\end{vmatrix}-\begin{vmatrix}A_{n-2}\end{vmatrix}$
