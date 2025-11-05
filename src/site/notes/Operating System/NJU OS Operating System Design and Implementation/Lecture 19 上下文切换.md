---
{"created":"2025-10-16T19:08","updated":"2025-10-16T19:11","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 19 上下文切换/","dgPassFrontmatter":true,"noteIcon":""}
---

上节课
- ecall指令：跳转到trampoline代码
- 保存所有寄存器到trapframe
- 进入内核态继续执行

为什么这样 -- today：上下文切换的原理与实现

### 处理器的虚拟化
**为什么死循环不能使计算机彻底卡死？**
先来复习一下什么是程序，一个程序，比如xv6，实际上就是一个状态机。
我们可以在`/user`文件夹中看xv6上的程序，比如sh.c
![Pasted image 20251028094155.png|400](/img/user/accessory/Pasted%20image%2020251028094155.png)
他最终会被编译成riscv的指令集，sh.asm
![Pasted image 20251028094531.png|400](/img/user/accessory/Pasted%20image%2020251028094531.png)

![Pasted image 20251028094544.png|400](/img/user/accessory/Pasted%20image%2020251028094544.png)
能看到这些指令，实际上就是分成三种
- load/store
- reg运算: 比如mv, addi ...
- syscall(x86-64)/ecall(riscv)

![Pasted image 20251028095039.png|400](/img/user/accessory/Pasted%20image%2020251028095039.png)

然后复习一下什么是操作系统？
操作系统实际上就是程序状态机的集合，操作系统也是一个状态机
![Pasted image 20251028095134.png|400](/img/user/accessory/Pasted%20image%2020251028095134.png)
CPU会选择其中一个程序执行，意味着这个程序的register和memory(这里是虚拟内存)会直接放到CPU上,CPU做的事情实际上就是取出这个程序PC指针出的指令，然后执行
但如果这条指令是`jump .`的话，就是一直跳回当前指令，那按理说实际上会发生死循环，但为什么它不会把机器卡死呢？ 是因为还有一个额外的机制，叫做**中断**
![Pasted image 20251028095609.png|400](/img/user/accessory/Pasted%20image%2020251028095609.png)
有外部中断时，就像强行执行ecall，其实这就像外部有个人在戳你一样，隔一段时间，就会执行一下操作系统的代码
而能执行操作系统的代码就意味着，如果系统里面有好多个状态机，操作系统就能够选择继续执行当前程序还是换一个程序执行
![Pasted image 20251028095808.png|400](/img/user/accessory/Pasted%20image%2020251028095808.png)
我们假设的是我们只有一个CPU，虽然物理上我们只有一个CPU，中断这个机制使得在时间维度上还是可以一起运行很多进程，就是因为把1s切成了好多个部分，让CPU看起来虚拟成了好多个CPU

硬件会发生中断，就像强行插入了ecall指令，会切换到操作系统代码执行，操作系统代码可以切换到另一个进程执行，那这些过程到底是怎么在代码层面上发生的？

### 代码层次上到底是怎么发生的？
如果我们想写一个A和B可以相互切换的程序？我们会怎么写？
![Pasted image 20251028101209.png|400](/img/user/accessory/Pasted%20image%2020251028101209.png)
一个好的方法是通过协程，什么是协程？
协程（Coroutine）是一种比线程更轻量级的用户级线程。它允许一个函数（或一段代码）在执行过程中暂停，将控制权交给另一个函数，然后在适当的时候再恢复执行
其实协程的核心就是那个`co_yield()`, 叫snapshot and switch，它的所有局部变量、执行到哪一行代码等上下文信息都会被保存起来。当它恢复时，可以从上次暂停的地方无缝地继续执行，就像从未离开过一样
那如果我们的编译器能够保证一段时间就插入一个`co_yield()`,是不是就像是我们在用户态模拟出了一个操作系统？
把协程看做线程或者进程，把yield调用的外面的代码看做OS
![Pasted image 20251028101536.png|350](/img/user/accessory/Pasted%20image%2020251028101536.png)
那xv6到底是怎么进行进程 上下文切换的
![Pasted image 20251028170400.png|400](/img/user/accessory/Pasted%20image%2020251028170400.png)
中间这一个就是trapframe，保存寄存器的地方，每一个进程都有一个指针，能够指向他自己的trapframe