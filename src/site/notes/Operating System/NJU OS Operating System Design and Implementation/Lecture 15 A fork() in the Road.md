---
{"created":"2025-10-03T14:08","updated":"2025-10-03T17:41","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 15 A fork() in the Road/","dgPassFrontmatter":true,"noteIcon":""}
---

today's agenda
能不能用系统调用实现比普通业务逻辑代码更有趣的东西？

### fork()行为的补充解释 文件描述符
之前其实讲过什么叫做fork？
什么叫做fork？就要先说操作系统，操作系统是一个大的状态机，操作系统这个大的状态机中有很多小的程序, 进程的小的状态机，当然除此之外还有一些操作系统自己的状态，比如操作系统的对象(之前讲过管道，文件描述符就在这一部分)；程序可以放在CPU上去执行，改变程序自己状态机的状态，还可以调用系统调用，交给操作系统，那可能就会改变操作系统这个状态机的一些状态，比如fork就是复制了一个完全一样状态的程序，除了fork的返回值不同（父进程返回子进程pid，子进程返回0）
execve是重置状态机的状态，比如我把fork出来的子进程重置成echo的初始状态，但是实际上是不会重置里面原来存在的文件描述符的
文件描述符实际上就是一个指向操作系统内对象的 “指针”。从 0 开始编号 (0, 1, 2 分别是 stdin, stdout, stderr)
![Pasted image 20251003142212.png|400](/img/user/accessory/Pasted%20image%2020251003142212.png)
这就成就了 管道

open系统调用实际上是一种获得文件描述符的方法
![Pasted image 20251003150730.png|300](/img/user/accessory/Pasted%20image%2020251003150730.png)
open实际上会分配一个新的描述符，会指向我们要打开的对象或者说是file
如果我们去读手册，会发现有一些比较有趣的事情
![Pasted image 20251003150913.png|500](/img/user/accessory/Pasted%20image%2020251003150913.png)
实际上对于file来说是由一个offset的概念的，这是我们之前讲pipe所没有的东西
文档上说open一个file有一种叫O_APPEND的方式，这种方式是一种追加写入的方式，每次写入的时候，offset都会在文件的末尾
![Pasted image 20251003151317.png|500](/img/user/accessory/Pasted%20image%2020251003151317.png)
Linux2.6.23之后有一种叫O_CLOEXEC的模式，实际上就是在execve重置的时候，不会保留或者说继承下父类的文件描述符，这是一种比较安全的方式
既然存在offset，就存在一些问题，比如下面这段代码
```cpp
#include<fcntl.h>
#include<unistd.h>

int main(){
   int fd = open("a.txt", O_WRONLY | O_CREAT | O_TRUNC, 0666);
   int pid = fork();
   if(pid == 0){
      write(fd,"Hello",5);
   }else{
      write(fd,"World",5);
    }
 
 }
```
这也是设计者需要考虑的事情，到底应该是HelloWorld. 还是相互覆盖？
事实上，我们write的语义是偏向往后追加的，所以实际上是会追加的，只不过我运行了好多遍都是WorldHello，这好像是因为我的父进程总会抢跑
![Pasted image 20251003152934.png|500](/img/user/accessory/Pasted%20image%2020251003152934.png)
而且我们能在write的文档中看到这一部分的阐述的，文档说write应该是atomic的，但其实linux一开始是没有这个保证的，是3.14后才有的

实际上之前我们还提到过一个dup系统调用
![Pasted image 20251003153131.png|500](/img/user/accessory/Pasted%20image%2020251003153131.png)
文档是这么说的，实际上dup就是一种copy，实际上就是会有一个新的文件描述符也指向同一个file
![Pasted image 20251003153237.png|300](/img/user/accessory/Pasted%20image%2020251003153237.png)
那前面我们提到了offset，dup之后的两个文件描述符是共享的同一个offset吗？
实际上手册里面讲的非常清楚

还有一个有意思的问题是，fork的时候我们说的是把整个状态机都复制一遍，实际上我们马上就能想到这可能是一个很大的开销，甚至复制后会接着execve
但实际上，计算机是复制但没完全复制的 -- **Copy-on-write**
在真正实现上，实际上进程的数据是分页的，我们从pmap看到这部分内存是属于进程的，但是实际上这个页面并不是属于进程的，而是属于操作系统的
进程实际上只是一个映射表，操作系统有一个CR3，这个叫做页目录基地址寄存器，使得映射表的地址通过地址翻译映射到操作系统给他分配的页面
![Pasted image 20251003155858.png|300](/img/user/accessory/Pasted%20image%2020251003155858.png)
那事实上如果执行fork，实际上只是复制了一个引用，同时给页面加了一个额外的引用计数。
但实际上第二个页面是可写的，如果两个进程都不写，那没关系；但如果想写，按理说应该是复制一份，这应该会被写成两个不一样的页面
操作系统在这里做了一个小的技巧，他暂时的把写的权限抹掉了
![Pasted image 20251003160108.png](/img/user/accessory/Pasted%20image%2020251003160108.png)
如果不去写没事，如果去写的话，会发生一个缺页中断，然后操作系统就会看这个缺页中断到底是不是非法的访问，如果是合法的访问，并且操作系统实际上知道这是可写页面只不过暂时被抹掉了，那么操作系统就会拷贝一份页面，然后原来的计数会减1
![Pasted image 20251003160254.png](/img/user/accessory/Pasted%20image%2020251003160254.png)
那这样的好处实际上就是在fork完成之后接着execve的时候，事实上绝大多数复制完接着就被重置了，改写的只有少数页面，就没有带来很多额外的开销
除此之外，像是libc的代码，实际上在整个系统中实际上是只有一份的

### 状态机，fork，魔法？
![Pasted image 20251003163843.png|300](/img/user/accessory/Pasted%20image%2020251003163843.png)
![Pasted image 20251003164024.png](/img/user/accessory/Pasted%20image%2020251003164024.png)

可是创建这种平行宇宙能干什么？
搜索 -- 在不同的平行宇宙中探索不同的情况
![Pasted image 20251003164206.png|200](/img/user/accessory/Pasted%20image%2020251003164206.png)
换句话说，fork可以来实现传统的dfs，没有回溯了
fork出来的天生就是四个分离的进程(比如上下左右)，可以平行执行
可以backjump(exit)而不是一层一层回
![Pasted image 20251003165845.png|300](/img/user/accessory/Pasted%20image%2020251003165845.png)

另一个应用：可以直接fork init执行完的状态，从这个状态开始执行程序，这就用一份nemu_init的时间，执行了多分程序
![Pasted image 20251003171053.png|400](/img/user/accessory/Pasted%20image%2020251003171053.png)
实际上这在很多地方都有应用，比如Android的应用看起来都是秒起的，他们背后都是这么启动的，Android实际上是Java程序，Java运行在JVM上，Android中叫ART(Android Runtime), Java的加载的标准 内容是很多的
在Android中一个Zygote Process这样的根，它实际上就是加载好了所有该加载的东西，用的时候直接fork一份开始执行就行

![Pasted image 20251003172146.png|400](/img/user/accessory/Pasted%20image%2020251003172146.png)
？？这不就是电视剧里面的循环吗哈哈哈
![Pasted image 20251003172241.png|500](/img/user/accessory/Pasted%20image%2020251003172241.png)


### A fork() in the road
一群微软大佬批评fork哈哈哈
![Pasted image 20251003173632.png|400](/img/user/accessory/Pasted%20image%2020251003173632.png)
信号：Ctrl+C 都返回嘛？
线程
![Pasted image 20251003173908.png|400](/img/user/accessory/Pasted%20image%2020251003173908.png)
正确答案是左边那个
更安全的API: POSIX Spawn
![Pasted image 20251003174102.png|400](/img/user/accessory/Pasted%20image%2020251003174102.png)
