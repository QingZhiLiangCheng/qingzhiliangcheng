---
{"created":"2025-09-23T16:36","updated":"2025-09-23T22:15","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 13 系统调用和UNIX Shell/","dgPassFrontmatter":true,"noteIcon":""}
---

我们是操作系统的用户，但操作系统提供的API并不是"我们"作为人类用户直接使用的，那"我们"怎么用操作系统？
我们接触的操作系统可能就是看见鼠标拖一下窗口，点击一下，我们所接触的操作系统就是使用运行在上面的app，这节课就是将之前讲过的比较枯燥的比如说系统调用和我们所认识的操作系统联系起来
**today's agenda**
UNIX Shel的设计和实现

### Shell
我们花了一大把的时间学了操作系统执行的整个过程，从CPU rest开始到Firmware加载操作系统，操作系统一开始只有一个程序，然后通过系统调用来创建进程，线程，改变状态...
我们甚至在上节课体会了一个小的linux-minimal的例子
但是我们对操作系统的第一印象是我可以双击某个按钮，就可以打开某个应用...而非系统调用
那就一定有一个程序，承担了管理所有的交互设备和实现人机交互的重任，第一个这样的程序就是Shell
Shell的本意就是果壳，再操作系统中，虽然我们说每个程序都是一个状态机，但是shell更像是一个包裹着kernel的壳，kernel中存在着syscall. shell会调用系统调用，同时他也会管理着我们所有的交互设备，从而把我们的用户指令翻译成系统调用
原来就是command，后来才有了graphical shell
操作系统在启动以后实际上就是一个系统调用的执行者和终端的管理者，退居二线；shell就是从我们的物理世界接受用户的输入翻译成系统调用


### 复刻经典
```shell
wget https://jyywiki.cn/pages/OS/2022/demos/s
h-xv6.c
```
这是老师从xv6扒下来的一个shell的程序，并且做了一些简单的处理
这个shell是可以通过下面的命令编译链接运行的
```shell
gcc -c -ffreestanding sh-xv6.c  -g -O2
ld sh-xv6.o -o sh
```

如果看源码的话，能发现这个sh-xv6.c是没有main函数的，他不依赖于libc库，他的其实函数是通过`_start`进的
![Pasted image 20250923174859.png|500](/img/user/accessory/Pasted%20image%2020250923174859.png)
能看到实际上start入口很简单，就是通过getcmd函数获取cmd中输入的命令存入buf
![Pasted image 20250923175023.png|400](/img/user/accessory/Pasted%20image%2020250923175023.png)
getcmd事实上做的就是一个一个字符的读取碰到回车结束
其他实际上就是在做解析命令和对应的系统调用
比如start中实际上右对cd命令的匹配，而且很有意思，要匹配`buf[0]和buf[1]`,也就是说在我们这个shell中，不支持前面好多空格再输入cd，然后去除回车然后把后面的东西都传给系统调用
这里用的是一个syscall的函数
![Pasted image 20250923194430.png|500](/img/user/accessory/Pasted%20image%2020250923194430.png)
syscall函数就是执行系统调用，做的事情很简单，就是把参数准备好，然后一条syscall指令
可以看一下`man 2 syscall`的文档
![Pasted image 20250923194903.png|500](/img/user/accessory/Pasted%20image%2020250923194903.png)
![Pasted image 20250923194916.png|500](/img/user/accessory/Pasted%20image%2020250923194916.png)
对于x86来说是明确规定了哪个寄存器存哪个系统调用的参数，也明确规定了系统调用的编号应该在rax中
![Pasted image 20250923200923.png|500](/img/user/accessory/Pasted%20image%2020250923200923.png)
确实是这样，在我们即将进入syscall的时候，我们把`\bin\`放入了rdi寄存器
![Pasted image 20250923201459.png|500](/img/user/accessory/Pasted%20image%2020250923201459.png)
这里也能看到rax是50 -- `RAX` 的值是 `0x50` (也就是 80)，这告诉内核：“我想要执行编号为 80 的系统调用”，即 `chdir` -- `chdir` 是一个面向进程的系统调用，用于改变进程的当前工作目录

如果不是cd的话，start中所写的逻辑是，会创建一个新的进程，然后把这个命令解析，这样做的原因是，可以只分配内存不回收

shell的主要的功能就是一个程序解释器，而这个程序解释器所做的事情就像之前学数据结构时的表达式求值，用一颗二叉树
![Pasted image 20250923202321.png|300](/img/user/accessory/Pasted%20image%2020250923202321.png)

![Pasted image 20250923202622.png|500](/img/user/accessory/Pasted%20image%2020250923202622.png)
比如LIST就是先执行左边，右边wait，然后在执行右边

什么是管道？

![Pasted image 20250923202830.png|300](/img/user/accessory/Pasted%20image%2020250923202830.png)
管道简单说就是将左边的输出当做输入放入右边
![Pasted image 20250923203942.png|300](/img/user/accessory/Pasted%20image%2020250923203942.png)
能看见第一步是调用了pipe的syscall
可以通过`man 2 pipe`查看管道的文档
![Pasted image 20250923203747.png|500](/img/user/accessory/Pasted%20image%2020250923203747.png)
文档说，管道这个系统调用就是创建了一个管道，然后创建了两个文件描述符，其中`pipefd[0]`是管道的读口，`pipfd[1]`是管道的写口
![Pasted image 20250923204011.png|300](/img/user/accessory/Pasted%20image%2020250923204011.png)
然后再140行立即执行了一个fork系统调用，fork就是把进程拷贝了一遍，然后管道还是那一个管道，fork会把文件描述符复制一份，也就是说，父子进程共享了这个管道
![Pasted image 20250923204223.png|300](/img/user/accessory/Pasted%20image%2020250923204223.png)
然后他做了这么一件事情
```c
140       if (syscall(SYS_fork) == 0) {
141         syscall(SYS_close, 1);
142         syscall(SYS_dup, p[1]);
143         syscall(SYS_close, p[0]);
144         syscall(SYS_close, p[1]);
145         runcmd(pcmd->left);
146       }
```
他在fork=0 子进程中把编号为1的文件描述符给关掉了，编号为1的文件描述符是std out(标准输出), 然后把p1这个文件描述符拷贝了一份，所谓拷贝就是说这个文件描述符指向哪里，就找一个空的数值最小的文件描述符也指向那里，所以dup做的就是把刚刚关掉的那个描述符指向了`p[1]`，即管道的写口
![Pasted image 20250923204749.png|300](/img/user/accessory/Pasted%20image%2020250923204749.png)
然后他把p0,p1给关掉了，这样我们新创建出来的子进程实际上就是一个标准输出指向写口，其他状态不变的进程
![Pasted image 20250923204824.png|300](/img/user/accessory/Pasted%20image%2020250923204824.png)

而这个进程执行了pcmd left, 实际上就是左边的命令，也就是说左边命令的输出写入了管道
后面的代码是
```cpp
147       if (syscall(SYS_fork) == 0) {
148         syscall(SYS_close, 0);
149         syscall(SYS_dup, p[0]);
150         syscall(SYS_close, p[0]);
151         syscall(SYS_close, p[1]);
152         runcmd(pcmd->right);
153       }
```
在执行完上一个fork=0中的代码后，会回到父进程，这时候父进程又执行了一个fork，然后再子进程中将编号为0的文件描述符(std in, 标准输入)关掉，然后拷贝了一份读口到编号为0的文件描述符，同样关闭p0,p1,然后执行右边的命令
![Pasted image 20250923205502.png|400](/img/user/accessory/Pasted%20image%2020250923205502.png)
后面
```cpp
156       syscall(SYS_wait4, -1, 0, 0, 0);
157       syscall(SYS_wait4, -1, 0, 0, 0);
```
执行了两个wait，也就是说要等左边和右边执行完，父进程才会就执行

这里其实有一个疑问，为什么原来的标准输出指向了管道的读口，左边命令的运行结果就能写入管道？
Unix的哲学本质是"Everything is File."，以及与之配套的文件描述符机制，程序不关心“目的地”，只认门牌号，如果调用printf, 他只知道把结果发送给1号文件描述符，而不知道是要显示在屏幕中，原来1是Shell启动时默认打开的文件描述符，它是默认连接终端屏幕的，而我们在管道中dup做的事情就是把门牌号的实体给换了。具体来说就是先把原来挂在 1号门 上的“通往屏幕”的牌子给摘掉了，这样1号门是空的，然后1是最小的空位，然后在调用dup的时候，将通往管道的写端的牌子挂在了1号门上

这里老师讲了一个小的调试技巧
```shell
strace -f -o /tmp/strace.log ./sh
```
把系统调用定位到一个文件中，然后可以`tail -f /tmp/strace.log`再文件中查看系统调用，而且指向两次命令之间可以在文件中换行分隔开两个命令的系统调用
![Pasted image 20250923211351.png|500](/img/user/accessory/Pasted%20image%2020250923211351.png)
然后会发现 pipe真的是这样的
![Pasted image 20250923211900.png|400](/img/user/accessory/Pasted%20image%2020250923211900.png)


### 终端和 Job Control
为什么有时候Ctrl+C能退出程序？ 为什么有时候Ctrl+C不能退出程序？
没有人 read 这个按键，为什么进程能退出？
Ctrl-C 到底是杀掉一个，还是杀掉全部？
tmux怎么实现的？为什么可以管理多个窗口？
Answer: 终端！

有一个命令叫tty，tty是这么说的
![Pasted image 20250923214259.png|400](/img/user/accessory/Pasted%20image%2020250923214259.png)

如果在tmux中运行tty
![Pasted image 20250923214350.png|400](/img/user/accessory/Pasted%20image%2020250923214350.png)
会发现tmux开的每一个窗口都是一个新的终端
然后就可以把打印的东西打印到另一个终端上
![Pasted image 20250923214539.png|400](/img/user/accessory/Pasted%20image%2020250923214539.png)
实际上tmux的实现就是不管在那个窗口上所按下的按键都会捕捉到，然后传输给对应的终端
我们的hello程序是怎么识别出终端的？
当然是系统调用
![Pasted image 20250923215553.png|400](/img/user/accessory/Pasted%20image%2020250923215553.png)
直接在终端打印和定向到文件中，是有一个不同点的
在这里实际上是看的1是不是标准输出，如果是标准输出就是终端，如果不是终端，会额外调用一个io control这样的syscall

Ctrl+C能够跳出程序的根本原因是操作系统有一个信号机制
这好像在CSAPP学过啊 [[CSAPP Computer-System-A-Program-Perspective/Lecture 15 Exceptional Control Flow： Signals and Nonlocal Jumps\|Lecture 15 Exceptional Control Flow： Signals and Nonlocal Jumps]]
这个信号是我们的终端产生的，简单讲，就是终端会给前台进程发送一个信号，比如C会发送一个SIGINT的信号，而这个前台进程会有对应信号的处理程序，如果前台程序没有注册过SIGINT这个信号的话就是退出

如果我们登录启动一个shell，它会打开一个叫Session的东西，Session中有很多进程组，进程组可以放前台可以放后台
进程组里面的一个进程如果fork的话，还属于同一个进程组
![Pasted image 20250923220749.png|500](/img/user/accessory/Pasted%20image%2020250923220749.png)
Ctrl+C会发送给所有的前台的进程组里的进程，这样就可以一起退出
tmux开好几个终端，其实和我们现在 开好几个窗口，没有本质的区别
![Pasted image 20250923221501.png](/img/user/accessory/Pasted%20image%2020250923221501.png)

