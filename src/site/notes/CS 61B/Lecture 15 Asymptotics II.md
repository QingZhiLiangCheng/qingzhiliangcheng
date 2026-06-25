---
{"week":"第六周","dg-publish":true,"tags":["cs61b","week6"],"created":"2025-03-26T14:47:47.595+08:00","updated":"2025-04-19T09:51:30.008+08:00","permalink":"/CS 61B/Lecture 15 Asymptotics II/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"week":"第六周","tags":["cs61b","week6"],"created":"2025-03-26T14:47:47.595+08:00","updated":"2025-04-19T09:51:30.008+08:00"}}
---

### For Loops
**Loops Example 1**
```java
int N = A.length;
for (int i = 0; i < N; i += 1)
   for (int j = i + 1; j < N; j += 1)
      if (A[i] == A[j])
         return true;
return false;
```
We have two ways of approaching our runtime analysis:
1. Counting number of operations
2. Geometric visualization
**Method 1: Count Number of Operations**
count the number of " == " operations
The first time through the outer loop, the inner loop will run N−1 times. The second time, it will run N−2 times. Then N−3, N−4, .... all the way till running the inner loop exactly 1 time when i = N−1. In the worst case, we have to go through every entry, and the outer loop runs N times.
$$
C=1+2+3+...+(N-3)+(N-2)+(N-1)=N(N-1)/2
$$
is part of $N^2$ family.

Method 2: Geometric Visualization
看面积
![Pasted image 20250326150415.png\|300](/img/user/accessory/Pasted%20image%2020250326150415.png)
$\Theta (N^2)$

**Loops Example 2**
```java
public static void printParty(int N) {
   for (int i = 1; i <= N; i = i * 2) {
      for (int j = 0; j < i; j += 1) {
         System.out.println("hello");   
         int ZUG = 1 + 1;
      }
   }
}
```
不同在于i=i * 2
The outer loop advances by multiplying `i` by 2 each time. The inner loop runs from 0 to the current value of `i`.
![Pasted image 20250326151051.png\|500](/img/user/accessory/Pasted%20image%2020250326151051.png)
$$
C(N)=1+2+4+...+N, N=2^m
$$
Again, we can think of this in two ways. Since we're already on a graphical roll, let's start there.
Method1: Finding the Bound Visually
![Pasted image 20250326151823.png](/img/user/accessory/Pasted%20image%2020250326151823.png)
$\Theta(N)$
Method 2: Finding the Bound Mathematically
$$
C(N)=1+2+4+...+N=2N-1
$$

面积
![Pasted image 20250326155238.png\|500](/img/user/accessory/Pasted%20image%2020250326155238.png)
![Pasted image 20250326155251.png\|500](/img/user/accessory/Pasted%20image%2020250326155251.png)

### Recursion
```java
public static int f3(int n) {
   if (n <= 1) 
      return 1;
   return f3(n-1) + f3(n-1);
}
```
What does this function do?
Let's think of an example of calling `f3(4)`:
- The first call will return `f3(4-1) + f3(4-1)`
- Each `f3(3-1)` call will branch out to `f3(2-1) + f3(2-1)`
- Then for each `f3(2-1)` call, the condition `if (n <= 1)` will be true, which will return 1.
- What we observe at the end is that 1 will be returned 8 times, meaning we have `f3(2-1)` summed 8 times.
- Therefore,`f3(4)`will return 8.
成本模型 调用f3的次数
Method 1: Intuition

![Pasted image 20250326160024.png\|500](/img/user/accessory/Pasted%20image%2020250326160024.png)
$2^N-1$ n每增大1 直接翻一倍
![Pasted image 20250326160032.png](/img/user/accessory/Pasted%20image%2020250326160032.png)
$\Theta(2^N)$
Method 2: Algebra
$$
C(N)=1+2+4+...+2^{N-1}=2^N-1
$$
$\Theta(2^N)$

### Binary Search
binary search的步骤就不赘述了
注意偶数的情况
核心在于每一次都切一半
我们要考虑对半切多少次的问题
所以我们觉得是$log_2n$

用函数调用次数作为成本模型
![Pasted image 20250326184703.png\|200](/img/user/accessory/Pasted%20image%2020250326184703.png)
C(N) = ⌊log2(N)⌋+1
以下是一些值得了解的属性：
⌊𝑓(𝑁)⌋=Θ(𝑓(𝑁))
⌈𝑓(𝑁)⌉=Θ(𝑓(𝑁))
𝑙𝑜𝑔𝑝(𝑁)=Θ(𝑙𝑜𝑔𝑞(𝑁))

最后一个本质上说明，对于对数运行时间，对数的底数根本不重要，因为就大O而言，它们都是等价的（可以通过应用对数换底来看到这一点）

### Mergesort
[[algorithm/Algorithm Princeton/UNIT6 Merge Sort\|UNIT6 Merge Sort]]
merge sort的过程就不赘述了[go here](https://docs.google.com/presentation/d/1mdCppuWQfKG5JUBHAMHPgbSv326JtCi5mvjH1-6XcMw/edit#slide=id.g463de7561_042)
What is the runtime of the merge operation? We can use the number of "write" operations to the new list as our cost model, and count the operations. Since we have to write each element of each list only once, the runtime is Θ(N).

我们都知道selction sort是 $N^2$的数量级
We noticed earlier that doing selection sort on an N=64 list will take ~2048 AU
But if we sort a list half that big, N=32, it only takes ~512 AU. That's more than twice as fast!
所以我们采用对N=32的序列排序之后 归并 512 + 512 + 64 = 1088 AU
But how much faster?
$N+2(\frac{N}{2})^2$   还是$N^2$

有一个问题是继续分下去  分到一个的时候 实际上就摆脱了任何的实际排序
![Pasted image 20250327103730.png\|300](/img/user/accessory/Pasted%20image%2020250327103730.png)
一层是N
所以是logN个N
