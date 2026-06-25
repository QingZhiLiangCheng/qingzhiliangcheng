---
{"created":"2025-12-01T11:07","updated":"2026-05-04T11:19","dg-publish":true,"permalink":"/math/考研线性代数/Topic 3 求解方程组 矩阵消元法, Ax=0, Ax=b, LU分解/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2025-12-01T11:07","updated":"2026-05-04T11:19"}}
---

### 矩阵消元法 高斯消元法
在[[math/考研线性代数/Topic 1 向量, 矩阵及乘法, 线性变换, 方程组的联系与本质#理解2：方程组\|Topic 1 向量, 矩阵及乘法, 线性变换, 方程组的联系与本质#理解2：方程组]]中提到过，方程组可以用矩阵的方式来表示
$$\begin{cases} x + 2y + z = 2 & \text{--- (1)} \\ 3x + 8y + z = 12 & \text{--- (2)} \\ 4y + z = 2 & \text{--- (3)} \end{cases}$$
写成矩阵形式
$$
\begin{align}
&A=\begin{pmatrix} 1 & 2 & 1 \\ 3 & 8 & 1 \\ 0 & 4 & 1 \end{pmatrix}\\
&Ax=b\\
&\begin{pmatrix} 1 & 2 & 1 \\ 3 & 8 & 1 \\ 0 & 4 & 1 \end{pmatrix} \begin{pmatrix} x \\ y \\ z \end{pmatrix} = \begin{pmatrix} 2 \\ 12 \\ 2 \end{pmatrix}
\end{align}
$$
中学时期所学过的消元法：
$$\begin{aligned} &\text{步骤 1：消除 (2) 中的 } x \text{ 项} \\ &(2) - 3 \times (1) \implies (3x - 3x) + (8y - 6y) + (z - 3z) = 12 - 6 \\ &\therefore 2y - 2z = 6 \quad \text{--- (2')} \\ \\ &\text{步骤 2：消除 (3) 中的 } y \text{ 项} \\ &(3) - 2 \times (2') \implies (4y - 4y) + (z - (-4z)) = 2 - 12 \\ &\therefore 5z = -10 \quad \text{--- (3')} \\ \\ &\text{步骤 3：回代求解} \\ &\text{由 (3') 得：} z = -2 \\ &\text{代入 (2')：} 2y - 2(-2) = 6 \implies 2y = 2 \implies y = 1 \\ &\text{代入 (1)：} x + 2(1) + (-2) = 2 \implies x = 2 \end{aligned}$$
如果用矩阵形式表示消元法的话就是
$$\begin{aligned} \left( \begin{array}{ccc|c} 1 & 2 & 1 & 2 \\ 3 & 8 & 1 & 12 \\ 0 & 4 & 1 & 2 \end{array} \right) &\xrightarrow{R_2 - 3R_1 \to R_2} \left( \begin{array}{ccc|c} 1 & 2 & 1 & 2 \\ 0 & 2 & -2 & 6 \\ 0 & 4 & 1 & 2 \end{array} \right) \\ &\xrightarrow{R_3 - 2R_2 \to R_3} \left( \begin{array}{ccc|c} 1 & 2 & 1 & 2 \\ 0 & 2 & -2 & 6 \\ 0 & 0 & 5 & -10 \end{array} \right) \\ &\xrightarrow{\text{回代求解}} \begin{cases} x = 2 \\ y = 1 \\ z = -2 \end{cases} \end{aligned}$$
能发现高斯消元法的核心是：通过行变换，将矩阵转换为上三角矩阵，从而逐步求解未知数
实际上我们对矩阵做了两次选主元的消元，在这个例子上，实际上就是$E_{32}(E_{21}A)=U$ 
在这里，左乘实际上是一种行变换，左乘$E_{21}$意味着将A中的$a_{21}$变成零
更抽象的，高斯消元法的过程就相当于在A矩阵上做成了好多个E，最终变成了U

其实甚至可以化成行最简 这样一眼就看出来了解
### 矩阵LU LRU分解
在高斯消元法中实际上提到了消元法本质上就是进行了行变换，而进行行变换的过程，是相当于在A矩阵上左乘了好多个E，最终变成了U
所以能看出来A和U实际上是有关系的，这个关系其实就是L，类似于一种E的综合的作用，这实际上能看作是一种更快的消元
假设这是一个一般的2by2矩阵
$$
A=\begin{bmatrix}2 & 1 \\ 8 &7\end{bmatrix}
$$
消元的过程 就是左乘一个$E_{21}$  即$E_{21}A=U$
$$
\begin{bmatrix}1 & 0 \\ -4 &1\end{bmatrix}
\begin{bmatrix}2 & 1 \\ 8 &7\end{bmatrix}=
\begin{bmatrix}2 & 1 \\ 0 &3\end{bmatrix}
$$
而我们要找A和U的联系 也就是说$A=LU$找L
首先能看出来L和E是逆的关系
消元矩阵的逆很好求，因为消元矩阵本身就是行变换，其实就是把行变换逆回去 $E_{21}^{-1}=L=\begin{bmatrix}1 & 0 \\ 4 &1\end{bmatrix}$
也就是$A=LU$
$$
\begin{bmatrix}2 & 1 \\ 8 &7\end{bmatrix}=\begin{bmatrix}1 & 0 \\ 4 &1\end{bmatrix}\begin{bmatrix}2 & 1 \\ 0 &3\end{bmatrix}
$$
你会发现这就是一个上三角和下三角的相乘！
L矩阵上的对角线都是1 U矩阵上的对角线都是主元，有些时候我们会把主元单独拿出来，也就是所说的LRU分解
$$
\begin{bmatrix}2 & 1 \\ 8 &7\end{bmatrix}=\begin{bmatrix}1 & 0 \\ 4 &1\end{bmatrix}\begin{bmatrix}2 & 0 \\ 0 &3\end{bmatrix}
\begin{bmatrix}1 & \frac{1}{2} \\ 0 &1\end{bmatrix}
$$
推广到3by3
$E_{32}E_{31}E_{21}A=U$ 找$A=LU$实际上就是去找$(E_{32}E_{31}E_{21})^{-1}$，总体的逆= 分别求逆逆序相乘，也就是$L=E_{21}^{-1}E_{31}^{-1}E_{32}^{-1}$
其实这里的核心思想就是把消元的反向操作封装进了一个下三角矩阵L
那为什么采用L的形式不用E的形式呢 -- 这是因为用E的逆矩阵(L的形式)做乘法更简单！
我们在进行行变换的时候，进行$E_{21}$的时候其实是改变了第二行的结果的，也就是说用第二行给第三行消元的时候，这个行不再是A的行2了
即
$$U \text{ 的行 } 3 = (A \text{ 的行 } 3) - a(U \text{ 的行 } 1) - b(U \text{ 的行 } 2)$$
其中 $- a(U \text{ 的行 } 1)$是在第一个主元消元的时候$E_{31}$的结果， $- b(U \text{ 的行 } 2)$ $E_{31}，E_{32}$共同造成的
如果把上面的式子改写一下
$$A \text{ 的行 } 3 = a(U \text{ 的行 } 1) + b(U \text{ 的行 } 2) + 1(U \text{ 的行 } 3)$$
那也就是说在A=LU以及左乘的思想，（a,b,1)应该是L的第三行
同理，L的第二行应该是c(U的行1)+1(U的行2)+0(U的行3)
...
$$L = \begin{pmatrix} 1 & 0 & 0 \\ l_{21} & 1 & 0 \\ l_{31} & l_{32} & 1 \end{pmatrix}$$
LU分解通过L存下了叠加后的消元倍数，以至于可以用在不同的b上
如果对于Ax=b，需要根据b来求信息x，每次b都是不同的，A是固定的，这个时候可以先把A进行LU分解，然后通过LU来求解x 比 直接用A来求解效率高
因为$AX=LUX=L(UX)=b \Rightarrow LY=b, Y=UX$
![Pasted image 20250804114713.png](/img/user/accessory/Pasted%20image%2020250804114713.png)
由于左乘时行变换，而前面的行的后面元素都是0 所以自上向下求解的时候省了很多东西，比如说$y_1$事实上只需要$b_1$和$L_{11}$就能求出来，而$y2$ 需要$L_{12}，L_{2,2},b1,b2$就能求出来
第一行方程为 $L_{11}Y_1 = b_1$，直接得出 $Y_1 = b_1 / L_{11}$。只需要 1 次操作，二行方程为 $L_{21}Y_1 + L_{22}Y_2 = b_2$。因为 $Y_1$ 已知，所以 $Y_2 = (b_2 - L_{21}Y_1) / L_{22}$。需要 2 次操作（1 次乘减，1 次除法）
也就是说，一共操作 $1+2+...+n = \frac{n(n+1)}{2} \approx \frac{n^2}{2}$次
![Pasted image 20250804115226.png](/img/user/accessory/Pasted%20image%2020250804115226.png)
同理，第二步回代，从下往上算 也是$\frac{n^2}{2}$次  也就说一共用了$n^2$次 
A的消元次数
![Pasted image 20250804115439.png](/img/user/accessory/Pasted%20image%2020250804115439.png)
用第一行给其他n-1行消元 计算次数是n(n-1) n-1是n-1行，n是每一行n个数加减法
那总共$n(n-1)+(n-1)(n-2)(n-3) +... + 2 * 1 \approx n^2+(n-1)^2+...+2^2+1=\frac{1}{3}n^3$
其实LU操作就是提前消元并存储 省了之后算不同b时的A消元$O(n^3)$的复杂度，改成了求解$LY=b, Y=UX$的$O(n^2)$的复杂度！

### Ax=0: 主变量, 特解, Ux=0, Rx=0
求解Ax=0的算法 -- 求零空间
$$
A=\begin{bmatrix}
1 & 2 &2 &2 \\
2&4 &6&8\\
3&6&8&10
\end{bmatrix}
$$
仍然是消元法
$$
A=\begin{bmatrix}
1 & 2 &2 &2 \\
2&4 &6&8\\
3&6&8&10
\end{bmatrix} =
\begin{bmatrix}
\underline{1}&2&2&2\\0& 0&2&4\\0&0 & 2 &4
\end{bmatrix}=
\begin{bmatrix}
1&2&2&2\\0& 0&\underline{2}&4\\0&0 & 0 &0
\end{bmatrix}
$$
方法仍然是选取pivot(主元, 标下划线了) 做行变换 最后得到一个行阶梯矩阵U
主元的数量 -- 矩阵的秩
主元所在的列叫做主元列，其余列叫做自由列
在这个例子中，U的第一列，第三列为主元列，第二列，第四列为自由列，所谓自由，就是可以自由或任意分配数值
事实上，上面U所代表的方程组是
$$
\begin{align}
x_1+2x_2+2x_2+2x_2 = 0\\
2x_3+4x_4=0
\end{align}
$$
当自由列决定了，也就意味着代入也就决定了主元列
比如，可以去自由列中的自由变量$x_2=1$, $x_4=0$ , 这样就能得到一个解 $\begin{bmatrix}-2\\1 \\ 0 \\0\end{bmatrix}$
而这就是Ax=0的一个零空间，想要得到零空间中的更多向量，显然可以将这个向量乘以一个常数c，即$c\begin{bmatrix}-2\\1 \\ 0 \\0\end{bmatrix}$, 这表示了四维空间中的一条直线
x2, x4可以取1,0 这是自由，但是也可以取0,1 , 这样可以得到一个解$\begin{bmatrix}2\\0 \\ -2 \\1\end{bmatrix}$
事实上这两个向量的线性组合就是整个零空间. 即$c_1\begin{bmatrix}-2\\1 \\ 0 \\0\end{bmatrix}+c_2\begin{bmatrix}2\\0 \\ -2 \\1\end{bmatrix}$
而这两个解叫做特解，教授叫特解，其实就是特定的解，我们好像一般叫基础解系。因为里面的x2, x4是取的特定的值，通常我们是轮着选取其中一个为1，其余为0
其实就是选取了最简单的极大线性无关组 选的是10 01这种标准的基 这一定是线性无关的 他的延伸组也是线性无关的 这就是基础解系
有多少个特解？ 有多少个自由列就有多少个特解，那有多少自由列呢？ 事实上是列数-r，这个数我们成为自由度
为什么有多少自由列就有多少特解？其实这个问题就是为什么自由列能决定解空间的维度
我们可以把上面例子的式子化成最简行阶梯矩阵 -- 向上消元 -- 让主元上面的元素变为0 -- 这要通过列交换，就会得到一个包含所有信息的最简的矩阵 -- 包含单位阵，主元列，自由列 -- I单位阵对应主元列，F自由部分对应自由列
![Pasted image 20250901175056.png\|500](/img/user/accessory/Pasted%20image%2020250901175056.png)

$$
Ax=RN=\begin{bmatrix}I&F\\0&0\end{bmatrix}\begin{bmatrix}x_{pivot}\\x_{free}\end{bmatrix}=0
$$
所以有
$$I \cdot x_{pivot} + F \cdot x_{free} = 0 \implies x_{pivot} = -F x_{free}$$
这意味着自由变量的值一旦确定，主元变量的值也就随机确定，令自由变量为I，则有$x_{pivot}=-F$
$$
N=\begin{bmatrix}-F \\I\end{bmatrix}
$$
所以事实上I的维度即自由变量的个数，就是整个零空间或者说解空间的维度！


![Pasted image 20260527152928.png\|500](/img/user/accessory/Pasted%20image%2020260527152928.png)
注意主元列的选取其实可以优先选择是1的那一列当主元列 然后把上面的元素换成0 化成一个类似行最简的矩阵 这样做会更快

行最简的规律
![Pasted image 20260527153225.png\|400](/img/user/accessory/Pasted%20image%2020260527153225.png)
对应是相反数


### Ax=b 可解性和解的结构
$$
\begin{align}
x_1+2x_2+2x_3+2x_4=b_1 \\
2x_1+4x_2+6x_3+8x_4=b_2 \\
3x_1+6x_2+8x_3+10x_4=b_3
\end{align}
$$
仍然还是消元法，之前学过，消元法实际本质上就是做的是行变换，同样的变换要作用在b上面，这里直接写到一个矩阵里，这个矩阵就是增广矩阵
$$
\left[
\begin{array}{cccc|c}
1&2&2&2& b1 \\ 2 &4&6&8&b_2 \\ 3&6&8&10&b_3
\end{array}
\right]
$$
处理成 -- 行阶梯型矩阵
$$
\left[
\begin{array}{cccc|c}
1&2&2&2& b1 \\ 2 &4&6&8&b_2 \\ 3&6&8&10&b_3
\end{array}
\right] = 
\left[
\begin{array}{cccc|c}
1&2&2&2& b1 \\ 0&0&2&4&b_2-b_1 \\ 0&0&0&0&b_3-b_2-b_1
\end{array}
\right]
$$
其实很明显最后一行所对应的方程是$0x_1+0x_2+0x_3+0x_4=b_3-b_2-b_1$ 也就是说，要想Ax=b有解，$b_3-b_2-b_1$需要等于0 -- 换句话说，如果A各行的线性组合得到零行  b同样组合也必须为零。 行空间实际上是有了一个约束，如果 $b$ 没能通过这个检查（即组合不为 $0$），就说明它包含了一些 $A$ 无法提供的、位于列空间之外的信息，从而导致方程无解
这其实就是李永乐老师书上的$r(A)=r(A|b)$
从Ax=b对A进行列变换来看，b要在A的列空间中，如果b不在A的列空间中，那肯定无解
具体求解的算法是这样的
- 找particular solution(特解) -- 一个好的方法 将自由变量设置为0 求出Ax=b中的主变量
- 加上Ax=0的零空间

对于一个具体的例子是这样的
![ee1a9705347331ea31c3c4411cc52d46.jpg\|400](/img/user/accessory/ee1a9705347331ea31c3c4411cc52d46.jpg)
其实特解+零空间是合理的，因为
$$
\begin{align}
Ax_{null sapce}=0\\
Ax_{special}=b
\end{align}
\to A(x_{nullspace}+x_{special})=b
$$
对于上面的例子，其实能发现最后的解的空间实际上是类似四维子空间的一个空间，后面的零空间是一个子空间，然后子空间的每个向量都加上了一个特解的向量，构成了一个新的平面（就像平移了一样）

对于列满秩的一个矩阵A, 也就是说有多少列就有多少个主元，比如
$$
\begin{bmatrix} 1 &2 \\2&8 \\3&10 \end{bmatrix}\to
\begin{bmatrix} 1 &2 \\0&4 \\0&0 \end{bmatrix}
$$
自由变量的个数实际上是零空间的维度，所以其实对于Ax=0来说，零空间只有零向量，那么最后的通解就取决于Ax=b有没有特解
- 如果有特解，那么解是唯一的
- 如果没有特解，那就是无解

对于行满秩的一个矩阵A, 也就是说有多少行就有多少个主元，比如
$$\begin{bmatrix}1 &2&2&2\\2&4&6&8\end{bmatrix} \to
\begin{bmatrix}1 &2&2&2\\0&0&1&2\end{bmatrix}
$$
在消元过程中不会出现零行 所以对b实际上没有要求，b取什么都会在A中的列空间中
同样的，Ax=0存在零空间
所以对于行满秩实际上是无数解

对于r=n=m的矩阵 -- 满秩方阵 比如
$$
\begin{bmatrix}
1&2\\3&1
\end{bmatrix}
$$
最终的最简行阶梯型矩阵一定是I
零空间是零向量
所以Ax=b 肯定有解 且是唯一解


