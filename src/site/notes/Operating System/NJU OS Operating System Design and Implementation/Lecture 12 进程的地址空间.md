---
{"created":"2025-09-18T09:18","updated":"2025-09-18T16:04","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 12 进程的地址空间/","dgPassFrontmatter":true,"noteIcon":""}
---

**today's agenda**
进程的地址空间和管理(mmap)
### 进程的地址空间
之前讲过C语言的状态机，C语言状态机中会有堆，栈，PC... ; 我们还讲过我们的C语言的状态机会经过编译器 编译成一个和它的含义或者行为完全等价的一个汇编语言的状态机，对与汇编语言来说，看到的不是堆，栈... 而是一个完整的内存，也就是一个地址空间，除了地址空间以外，还有寄存器；而堆，栈会包含在那个平坦的地址空间中
我们都知道C语言中，指针的本质就是地址，指针是可以直接操控地址的，比如我们可以获取main函数的入口地址的内容
```c
#include<stdio.h>
int main(){
    unsigned *p;
    p=(void*)main;
    printf("%x\n",*p);
}
```
![Pasted image 20250918094202.png|500](/img/user/accessory/Pasted%20image%2020250918094202.png)
但是如果我们随便给指针赋一个地址就会发生段错误，或者说如果给main这个地址赋值，也会发生段错误
那在我们的系统中，到底哪些地址是可以修改的，哪些地址是不可以修改的？操作系统给我们提供了一个命令来查看 -- pmap
我们以我们之前写过的最小的minimal.s为例来看一下
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/minimal.S

gcc -c minimal.S && ld minimal.o -o minimal.out
```
这是我们之前写过的可以算是最小的程序了
![Pasted image 20250918094841.png|500](/img/user/accessory/Pasted%20image%2020250918094841.png)
如果strace一下的话会发现实际上这个minimal只调用了三个系统调用，一个是我们上节课讲过的execve，一个write，一个exit
我们可以用gdb调试他，并拿到他的pid
![Pasted image 20250918095044.png|500](/img/user/accessory/Pasted%20image%2020250918095044.png)
然后可以用pmap来查看一个进程的所有地址空间
![Pasted image 20250918095157.png|500](/img/user/accessory/Pasted%20image%2020250918095157.png)
我们想我们可不可以写这么一个pmap的命令
当然我们可以上网搜，但我们也可以通过`strace pmap 1239 &| vi -` 来看一下
![Pasted image 20250918100638.png|500](/img/user/accessory/Pasted%20image%2020250918100638.png)
其中能看到他打开了一个叫`/proc/1239/maps`的文件
![Pasted image 20250918100825.png|500](/img/user/accessory/Pasted%20image%2020250918100825.png)
而且这个map的信息更详细
总之，我们能看到所有的这一段内存，如果上面写的是w，那就是可以写
读手册`man 5 proc`

我们可以写一个正常的main.c
```c
int main(){
}
```
我们可以静态编译它
```bash
gcc -static main.c -o main.out
```

![Pasted image 20250918102844.png|500](/img/user/accessory/Pasted%20image%2020250918102844.png)

ta的地址空间
![Pasted image 20250918104119.png|500](/img/user/accessory/Pasted%20image%2020250918104119.png)

第一行是一个只读的不可执行的段 -- EIF的文件头
第二行是个可执行的代码
第三行使个只读的数据 -- 比如字符串常量
下面还有堆区等等
我们可以把结果和`readelf -l main.out | less` ELF对应起来看，ELF中有把哪部分加载到了哪里
![Pasted image 20250918104550.png|500](/img/user/accessory/Pasted%20image%2020250918104550.png)

同样也可以看动态链接的文件，里面会稍微麻烦一点，之前静态编译的时候，都会会在一个确定的位置，004左右，而动态编译每运行一次会映射到不同的地址，这是因为操作系统有一个地址空间随机化，但调试器会把这个性能关掉，所以每次看到的都是55555
![Pasted image 20250918110738.png](/img/user/accessory/Pasted%20image%2020250918110738.png)
前面还有main.out 但是后面多了一些libc的映射
而且会看到有一些空的 这是什么？ 这段内存可读可写，不能执行，没有文件
首先这段内存和libc是靠在一起的，可能是留给没有初始化过的数据？
可以加一个数组验证一下
```cpp
char big[1<<30];
int main(){
}
```

![Pasted image 20250918111917.png](/img/user/accessory/Pasted%20image%2020250918111917.png)
我们发现我们未初始化的数据放在了main.out的后面，那么也就是说libc后面的这些空可能是这个库未初始化的一些数据

### vdso: 非陷入系统调用
我们之前学的是程序这个状态机，每执行一条指令就改变一个状态，然后再需要的时候调用syscall，将管理权交给操作系统内核
但是，有没有可能，或者说，能不能实现 不进入操作系统内核然后实现syscall呢？
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/vdso.c

gcc -O1 -g vdso.o -o vdso.out
```
gdb调试 si调试
![Pasted image 20250918143228.png|500](/img/user/accessory/Pasted%20image%2020250918143228.png)
执行到time的时候会跳转到0x7ffff7fc1a40这个地址
![Pasted image 20250918144213.png|500](/img/user/accessory/Pasted%20image%2020250918144213.png)
而这个地址正是处于vdso的地方
更奇怪的是在下一行他把rip-0x49cb实际上就是减到0x7ffff7fbd080去了，也就是vvar中了 把0x7ffff7fbd080这个地址的内存给读出来
![Pasted image 20250918144553.png|500](/img/user/accessory/Pasted%20image%2020250918144553.png)
而且有意思的是，vvar这段内存是不可读的，但在执行完这条指令之后，rax的值就从一个乱七八糟的值变成了时间
实际上，操作系统会给所有的进程映射一个页面，叫vvar，操作系统会每隔1s把vvar+1，这个vvar页面是操作系统中所有的页面都是共享的，所以这就可以实现一些不进入操作系统内核的系统调用

### 进程的地址空间管理
用gdb调试之前的动态链接的main.out
```bash
gdb main.out
b main
starti
```
如果打断点并用starti的话，实际上我们pmap中是没有libc的
![Pasted image 20250918150756.png|500](/img/user/accessory/Pasted%20image%2020250918150756.png)
而且现在的指令实在ld.so的，实际上是由我们的加载器帮我们加载libc的
但如果在gdb中continue，在执行pmap，这个时候libc就进来了
![Pasted image 20250918150901.png|500](/img/user/accessory/Pasted%20image%2020250918150901.png)
这说明我们的操作系统一定是提供了一定的系统调用，改变了地址空间，而这个系统调用就是mmp
![Pasted image 20250918151104.png|400](/img/user/accessory/Pasted%20image%2020250918151104.png)
mmap能把文件映射到地址空间中 -- 进程在内存上就是个字节序列，文件或者磁盘实际上也是个字节序列
甚至可以通过offset映射文件的一部分，而且并不是直接搬进去，而是访问哪里就搬哪里，访问多少就搬多少
![Pasted image 20250918151512.png|400](/img/user/accessory/Pasted%20image%2020250918151512.png)

EIF会告诉把哪个文件加载到哪里， 加载器用个mmap就加载进去了
这里有一个小栗子
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/mmap-alloc.c
```

![Pasted image 20250918152629.png|500](/img/user/accessory/Pasted%20image%2020250918152629.png)
这段代码实际上是要用mmap分配3GB的内存
执行的话是瞬间完成的
![Pasted image 20250918152707.png|400](/img/user/accessory/Pasted%20image%2020250918152707.png)
![Pasted image 20250918152813.png|500](/img/user/accessory/Pasted%20image%2020250918152813.png)
3个GB的内存一瞬间完成，实际上是因为mmap只是标记了这部分内存已经被分配了，等到缺页的时候，才会把这段内存真正分配，后面会再讲

### 地址空间的隔离
虚拟内存，mmap这些给我们创造了一个进程地址空间隔离的机制
一个进程的mmap是不能访问其他进程的地址空间的
但是如果想侵占另一个进程的地址空间，真的没有办法吗？
实际上老师demo了一个游戏修改器，主要的原理就是先去获取到进程的PID，然后去proc中找这个进程的地址空间的文件，然后扫描整个地址空间，找到要修改的地方，然后修改
进程要支持调试器的执行，而普通的程序就是要给状态机，所以我们的调试器就可以attach到进程上，读内存改内存 -- 这是操作系统留的，有这样的需求

