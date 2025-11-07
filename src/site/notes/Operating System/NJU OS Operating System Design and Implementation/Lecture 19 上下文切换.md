---
{"created":"2025-10-16T19:08","updated":"2025-11-07T15:27","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 19 上下文切换/","dgPassFrontmatter":true,"noteIcon":""}
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
复习程序的状态，一个xv6程序的状态是由内存(trampoline， trapframe， user stack, heap, user test and data)和寄存器(图中的x0，x1，，，)
![Pasted image 20251107125201.png|300](/img/user/accessory/Pasted%20image%2020251107125201.png)
在操作系统中还有程序不可见的东西，比如文件描述符，程序只能看见文件描述符这个数字，不知道文件描述符后面有什么(我们之间遇到过管道的原理，其实本质上就是偷换了文件描述符数字背后的东西)；除此之外，还有很多操作系统的对象是不可见的
我们在gdb中的调试就和这个程序的状态是对应起来的
比如说下面这种情况
![Pasted image 20251107130640.png|500](/img/user/accessory/Pasted%20image%2020251107130640.png)
cpu的状态实际上就是取出PC所指位置的指令，然后执行
但总有一天会执行的ecall或者中断的时候
如果执行到ecall，就会转到内核态执行
ecall指令会保存一些寄存器的状态，关中断，但它最后还是做了一个跳转，就是将stvec这个寄存器的值给了pc，有意思的是，stvec这个寄存器的值对用户态的程序是不可见的，换句话说，就是这个值不能改，是由我们操作系统提前配置好的
那跳转到内核态，到底先干了什么？
![Pasted image 20251107133857.png|400](/img/user/accessory/Pasted%20image%2020251107133857.png)
实际上操作系统帮我们保持了一种invariant(不变)，这段代码实际上帮助我们将现在CPU上正在运行的进程的相关寄存器内存的信息保存封装在了一个结构 - PCB，从而将整个操作系统中的所有用户态的进程都封装成了一个统一的样式和结构，比如说里面有register，都有memory，而memory又指向了一个数据结构，然后这个数据结构又指向了很多物理页...
操作系统可以看到一个struct proc的列表，这个列表指向了每一个进程，使得操作系统能够遍历操作系统中的所有的进程
![Pasted image 20251107134346.png|300](/img/user/accessory/Pasted%20image%2020251107134346.png)
当正是开始执行系统调用的时候，如果系统调用很快，不需要等待，内核直接就准备返回。但如果系统调用很慢或者发生阻塞，内核会调用schedule，schedule会选择现在所有封装好的进程的任意一个，将它恢复到CPU上！(这里就涉及到了调度算法)
到底是怎么保存的？
![Pasted image 20251107135723.png|400](/img/user/accessory/Pasted%20image%2020251107135723.png)
xv6的封存是用到了一个用户进程看不见的临时寄存器，叫做sscratch；这里做了一件事儿，就是将sscratch寄存器的值和a0寄存器的值交换了，原来sscratch寄存器存的值就是0x3fffffe000，就是trapframe的启示的地方，这样a0原来的值就被存了下来，然后就能把a0寄存器拿来用了
这里执行了一条`sd ra,40(a0)`的指令，实际上是将ra寄存器中的值，给了虚拟地址`[$a0+40]`即`[0x3fffffe000+40]`, 即物理地址`[0x87f77000+40]`
而实际上trapframe这部分内存就解读成了一个结构体，c语言中结构体就是一个地址，而这个结构体中，偏移40的地方就是ra的值
![Pasted image 20251107140404.png|500](/img/user/accessory/Pasted%20image%2020251107140404.png)
最后保存的是a0，具体是把sscrach的值给了t0 然后通过t0保存了原来a0的值
![Pasted image 20251107141037.png|500](/img/user/accessory/Pasted%20image%2020251107141037.png)
然后保存的是属于这个进程的一些操作系统的状态，比如内核的页表`kernel_stap`, 内核栈指针`kernel_sp`等...
后面的代码实际上是做了一个空间地址到空间地址的原子性的完整的切换，代码彻底进入内核态
```asm
 # restore kernel page table from p->trapframe->kernel_satp
        ld t1, 0(a0)
        csrw satp, t1
        sfence.vma zero, zero
```
具体来说，就是a0寄存器指向的地址，加载到了t1寄存器，然后通过csrw，将t1的寄存器的值写入CPU的stap寄存器中。
stap寄存器是什么？ 这是RISC-V架构中负责地址翻译的核心控制寄存器，告诉CPU的内存管理单元，按照这个物理地址上的页表来进行所有的虚拟地址到物理地址的翻译。这样，就完全进入了内核态
最后一句实际上是清楚TLB这个高速缓存
![Pasted image 20251107142712.png|300](/img/user/accessory/Pasted%20image%2020251107142712.png)
这个时候，我们就看到了一个完全不一样的地址空间
实际上这个时候代码就执行到了kernel/trap.c
![Pasted image 20251107143126.png|400](/img/user/accessory/Pasted%20image%2020251107143126.png)
在usertrap中一开始还会进行一点状态的保存
然后大约到53行的时候，就已经完成了所有的封存
这个时候实际上操作系统内核就能通过改这个封装完的结构(我觉得是PCB)，就能任意修改这个状态机的状态
将每一个进程都封装好，能够用指针去访问它之后，就可以做一个处理器的调度，当前进程的中断或者系统调用没有返回的时候，就可以让别的进程上CPU运行一会儿
![Pasted image 20251107150043.png|400](/img/user/accessory/Pasted%20image%2020251107150043.png)
x86-64和RISCV还不太一样，因为x86-64中断和议程会伴随堆栈切换，所谓的堆栈切换就是CPU的栈指针寄存器从指向一块内存区域(旧堆栈)，改像另一块完全不同的内存区域(新堆栈)的过程。
但x86带来的好处是中断处理程序变简单了，这里的简单主要指的是操作系统内核需要手写的，用于保存现场的汇编代码更少了

回到ecall的代码
我们虽然说一个进程的状态有register和memory，但实际上，真实的世界，我们的操作系统中只有一个物理内存，应用进程中只有一个satp的寄存器，就像个给程序带了一个VR眼镜，实际上satp程序指向一个数据结构(当然这个数据结构也是存储在真实的物理地址中的)，它又会指向一个个物理空间 (其实这个数据结构就是页表？)