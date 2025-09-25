---
{"created":"2025-09-25T14:33","updated":"2025-09-25T21:25","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 14 C标准库的实现/","dgPassFrontmatter":true,"noteIcon":""}
---

### Overview
之前的课，我们学习了操作系统有有一个内核Kernel, 内核对外会暴露给程序一些API,这个API叫syscall，而外面的这些程序中有一个比较特殊的叫shell, shell管理这我们所有的人机交互的这些设备，并帮助我们把我们的动作翻译成syscall，除此之外，我们可以在shell上运行程序, 比如我们可以用命令来运行起一个程序，或者是在图形化shell中通过点击来运行一个程序
而今天这节课，就是回答，对于外面的这些程序怎么写？如何在系统调用之上构建程序能够普遍受惠的标准库？
我们有syscall，之前也讲了C语言的形式语义，那我们实际上就能用C语言封装出一些库，而这些库，是几乎每一个应用程序都可以用的库

### 熟悉又陌生的libc
```c
 #include<stdio.h>
 int main(){
    printf("Hello World!\n");
 }
```
实际上，从我们学计算机，C语言的第一天起，我们就已经接触到libc了，hello world是我们几乎每个人写的第一个程序，实际上，我们就已经用libc了
后面我还回到引用string.h的头文件，调用一个strlen函数等等，我们甚至觉得这很自然，但是我们没有想过，为什么有libc，libc是怎么实现的
其实为什么要有libc这个问题也很简单
在上节课我们用到了一个sh-xv6.c的一个没有调用任何库的程序，但是在这个程序中，实际上我们是自己对一些常用的功能做了封装的
![Pasted image 20250925150315.png|500](/img/user/accessory/Pasted%20image%2020250925150315.png)

其实我们实际上就相当于在写库函数，那很多程序都需要这个功能，那为什么不封装起来呢？
而且很多时候，比如在32bit和64bit上的int类型的长度实际上是不一样的，那这就会带来很多移植性的问题，其实C语言标准的设计者也考虑到了这些问题，那其实就封装起了一些指定特定长度的类型，比如`int_32t`
eg. [cplusplus.com/reference/cstdint/](https://cplusplus.com/reference/cstdint/)
![Pasted image 20250925154216.png|500](/img/user/accessory/Pasted%20image%2020250925154216.png)

再比如其实并不是所有的系统调用都不是想fork一样算是很"紧凑”的最小接口，这个时候可能需要去把那些不太好用的syscall给封装起来
比如execve并不好用，execve前面已经学过了，在我们开启一个新进程的时候，做的实际上是先fork一个状态完全一样的进程，然后通过execve变成我们想要的那个程序的出状态
```cpp
extern char **environ;
char *argv[] = { "echo", "hello", "world", NULL, };
if (execve(argv[0], argv, environ) < 0) { 
	perror("exec"); 
}
```
这段代码实际上是错的，这是因为execve的第一个参数要求是一个合法的路径(可以看手册)
所以我们这里如果写`./../..//bin/echo`就对了，它实际上应该是在我们的env上面的路径上的echo
实际上有一些高情商的API, 比如execlp就帮我们封装好了这件事情
```c
execlp("echo", "echo", "hello", "world", NULL);
```
其实我们可以查看strace
![Pasted image 20250925160349.png|500](/img/user/accessory/Pasted%20image%2020250925160349.png)
能看到execlp实际上试环境变量中的pass每一个都拼上我们的这个命令来执行execve

总结一下实际上libc是对我们的系统调用和一些不好用，常用的东西做一些合适的封装
### 封装1: 纯粹的计算
libc可能封装的是纯粹的计算
比如memset，我们在学C语言的时候，实际上为了加深理解，真的手搓过strlen, strcat, ...这些处理字符串的函数
比如这里说的memset实际上就相当于一个笔刷，就是从一个指针开始的n个字节都涂成c
```c
void *memset(void *s, int c, size_t n) {
	for (size_t i = 0; i < n; i++) {
		 ((char *)s)[i] = c; 
	} 
	return s; 
}
```
但这段代码真的能行吗？
需要考虑多线程，要不要加锁？ 需要考虑够不够快，需要考虑底层CPU的优化...
那要不要上锁？ 实际上memset并不管
![Pasted image 20250925175136.png|500](/img/user/accessory/Pasted%20image%2020250925175136.png)

### 封装2: 文件描述符
封装操作系统里面的对象，其中这些对象可能有上节课讲的文件描述符，这些对象可能是一个终端，...
但是UNIX的哲学本质是Everything is a file. 所以说，实际上，理论上来说，可以通过文件描述符来访问操作系统里面的任何对象
所以libc当然要对文件描述符这样的东西做一个封装
```c
#include<stdio.h>
int main(){
	FILE *fp = fopen("a.txt","w");
	fprintf(fp, "Hello,OS World\n");
}
```
如果调用strace的话能看到
![Pasted image 20250925185506.png|500](/img/user/accessory/Pasted%20image%2020250925185506.png)
实际上是调用了openat的系统调用，并且返回了一个编号为3的文件描述符，然后调用了write系统调用把这个值写进去了
同样的我们也能在gdb中从应用的视角看到这些内容
![Pasted image 20250925190526.png|500](/img/user/accessory/Pasted%20image%2020250925190526.png)
在运行到第四行的时候去打印fp
![Pasted image 20250925185901.png|500](/img/user/accessory/Pasted%20image%2020250925185901.png)
能看到一个编号为3的文件描述符
![Pasted image 20250925190053.png|400](/img/user/accessory/Pasted%20image%2020250925190053.png)
比如在stdout中，这个为1，这是上节课讲过内容，也证明了FILE实际上本质上就是封装了一个文件描述符
实际上popen 和 pclose是一个设计有缺陷的API
Since a pipe is by definition unidirectional, the type argument may specify only reading or writing, _not both_; the resulting stream is correspondingly read-only or write-only.

### 封装3: 更多的进程/操作系统功能
所有 API 都可能失败
![Pasted image 20250925200103.png|400](/img/user/accessory/Pasted%20image%2020250925200103.png)
hhh这个报错是相同的message
所以这并不是每一个应用程序单独做的
我们甚至可以山寨出这个效果来
```c
 #include<stdio.h>
 #include<err.h>
 int main(){
   const char *fname = "xxx.c";
   FILE *fp = fopen(fname, "r");
   if(!fp){
     warn("%s",fname);
   }
 }
```
![Pasted image 20250925201133.png|500](/img/user/accessory/Pasted%20image%2020250925201133.png)
这个效果和上面的效果是一样的，如果我们strace一下的话
![Pasted image 20250925201231.png|500](/img/user/accessory/Pasted%20image%2020250925201231.png)
发现比较有意思的是他是好多个write分开打印的
如果我们strace上面的比如cat xxx.c的话，也能看到同样的效果
所以实际上他们就是靠的同一份library

封装计算机的状态
环境变量 env -- 打印所有的环境变量
![Pasted image 20250925202652.png|400](/img/user/accessory/Pasted%20image%2020250925202652.png)
这部分也有一个手册，`man 7 environ`,
![Pasted image 20250925202755.png|500](/img/user/accessory/Pasted%20image%2020250925202755.png)
environment是我们定义的一个符号，一个`char **`类型的
我们借助这个也可以实现自己的env
```c
 #include <stdio.h>
 int main() {
   extern char **environ;
   for (char **env = environ; *env; env++) {
     printf("%s\n", *env);
   }
 }
```
这是一个符号，所以在进程状态机reset的时候这个变量可能是空的，那到底是不是空的？如果是空的，那谁给的赋值的呢？
如果是`char**`类型的话，那应该是这样的
![Pasted image 20250925203116.png|400](/img/user/accessory/Pasted%20image%2020250925203116.png)
可以用gdb调试打断点
![Pasted image 20250925203309.png|500](/img/user/accessory/Pasted%20image%2020250925203309.png)
我们在starti之后打印environ能看到这个符号确实是空的
然后打watchpoint，continue
![Pasted image 20250925203435.png|500](/img/user/accessory/Pasted%20image%2020250925203435.png)
看到是`__libc_static_main_impl`给他赋的值，这是我们静态链接的
同样的，也能动态编译看一下是在哪里赋值的

### 封装4: 地址空间
libc另一个重要的功能是内存管理
主要是两个api, malloc和free
我们之前学过mmap
在我们的程序reset的时候，实际上在内存空间中，是有一些比如code, data, 在stack中也有一些东西，但其实也会有一些空的内存空间
我们的mmap系统调用会帮助我们在地址空间中找到一块空间的地址空间给我们
然后我们又可以在这样连续的大内存上面实现小内存的分配，这就是malloc和free
![Pasted image 20250925205224.png|400](/img/user/accessory/Pasted%20image%2020250925205224.png)
malloc和free是区间管理的一个问题
实际上这一块我们在CSAPP看过[[CSAPP Computer-System-A-Program-Perspective/Lecture 19 Dynamic Memory Allocation：Basic Concepts\|Lecture 19 Dynamic Memory Allocation：Basic Concepts]] [[CSAPP Computer-System-A-Program-Perspective/Lecture 20：Dynamic Memory Allocation：Advanced Concepts\|Lecture 20：Dynamic Memory Allocation：Advanced Concepts]]
做优化！ 要有workload！！！
可以去看业界上公认的一些paper，先了解他，去尝试模仿他，然后再谈打败它
工程经验的重要性
![Pasted image 20250925205845.png|500](/img/user/accessory/Pasted%20image%2020250925205845.png)
这符合Web Server, 数据库系统！
粗粒度 没有性能可言
system中最常见的解决问题的一种方案: Fast Path and Slow Path
- fast path
    - 性能极好、并行度极高、覆盖大部分情况
    - 但有小概率会失败 (fall back to slow path)
- slow path
    - 不在乎那么快
    - 但把困难的事情做好
        - 计算机系统里有很多这样的例子 (比如 cache)

malloc和free就应该是这样的逻辑
![Pasted image 20250925210937.png|400](/img/user/accessory/Pasted%20image%2020250925210937.png)
先在page中进行操作（绝大部分时候不需要lock），如果不够了再上锁去global去找page -- 拆锁 -- 先fast path再slow path
另一种方式是
![Pasted image 20250925212247.png|400](/img/user/accessory/Pasted%20image%2020250925212247.png)
？？这有点数据库堆文件的意思了


