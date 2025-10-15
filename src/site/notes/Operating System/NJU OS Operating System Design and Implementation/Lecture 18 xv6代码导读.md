---
{"created":"2025-10-14T09:14","updated":"2025-10-14T09:15","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 18 xv6代码导读/","dgPassFrontmatter":true,"noteIcon":""}
---

### xv6 overview
xv6: UNIX v6的现代"克隆"
可以从github上下载
```shell
git clone https://github.com/mit-pdos/xv6-public.git
```

可以运行
```shell
make qemu
```

![Pasted image 20251014093502.png|500](/img/user/accessory/Pasted%20image%2020251014093502.png)
就得到了一个真的xv6的虚拟机
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
![Pasted image 20251014100233.png|500](/img/user/accessory/Pasted%20image%2020251014100233.png)
偏移量在左边写了注释
![Pasted image 20251014100433.png|500](/img/user/accessory/Pasted%20image%2020251014100433.png)

xv6有一份手册: [xv6: a simple, Unix-like teaching operating system](https://jyywiki.cn/pages/OS/manuals/xv6-riscv-rev2.pdf)

### xv6中的进程
