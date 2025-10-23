---
{"created":"2025-10-14T09:14","updated":"2025-10-23T22:19","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 18 xv6代码导读/","dgPassFrontmatter":true,"noteIcon":""}
---

### xv6 overview
xv6: UNIX v6的现代"克隆"
可以从github上下载 注意不是xv6-public，我们需要的是xv6-riscv 额我发现我转过xv6了，应该装xv6-riscv 这才是新版的那个xv6 (2020年以后)
```shell
git clone https://github.com/mit-pdos/xv6-riscv
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
可以在xv6-riscv目录下，`make qemu-gdb`
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
首先我配置了Make的执行地址
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
### 调试第一个系统调用

