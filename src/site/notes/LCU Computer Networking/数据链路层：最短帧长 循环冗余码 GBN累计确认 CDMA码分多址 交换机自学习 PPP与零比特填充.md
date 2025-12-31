---
{"created":"2025-12-28T14:37","updated":"2025-12-29T09:52","dg-publish":true,"permalink":"/LCU Computer Networking/数据链路层：最短帧长 循环冗余码 GBN累计确认 CDMA码分多址 交换机自学习 PPP与零比特填充/","dgPassFrontmatter":true,"noteIcon":""}
---

### 最短帧长
![Pasted image 20251229081401.png|300](/img/user/accessory/Pasted%20image%2020251229081401.png)
假设从0时刻甲检测到信道是空的，于是向乙传送分组，我们暂且只看第一个比特，假设经过了$\tau$马上到达乙，还需要$\theta$到达乙，这里假设$\theta \to 0$($\theta$趋近于0)
在此时乙由于某些原因检测到信道是空的，向甲发送分组
由于同一条链路的传输速率应该是一样的，所以两个分组的比特应该会在图中C点处发生碰撞，即$\tau+\frac{\theta}{2}$
乙何时知道发生碰撞？A所发送的第一个比特碰撞之后到达乙时，即$\tau+\theta$,由于$\theta \to 0$,所以近似时间为$\tau$
甲何时知道发生碰撞？在$\tau+\theta$时，甲分组的第一个比特到达乙，乙分组的第一个比特到达B点，还需要$\tau$时间乙分组的比特能够到达甲. 所以在$2\tau + \theta$时，即$2\tau$时，甲知道碰撞
核心问题在于，甲能够监听信道的时间是它发送甲分组的时间，换句话说，如果甲分组的发送延时太短，就没法检测到信道中传回来的碰撞的消息，所以甲分组的传输延时必须大于$2\tau$,即
$$
t_{transmission}\geq 2\tau = 2t_{propagation}
$$
而发送时延是与帧长有关的，帧长不能太短，所以有了最短帧长的概念
事实上
$$
\frac{L}{R}\geq 2\frac{d}{s}
$$
这四个指标已知三个都能求另一个

以太网中的最小帧长为64B


![Pasted image 20251228143758.png|500](/img/user/accessory/Pasted%20image%2020251228143758.png)

![1b851d12d6da682547a6d3268e5d47ed.jpg|500](/img/user/accessory/1b851d12d6da682547a6d3268e5d47ed.jpg)


![320c4de03f0308f3ce55bcafa57d4558.jpg|500](/img/user/accessory/320c4de03f0308f3ce55bcafa57d4558.jpg)

注意10Base-T的含义

### 差错检测之循环冗余码
差错检测其实是一种大的技术，分为好几种
在数据链路层接触了一个CRC(Cyclic Redundancy Check, CRC, 循环冗余检验)
补0：个数是生成多项式的最高次幂
除数: 生成多项式的系数
其实是做的异或运算，所谓异或运算就是同为1才为1，不同为0；为了避免出错，这里一个更简单的想法是做不进位的加法
可能计算余数
发送的数据就是原数据+余数
接收端验证： 接收到的数据除以除数 余数为0

eg: 待发送的信息为101001，生成多项式为$G(x)=x^3+x^2+1$,计算余数，发送的数据，以及接收端校验
![b3744dc86c46cef33bc37d7a8b366155.jpg|300](/img/user/accessory/b3744dc86c46cef33bc37d7a8b366155.jpg)

![Pasted image 20251228143036.png|500](/img/user/accessory/Pasted%20image%2020251228143036.png)

### GBN累计确认
![Pasted image 20251229095124.png|500](/img/user/accessory/Pasted%20image%2020251229095124.png)

### CDMA码分多址
![80ce59a7ad3b81ffad35655462e45988 1.jpg|500](/img/user/accessory/80ce59a7ad3b81ffad35655462e45988%201.jpg)

### 交换机自学习功能
核心就是在交换机没有学习进入表转发表的时候，会广播
![730ab77ff47b0402a5c85682dad58a30.jpg|500](/img/user/accessory/730ab77ff47b0402a5c85682dad58a30.jpg)

### PPP与零比特填充
![312d11f2286885973832e7a5ad4c90ee.jpg](/img/user/accessory/312d11f2286885973832e7a5ad4c90ee.jpg)