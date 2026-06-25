---
{"created":"2025-12-01T11:15","updated":"2026-05-29T16:40","dg-publish":true,"permalink":"/math/考研线性代数/Topic 10 相似/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2025-12-01T11:15","updated":"2026-05-29T16:40"}}
---

### 相似矩阵
如果存在n by n的矩阵A,B 以及一个矩阵M具有可逆矩阵$M^{-1}$,使得$B=M^{-1}AM$成立，则两个方阵A和B相似
其实这个式子我们早就接触过，比如我们对角化的式子$S^{-1}AS=\Lambda$,其中$\Lambda$就是A的一个相似矩阵
其实反过来也成立 如果有$P^{-1}AF=B$ 如果B是一个对角矩阵 那么A就有n个线性无关的特征向量 每个特征向量就是P的列向量；对应的n个特征值就是B中对应的元素
事实上，基于矩阵A我们能够取不同的矩阵B得到一系列的相似矩阵，这样就将这些矩阵联系起来，这些矩阵属于一类矩阵，这一类矩阵中的任意两个矩阵彼此相似，其实$\Lambda$是其中最简单的一个矩阵
那其实自然有A~B B~C -> A~C
证明如下
$$
\begin{aligned}
&\text{ 若 } A \sim B, B \sim C \text{ 则 } A \sim C \\
&\text{设 } P_1^{-1}AP_1 = B, \quad P_2^{-1}BP_2 = C \\
&\text{则 } P_2^{-1}(P_1^{-1}AP_1)P_2 = C \\
&\text{令 } P = P_1P_2, \text{ 有 } P^{-1} = (P_1P_2)^{-1} = P_2^{-1}P_1^{-1} \\
&\therefore P^{-1}AP = C
\end{aligned}
$$
这自然有了一种证明相似的思路 找一个中介！

当然这些相似矩阵的共同点在于具有相同的特征值！
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
当然特征值相同 迹就相同！
实际上相似矩阵他们有相同的特征多项式 ,即$|\lambda E-A|=|\lambda E-B|$ 证明如下
$$
\begin{aligned}
|\lambda E - B| &= |\lambda E - P^{-1}AP| \\
&= |P^{-1}(\lambda E - A)P| \\
&= |P^{-1}| \cdot |\lambda E - A| \cdot |P|\\
&=|\lambda E-A|
\end{aligned}
$$

r(A)=r(B) 证明如下
$$
\begin{align}
r(B)&=r(P^{-1}AP),P^{-1}可逆\\
&=r(AP),P可逆\\
&=r(A)
\end{align}
$$
主要用了如果A可逆 r(AB)=r(B)这个公式

|A|=|B| 证明如下
$$
\begin{aligned}
|B|&=|P^{-1}AP|\\
&=|P^{-1}||A||P|\\
&=|A|
\end{aligned}
$$

$A\sim B \to A^n \sim B^n$
$$
\begin{align}
B^n&=(P^{-1}AP)(P^{-1}AP)...(P^{-1}AP)\\
&=P^{-1}A(PP^{-1})A(PP^{-1})...(PP^{-1})AP\\
&=P^{-1}A^{n}P
\end{align}
$$


$A\sim B \to A+kE \sim B+kE$
$$
\begin{align}
P^{-1}(A+kE)P&=P^{-1}AP+P^{-1}kEP\\
&=B+kE

\end{align}
$$
那根据前面的性质 $|A+kE|=|B+kE|$, $r(A+kE)=r(B+kE)$

$A\sim B$如 A 可逆，则 $A^{-1} \sim B^{-1}$
$$
\begin{aligned} &(P^{-1}AP)^{-1} = B^{-1} \\ &\Rightarrow P^{-1}A^{-1}(P^{-1})^{-1} = B^{-1} \\ &\therefore P^{-1}A^{-1}P = B^{-1} \end{aligned}
$$

注意如果出现了重根 只有定理能够说明 二重根最多有两个不相关的特征向量 可能有两个 可能有一个 要根据具体情况具体分析！
换句话说如果有重根 虽然也能写出P和对角矩阵 但是可能会出现不相似的情况



### 若尔当标准型 （了解）
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
![Pasted image 20251020145655.png\|400](/img/user/accessory/Pasted%20image%2020251020145655.png)
若按照若尔当块进行划分，得到不同的结构，所以两个矩阵并不相似
当然如果方阵有n个线性独立的特征向量，则方阵对角化，那么它所对应的若尔当矩阵J是特征值矩阵(对角矩阵)$\Lambda$
