---
{"created":"2025-09-16T08:59","updated":"2025-09-16T19:51","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 11 操作系统上的进程/","dgPassFrontmatter":true,"noteIcon":""}
---

本次课回答的问题
- Q1: 操作系统启动后到底做了什么？
- Q2: 操作系统如何管理程序 (进程)？


本次课主要内容
- 虚拟化：操作系统上的进程
- 进程管理 API

### 操作系统启动后到底做了什么？
操作系统在启动后实际上只加载了一个程序
如果运行`pstree`，能看到我们的系统里面有很多个进程
![Pasted image 20250916091023.png|400](/img/user/accessory/Pasted%20image%2020250916091023.png)
这些进程都构成一些树状的结构，实际上我们的操作系统就只创建这个树根的进程，然后就把控制器交给树根的进程了，后面的这些进程都是树根那个进程创建的
我们能在Linux内核的代码中，真的看到这一部分
![Pasted image 20250916091453.png|400](/img/user/accessory/Pasted%20image%2020250916091453.png)
![Pasted image 20250916091539.png|500](/img/user/accessory/Pasted%20image%2020250916091539.png)
我们真的能看到在`/sbin/init`下面有一个`systemd`，跟我们在`pstree`中看到的是一样的
所以操作系统做的事儿就是启动了一个进程，然后程序实际上就是状态机，就开始运行我们的状态机模型，状态机就可以调用系统调用，利用操作系统所提供的一些特性；进程也能创建进程，所以实际上就是一个进程一个进程这样一级一级的构建出来的
那我们很自然的就能想到，我们能创建一个最小的Linux内核的镜像，因为我们知道只要有一个程序，比如`/sbin/init`，整个系统就能启动

老师给我们准备了一个解压包
```bash
wget 'https://box.nju.edu.cn/f/3f67e092e1ba441187d9/?dl=1'

mv 'index.html?dl=1' l.zip

unzip l.zip
```
会得到一个linux-minimal的文件
```bash
qingzhiliangcheng@localhost linux-minimal > tree .
.
├── Makefile
├── initramfs
│   ├── bin
│   │   └── busybox
│   └── init
└── vmlinuz
```
然后可以make 
```bash
qingzhiliangcheng@localhost linux-minimal > make 
.                                                                      
./bin
./bin/busybox
./init
5873 blocks
qingzhiliangcheng@localhost linux-minimal > tree .
.
├── Makefile
├── build
│   └── initramfs.cpio.gz
├── initramfs
│   ├── bin
│   │   └── busybox
│   └── init
└── vmlinuz
```
make之后会在build中生成一个initramfs.cpio.gz这样的一个镜像
![Pasted image 20250916100048.png|500](/img/user/accessory/Pasted%20image%2020250916100048.png)
Makefile文件里面用cpio打了一个包，这是Linux默认的
make run用qemu起了一个最小的模拟器，`-nographic`: 不创建图形界面窗口，`-m 128`: 分配给虚拟机 128MB 内存，`-kernel vmlinuz`: 指定要加载的 Linux 内核文件 `-initrd build/initramfs.cpio.gz` 将打包好的那个镜像放进去
如果make run的话就会启动一个模拟器，这是一个真正的Linux
![Pasted image 20250916100439.png|400](/img/user/accessory/Pasted%20image%2020250916100439.png)
我们得到了一个shell
这个shell是怎么启动的呢？是在initramfs目录下有一个busybox
![Pasted image 20250916100711.png|500](/img/user/accessory/Pasted%20image%2020250916100711.png)
busybox 是一个 工具集，包含了常用的这些命令行工具
但是在启动的Linux中shell中什么也干不了
但是我们可以`/bin/busybox` 然后加指令
在init中实际上是执行了一个`/bin/busybox sh`
![Pasted image 20250916101218.png|500](/img/user/accessory/Pasted%20image%2020250916101218.png)
所以能够看到一个命令行的工具
如果我们把命令改成`echo hello`
![Pasted image 20250916101454.png|500](/img/user/accessory/Pasted%20image%2020250916101454.png)
实际上我们在运行的时候就会打出一个hello
![Pasted image 20250916101610.png|500](/img/user/accessory/Pasted%20image%2020250916101610.png)
这个时候真的会打印一个hello，但同样的会出现一个内核错误，意思是初始的进程返回了

如果我们在make run之后的shell加一段代码
```bash
c1="arch ash base64 cat chattr chgrp chmod chown conspy cp cpio cttyhack date dd df dmesg dnsdomainname dumpkmap echo ed egrep false fatattr fdflush fgrep fsync getopt grep gunzip gzip hostname hush ionice iostat ipcalc kbd_mode kill link linux32 linux64 ln login ls lsattr lzop makemime mkdir mknod mktemp more mount mountpoint mpstat mt mv netstat nice nuke pidof ping ping6 pipe_progress printenv ps pwd reformime resume rev rm rmdir rpm run-parts scriptreplay sed setarch setpriv setserial sh sleep stat stty su sync tar touch true umount uname usleep vi watch zcat" c2="[ [[ awk basename bc beep blkdiscard bunzip2 bzcat bzip2 cal chpst chrt chvt cksum clear cmp comm crontab cryptpw cut dc deallocvt diff dirname dos2unix dpkg dpkg-deb du dumpleases eject env envdir envuidgid expand expr factor fallocate fgconsole find flock fold free ftpget ftpput fuser groups hd head hexdump hexedit hostid id install ipcrm ipcs killall last less logger logname lpq lpr lsof lspci lsscsi lsusb lzcat lzma man md5sum mesg microcom mkfifo mkpasswd nc nl nmeter nohup nproc nsenter nslookup od openvt passwd paste patch pgrep pkill pmap printf pscan" c3="pstree pwdx readlink realpath renice reset resize rpm2cpio runsv runsvdir rx script seq setfattr setkeycodes setsid setuidgid sha1sum sha256sum sha3sum sha512sum showkey shred shuf smemcap softlimit sort split ssl_client strings sum sv svc svok tac tail taskset tcpsvd tee telnet test tftp time timeout top tr traceroute traceroute6 truncate ts tty ttysize udhcpc6 udpsvd unexpand uniq unix2dos unlink unlzma unshare unxz unzip uptime users uudecode uuencode vlock volname w wall wc wget which who whoami whois xargs xxd xz xzcat yes" for cmd in $c1 $c2 $c3; do /bin/busybox ln -s /bin/busybox /bin/$cmd done mkdir -p /proc && mount -t proc none /proc mkdir -p /sys && mount -t sysfs none /sys export PS1='(linux) '
```
然后提示符就变了😱
![Pasted image 20250916102511.png|500](/img/user/accessory/Pasted%20image%2020250916102511.png)
然后就能开始用Linux的命令了😱
比如top
![Pasted image 20250916102540.png|500](/img/user/accessory/Pasted%20image%2020250916102540.png)
但是你会发现实际上还是/bin/busybox sh在工作
甚至可以用vi创建文件
why
这段代码实际上最重要的是吧proc和sysfc这两个重要的文件系统(实际上是我的系统中的这俩文件系统吧)给挂在上去了，然后就拥有了全世界
![Pasted image 20250916103339.png|400](/img/user/accessory/Pasted%20image%2020250916103339.png)
这个Linux告诉我们的是，OS执行的时候是先执行了一个init进程，然后是通过一些syscall才能构造出整个世界
我们甚至可以将我们之前的minimal.S, logisim.c这些代码编译好的可执行文件放进我们的这个linux然后真的可以执行

所以Linux的操作系统启动流程: - CPU Reset → Firmware → Loader → Kernel `_start()` → 第一个程序 `/bin/init` → 程序 (状态机) 执行 + 系统调用
所以本质上后面就是讲系统调用
- 进程(状态机)管理
	- fork, execve, exit - 状态机/进程的创建/改变/删除
- 存储(地址空间)管理
	- mmap - 虚拟地址空间管理
- 文件(数据对象)管理
	- open, close, read, write - 文件访问管理
	- mkdir, link, unlink - 目录管理
- 网络管理...


今天主要讲创建进程

### fork()
Linux实现了一个非常有趣的实现创建进程的系统叫做fork
fork是叉子，意思是在创建进程的时候一个执行流会分叉
![Pasted image 20250916164735.png|300](/img/user/accessory/Pasted%20image%2020250916164735.png)

程序是状态机，前面学了，操作系统启动起来之后上面就运行了一个init进程，这个进程执行指令实际上就是状态机的执行，当执行fork系统调用，就是进入OS代码，执行完将状态机的状态完整的复制一份一模一样的
所谓的一摸一样是指两个init的内存字节是完全一样的，寄存器也完全一样，除了fork的返回值 --- 新创建进程返回 0， 执行 fork 的进程返回子进程的进程号
![Pasted image 20250916170022.png|300](/img/user/accessory/Pasted%20image%2020250916170022.png)

在执行完fork之后，我们操作系统的执行模型就变了，变成了并发程序的执行模型，也就是说操作系统想要执行一步的话，可以选进程1执行，也可以选进程20执行
什么是操作系统？ 操作系统是状态机的管理者
什么是虚拟化？ 容纳了很多状态机，但每次就只能选一个执行
### execve()
如果我们想创建别的进程，而不是创建一个完全一样的进程，就需要execve
execve的含义将当前运行的状态机重置成成另一个程序的初始状态
可以传入想要重置设置的参数和环境变量
`int execve(const char *filename, char * const argv, char * const envp);`
- 执行名为 `filename` 的程序
- 允许对新状态机设置参数 `argv (v)` 和环境变量 `envp (e)`

而且我们发现argv和anvp刚好是main函数的参数
事实上这里，执行execve的意思是说把这个状态机重置成了filename这个程序的初始状态，并且给这个程序的main函数传入了argv和envp

这里有一个小demo
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/execve-demo.c
```

![Pasted image 20250916180121.png|300](/img/user/accessory/Pasted%20image%2020250916180121.png)

在这里是要执行名为`/bin/bash`这个程序，后面是传的参数，对于bash这个程序来说`-c`是指执行命令，可以执行一下`bash -c ls`和`bash -c env`试一下，env是说查案环境变量
![Pasted image 20250916180613.png|500](/img/user/accessory/Pasted%20image%2020250916180613.png)
这是我现在系统的状态
而执行execve可以给他设定执行bash的参数和环境变量
也就是说这个是说启动bash这个程序，然后执行bash -c env，然后设置了环境变量，所以我们运行结果中只能看到这个进程中很干净的一个环境变量
![Pasted image 20250916180829.png|400](/img/user/accessory/Pasted%20image%2020250916180829.png)
而且关键是printf没有执行

常见的环境变量
- 使用 `env` 命令查看
    - `PATH`: 可执行文件搜索路径
    - `PWD`: 当前路径
    - `HOME`: home 目录
    - `DISPLAY`: 图形输出
    - `PS1`: shell 的提示符

如果strace一下前面的execve-demo.out的话
事实上所有程序的strace的第一行都是execve，这是因为在老的linux世界中，所有的进程的创建都是通过fork，新程序的话就是execve
后面还有一个execve就是我们代码里写的execve
![Pasted image 20250916193118.png|500](/img/user/accessory/Pasted%20image%2020250916193118.png)
这跟我们程序里面写的是完全一致的
fork+execve -> 整个世界
### exit()
销毁
世界里面的所有东西都保持不变，除了执行exit的状态机直接从世界里面消失
`void _exit(int status)`
- 销毁当前状态机，并允许有一个返回值
- 子进程终止会通知父进程 (后续课程解释)

多线程程序怎么办？ exit是删除一个线程？还是删除所有线程？ 
感觉哪一种都不太对，因为有时候我们会想删除一个，有时候想删除所有
所以实际上系统提供了好几个exit
- `exit(0)` - `stdlib.h` 中声明的 libc 函数
	- libc的clean up, 比如缓冲区, 会打印出来吧？
	- 会调用atexit
- `_exit(0)` - glibc 的 syscall wrapper
	- 进程连带着里面的所有线程都结束调
- `syscall(SYS_exit, 0)`
	- 只会关注一个线程