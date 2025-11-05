---
{"created":"2025-10-14T09:14","updated":"2025-10-28T09:09","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 18 xv6代码导读/","dgPassFrontmatter":true,"noteIcon":""}
---

### xv6 overview
xv6: UNIX v6的现代"克隆"
可以从github上下载 注意不是xv6-public，我们需要的是xv6-riscv 额我发现我转过xv6了，应该装xv6-riscv 这才是新版的那个xv6 (2020年以后)
呃呃先后经历了多个版本代码，终于找到和老师一样的了 我哭死😭
```shell
# no
git clone https://github.com/mit-pdos/xv6-public.git
# yes
git clone https://github.com/mit-pdos/xv6-riscv
# 但是要回退导2022年的时候 我随便挑了一个
git checkout 74c1eba516fdb0ec1a17b16be7e76613ccba92bf
# 或者用这个 我后面用的这个
git clone https://github.com/Ran1s/xv6-labs-2021.git
```

可以运行
```shell
make qemu
```

![Pasted image 20251016195544.png|400](/img/user/accessory/Pasted%20image%2020251016195544.png)
而且我 `sudo update` 不行, 因为我的Ubuntu能找到的最新版本的qemu为5.6
最后是参考了这篇blog[Ubuntu 22.04 安装 QEMU 流程 - arcsin2 的个人博客](https://arcsin2.cloud/2023/03/03/Ubuntu-22-04-%E5%AE%89%E8%A3%85-QEMU-%E6%B5%81%E7%A8%8B/)
最终得到了一个真的xv6的虚拟机，可以按Ctrl A接着按C退出
![Pasted image 20251019102451.png|400](/img/user/accessory/Pasted%20image%2020251019102451.png)

在这里可以得到完整的UNIX体验，包括管道，重定向等等

xv6提供了21个系统调用，都是我们常用的系统调用
![Pasted image 20251014094550.png|500](/img/user/accessory/Pasted%20image%2020251014094550.png)

之前我们有一个sh-xv6.c的代码，是一个可以零依赖直接在Linux上跑起来的shell
```shell
wget https://jyywiki.cn/pages/OS/2022/demos/sh-xv6.c
```
当时老师讲了对各个系统调用的一些简单的实现，尤其是pipe
如果通过`:%!grep syscall`过滤一下，就能看到我们实现这个shell的所有的系统调用
![Pasted image 20251014095242.png|400](/img/user/accessory/Pasted%20image%2020251014095242.png)
实现shell是这样，那实际上有了这些系统调用，就能去实现linking loader, text editor, assembler....
更重要的是学习代码的规范...
![Pasted image 20251023101453.png|500](/img/user/accessory/Pasted%20image%2020251023101453.png)
偏移量在左边写了注释
![Pasted image 20251014100433.png|500](/img/user/accessory/Pasted%20image%2020251014100433.png)

xv6有一份手册: [xv6: a simple, Unix-like teaching operating system](https://jyywiki.cn/pages/OS/manuals/xv6-riscv-rev2.pdf)

### xv6中的进程
init.c文件中的内容
![Pasted image 20251016194741.png|350](/img/user/accessory/Pasted%20image%2020251016194741.png)
这是第一个用户态的进程
它的事情是打开一个叫做console的文件，如果这个文件不存在，就创建它。然后做了一件事是`dup(0),dup(0)`
这个过程是这样的:
一开始进程里面一个文件描述符都没有，甚至连console的程序也没有，那实际上他会先把console创建出来，`mknod("console", 1, 1);`：`mknod` (make node) 是一个系统调用，用于创建一个特殊文件（设备文件）。这行代码的意思是：“请在文件系统中创建一个名为 `console` 的设备文件，它的主设备号是1，次设备号是1”。内核看到这个设备号，就知道任何对这个文件的读写都应该被路由到控制台驱动程序；然后再调用open系统调用，open系统调用实际上打开一个文件，然后返回一个文件描述符，在这里返回的是文件描述符0
![Pasted image 20251016161732.png|400](/img/user/accessory/Pasted%20image%2020251016161732.png)
然后两个dup(0)分别将stdout文件描述符(1)和stdin文件描述符(2)和console联系起来了
后面的for循环实际上是不断启动一个shell，通过fork父进程然后子进程调用exec，保留了父进程的文件描述符表。如果shell退出了会立马起一个新的

![Pasted image 20251016163409.png|400](/img/user/accessory/Pasted%20image%2020251016163409.png)
这是文档中所写的xv6的初始状态

### 配置Debug调试
可以在xv6-labs-2021目录下，`make qemu-gdb`
![Pasted image 20251023215857.png|500](/img/user/accessory/Pasted%20image%2020251023215857.png)
然后可以在另一个shell中，同等目录下启动gdb
```shell
gdb
(gdb) set architecture riscv:rv64
(gdb) file kernel/kernel
(gdb) target remote localhost:26000
(gdb) break main
(gdb) continue
```

![Pasted image 20251023220427.png|500](/img/user/accessory/Pasted%20image%2020251023220427.png)
能看到报错了，而且在设置riscv调试的时候，它觉得undefined，我查了一下，实际上pwndgb里面是包了一个gdb，可能里面的gdb不支持，所以我把我默认的gdb启动pwndgb改掉了
```shell
vi ~/.gdbinit
```
![Pasted image 20251023220658.png|300](/img/user/accessory/Pasted%20image%2020251023220658.png)
然后又运行了一遍原生的gdb
![Pasted image 20251023221012.png|300](/img/user/accessory/Pasted%20image%2020251023221012.png)
依然不行，所以我发现是gdb的问题，通过查询资料，我装了gdb-multiarch
```shell
sudo apt install gdb-multiarch
```

![Pasted image 20251023221148.png|500](/img/user/accessory/Pasted%20image%2020251023221148.png)
成功了！然后就可以通过`layout src`进行调试了
![Pasted image 20251023221218.png|500](/img/user/accessory/Pasted%20image%2020251023221218.png)

但是这种时候gdb可能就不如可视化的CLion，Vscode的调试了，我这里是配置的CLion，具体配置如下
工具链是我之前写CMU15445就一直配置好的
![Pasted image 20251023221347.png|500](/img/user/accessory/Pasted%20image%2020251023221347.png)
一开始Makefile构建不成功，需要执行下面的配置
首先我配置了Make的执行地址, `\\wsl\Ubuntu-22.04\user\bin\make`
![Pasted image 20251023221408.png|500](/img/user/accessory/Pasted%20image%2020251023221408.png)
工具链还是 wsl，build target 改为qemu，clean 还是 clean
![Pasted image 20251023221437.png|500](/img/user/accessory/Pasted%20image%2020251023221437.png)
然后重新构建整个项目
之后进行运行/调试配置，如下图
![Pasted image 20251023221518.png|500](/img/user/accessory/Pasted%20image%2020251023221518.png)
值得注意的是，一开始显示找不到main.c等，能运行但是会直接跳过断点
![Pasted image 20251023221714.png|500](/img/user/accessory/Pasted%20image%2020251023221714.png)
一定要加那个路径映射！
![Pasted image 20251023221906.png|500](/img/user/accessory/Pasted%20image%2020251023221906.png)
成功！
![Pasted image 20251026163623.png|500](/img/user/accessory/Pasted%20image%2020251026163623.png)
真的能够调试起来了
### 调试第一个系统调用
RISC-V 通电以后，会运行保存在ROM中的引导程序（BootLoader），引导程序将xv6内核加载到物理地址为0x80000000。  
然后在机器模式下，CPU从kernel/entry.S开始运行`xv6 _entry`。
我用gdb打了一个断点
![Pasted image 20251026165043.png|400](/img/user/accessory/Pasted%20image%2020251026165043.png)
`_entry`函数，主要用于开辟栈空间，以便后续C代码运行  
每个CPU都有自己的栈，把栈地址存入sp寄存器，接着_entry跳转到start执行C代码
![Pasted image 20251026170025.png|400](/img/user/accessory/Pasted%20image%2020251026170025.png)
entry最后是跳转到了start
![Pasted image 20251026170105.png|500](/img/user/accessory/Pasted%20image%2020251026170105.png)

start.c文件中运行start()函数  
RISCV提供了mret指令，这个指令一般是从前一个Supervisor模式转换到Machine模式的  
调用返回。但是start并没有从这样的调用返回。  
所以，start函数假装自己从这样的调用返回。  
在mstatus寄存器设置运行模式为Supervisor，以便进入内核  
mepc寄存器设置为main函数地址，将返回地址设为main  
satp寄存器写入0，禁用虚拟地址转换。  
最后通过mret指令进入main函数
![Pasted image 20251026170459.png|500](/img/user/accessory/Pasted%20image%2020251026170459.png)

在main函数中，经过一系列初始化设备和子系统后，通过调用userinit创建第一个子进程
![Pasted image 20251026170735.png|500](/img/user/accessory/Pasted%20image%2020251026170735.png)

实际上是用到了initcode,uvminit实际上是创建了一个新的地址空间，把intcode这部分代码搬进去了
![Pasted image 20251026171025.png|500](/img/user/accessory/Pasted%20image%2020251026171025.png)

intcode如下
![Pasted image 20251026171050.png|400](/img/user/accessory/Pasted%20image%2020251026171050.png)
而这个正好是user态中的intcode.out是对应起来的，0x17, 0x05, 0x00, 0x00对应00000517
![Pasted image 20251026164801.png|500](/img/user/accessory/Pasted%20image%2020251026164801.png)
指的最后的是最后是把7写入了a7，然后调用了ecall
这一部分其实可以在gdb中调试，可以通过一个`b *0`在0处打断点，然后continue之后一直s下一步，就真的是我们看到的这些语句
然后可以调试ecall了, ecall是什么？
ecall实际上就是riscv的syscall 可以通过查看`man 2 syscall`文档，并且能看到a7寄存器使用了存放系统调用号(syscall number)的
![Pasted image 20251026171618.png|500](/img/user/accessory/Pasted%20image%2020251026171618.png)
如果查看kernel文件下的syscall.h的话，能看到7实际上是调用了exec
![Pasted image 20251026171806.png|300](/img/user/accessory/Pasted%20image%2020251026171806.png)

这就是说，第一个状态就是一小段汇编代码initcode，执行了exec，变成了`/init`



![Pasted image 20251026173450.png|450](/img/user/accessory/Pasted%20image%2020251026173450.png)
这是文档中给出的答案
**If the trap is a device interrupt, and the sstatus SIE bit is clear, don’t do any of the following.**  
如果这个 trap 是 **设备中断**，并且 **sstatus 寄存器中的 SIE（Supervisor Interrupt Enable）位被清零**，那么 **下面的操作都不执行**。
意思是，如果内核中断被禁止（SIE=0），设备中断不会被处理。
**Disable interrupts by clearing the SIE bit in sstatus.**  
通过清除 sstatus 中的 SIE 位来 **禁止中断**。
保证在处理 trap 的过程中不会被其他中断打断。
**Copy the pc to sepc.**  
将当前程序计数器（PC）值保存到 **sepc 寄存器**。
这是陷入前的用户/内核指令地址，用于 trap 处理完毕后返回。
**Save the current mode (user or supervisor) in the SPP bit in sstatus.**  
将当前 CPU 运行模式（U-mode 或 S-mode）保存到 **sstatus 的 SPP 位**。
处理完 trap 后需要知道要返回哪个模式。
**Set scause to reflect the trap’s cause.**  
将 **scause 寄存器**设置为本次 trap 的原因。
例如是系统调用（ecall）、页错误、定时器中断或外设中断。
**Set the mode to supervisor.**  
将 CPU 当前模式设置为 **S-mode（Supervisor 内核模式）**。
因为 trap 处理程序在内核态运行。
**Copy stvec to the pc.**  
将 **stvec 寄存器**的值加载到 PC，开始执行 trap 入口程序。
stvec 通常指向内核 trap 处理函数，如 xv6 的 `usertrap()`。
**Start executing at the new pc**  
从新的 PC 开始执行，进入内核的 trap 处理逻辑。

这套流程是CPU从用户态进入内核态的标准步骤，换句话说，这就是中断发生时CPU上下文切换的机制
![Pasted image 20251026173902.png|400](/img/user/accessory/Pasted%20image%2020251026173902.png)
实际上ecall指令就是跳转到现在的这个stvec中的值，0x3ffffff000
![Pasted image 20251026174228.png|400](/img/user/accessory/Pasted%20image%2020251026174228.png)
0x3ffffff000可读可执行，用户访问不了
在0x3ffffff000打断点，跳过去，刚刚说的所有事情都发生了
![Pasted image 20251026174438.png|400](/img/user/accessory/Pasted%20image%2020251026174438.png)
ecall指令就是提高用户权限，调到特定的地方
现在调到了这个地方
![Pasted image 20251026174643.png|500](/img/user/accessory/Pasted%20image%2020251026174643.png)
这一页的代码在kernel文件夹下的trampoline.S，能够与gdb对应起来
![Pasted image 20251026174833.png|500](/img/user/accessory/Pasted%20image%2020251026174833.png)
trampoline.S实际上就是一个跳板，对ecall做快照,保存在了trapframe
![Pasted image 20251026175418.png|500](/img/user/accessory/Pasted%20image%2020251026175418.png)
![Pasted image 20251026175524.png|400](/img/user/accessory/Pasted%20image%2020251026175524.png)
这一页就是在3ffffe000,实际上就是说只有上面那一个是应用程序可以访问的，有个u，下面这俩都是内核才能访问的
最下面这个存代码，中间这个存数据！
![Pasted image 20251026175726.png|400](/img/user/accessory/Pasted%20image%2020251026175726.png)
其实这个trapframe就是在trampoline下面一页
![Pasted image 20251026180039.png|500](/img/user/accessory/Pasted%20image%2020251026180039.png)
这个a0还是8还在用户进程的界面上
操作系统很聪明的自己留了一个寄存器，叫sscratch，他的值实际上就是trapframe的值，然后做了一个交换
![Pasted image 20251026183011.png|350](/img/user/accessory/Pasted%20image%2020251026183011.png)
一堆sove 存储当时各种寄存器的值
然后把a0和sscratch又交换回去了
后面切换到内核地址空间，修改$satp
![Pasted image 20251026184041.png|500](/img/user/accessory/Pasted%20image%2020251026184041.png)

![Pasted image 20251026184226.png|350](/img/user/accessory/Pasted%20image%2020251026184226.png)
执行完 地址空间变了，这就进入了内核态
