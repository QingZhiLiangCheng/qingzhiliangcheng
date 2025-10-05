---
{"created":"2025-10-05T10:29","updated":"2025-10-05T18:37","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 16 可执行文件/","dgPassFrontmatter":true,"noteIcon":""}
---

**today's agenda**
回答的主要问题：
- 可执行文件到底是什么？
- 可执行文件是如何在操作系统上执行的

内容
- 可执行文件
- 解析可执行文件
- 链接和加载（this lecture只有静态链接）

### 可执行文件
手册：System V Application Binary Interface，实际上手册已经给了所有的信息了hh 一定要去试着去看手册！
手册是由大量的dependency的，所以我们一上来就读手册是不合适的，所以老师的任务或者说我们应该选取学的东西是 -- 在一个简化的系统上把所有的基本概念都搞清楚，然后回去看手册就会豁然开朗，然后在手册中跟重要的基本概念不相关的就可以先跳过去，相关的就可以留下来，这样就能知道手册哪一部分是重要的，哪一部分是不那么重要的

**什么是可执行文件？**
对于一个什么也没写的a.c文件
```cpp
int main(){

}
```
如果用`gcc -static a.c -o a.out`编译的话，然后用`file a.out`命令看到的是
![Pasted image 20251005153500.png|500](/img/user/accessory/Pasted%20image%2020251005153500.png)
给出了很多信息，包括这是一个ELF文件,Executable linkable format的文件，（这也是我们最早接触CSAPP时对于可执行文件的认识），64位，x86-64架构, 静态链接...
但是更重要的，可执行文件并不是我们打开a.out看到的东西，而是一个对于状态机的描述，是一个迁移的数据结构，是操作系统中最重要的一个对象
我们什么时候用可执行文件？ -- execve系统调用中用到可执行文件，而execve是重置整个状态机的状态的，换句话说就是将原来fork出来的跟父进程一模一样的进程的状态，初始化为a.out这个可执行文件的初始状态
![Pasted image 20251005153809.png|500](/img/user/accessory/Pasted%20image%2020251005153809.png)
那接下来一个很重要的推论就是可执行文件实际上就是描述了状态机的初始状态+迁移的数据结构！
那如果我们要自己实现一个ELF文件的话，我们的文件中应该有什么？
那状态机重置之后，状态有什么啊？无非就是寄存器和内存(地址空间)！
![Pasted image 20251005154536.png|500](/img/user/accessory/Pasted%20image%2020251005154536.png)

![Pasted image 20251005154523.png|500](/img/user/accessory/Pasted%20image%2020251005154523.png)

包括我们看到的40100的rx的地址空间，这部分内存位于哪里？值是多少？这些都是需要初始化的内容
除此之外，还有一些可能有用的信息，都可以加在可执行文件中，比如便于调试的信息

**到底什么样的程序，在操作系统上才是可以执行的？**
如果我们去运行a.c 实际上会得到一个报错
```bash
$ ./a.c
bash: ./a.c: Permission denied
```
系统会告诉我们没有权限
所以我们试着去给a.out去掉可执行权限
```bash
$ chmod -x a.out && ./a.out
fish: The file “./a.out” is not executable by this user
```
系统告诉我们没法执行，这是因为我们自己设置了不可执行
然后我们试着去给a.c加一个权限
```bash
$ chmod +x a.c && ./a.c
Failed to execute process './a.c'. Reason:
exec: Exec format error
The file './a.c' is marked as an executable but could not be run
```
然而还是不能运行，虽然有权限，但是他发现执行格式错误

如果用strace来看的话，就是
![Pasted image 20251005162042.png|400](/img/user/accessory/Pasted%20image%2020251005162042.png)
这是跟报错对应起来的
在我们有了这个经验和认识之后，再去看execve的手册`man 2 execve`
然后去看ERRORS中的内容，规定了什么时候不能执行
![Pasted image 20251005162400.png|400](/img/user/accessory/Pasted%20image%2020251005162400.png)

**常见的可执行文件**
![Pasted image 20251005162909.png|500](/img/user/accessory/Pasted%20image%2020251005162909.png)
但是存在一些额外的方法可以把a.c变成可执行文件
```python
#!/usr/bin/python3

print("Hello")
```
比如可以在最上面加一个shell的注释，然后写一个python的脚本
然后就可以执行了
![Pasted image 20251005164841.png|300](/img/user/accessory/Pasted%20image%2020251005164841.png)
对于Linux系统来说，不管名字是什么，后缀是什么，只要把文件设置成可以执行，就真的可以执行
那有一个有意思的事情，就是通过一个文件来执行另一个文件，比如说我有一个demo.c，注释写的是执行a.out
demo.c:
```c
#! ./a.out
```

a.out:
```c
int main(int argc, char *argv[]){
   for(int i=0;i<argc;i++){
      printf("argv[%d] = %s\n",i,argv[i]);
   }
}
```

![Pasted image 20251005170156.png|500](/img/user/accessory/Pasted%20image%2020251005170156.png)
这里有一个好玩的地方是，如果没有头文件，对于printf来说，会报warning，自己加上，但是不会报错
那这样实际上就可以通过demo.c执行a.out
![Pasted image 20251005170355.png|400](/img/user/accessory/Pasted%20image%2020251005170355.png)
事实上是啥这样的：
![Pasted image 20251005165551.png|300](/img/user/accessory/Pasted%20image%2020251005165551.png)
实际上就是如果加载器发现了#!,就会在execve的时候，偷换一下它

### 解析可执行文件
首先是有一些工具来帮助我们认识可执行文件
GNU binutils
![Pasted image 20251005171346.png|400](/img/user/accessory/Pasted%20image%2020251005171346.png)
这里面有很多我们之前用过的，比如ld连接，as编译，比如objdump，比如readelf...
比如我们可以通过`readelf -h a.out`来看这个可执行文件这个数据结构的一些信息
![Pasted image 20251005171703.png|400](/img/user/accessory/Pasted%20image%2020251005171703.png)
这个在CSAPP中学过，包括各种header存的是什么信息，是干嘛的等等[[CSAPP Computer-System-A-Program-Perspective/Lecture 13 Linking\|Lecture 13 Linking]]
这些命令集实际上有很多都是来解析这个数据结构的一部分，比如说objdump，是将代码的那部分进行反汇编，比如说nm，是把符号解析出来
![Pasted image 20251005172134.png|300](/img/user/accessory/Pasted%20image%2020251005172134.png)
里面还存在一些info，是帮助debugger解析运行时状态的
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/segfault.c
```
这是老师所提供的一段代码，这段代码是报Segmentation fault的
如果用gdb调试的话
![Pasted image 20251005180531.png|500](/img/user/accessory/Pasted%20image%2020251005180531.png)
是能看到很多信息的，比如说backtrace
为什么能看到啊backtrace？ 根源就在可执行文件所存的额外的一些info
如果我们用-S来编译segfault.c的话
```bash
gcc -static -g segfault.c -S segfault.s
```

![Pasted image 20251005180902.png|500](/img/user/accessory/Pasted%20image%2020251005180902.png)
我们会发现在segfault.s的文件中，除了汇编多了很多以.开头的东西,这是编译器顺便加的一些调试信息，甚至还有专门的debug_info
生成的这些调试信息事实上也遵循一定的标准：[DWARF Debugging Information Format](https://dwarfstd.org/)，这是非常非常通用的格式，他其实定义了一个Turing Complete的指令集，有点类似于在一段内存中起了一个虚拟机，然后根据PC的值，能够恢复出一些信息来
![Pasted image 20251005181305.png|250](/img/user/accessory/Pasted%20image%2020251005181305.png)
![Pasted image 20251005181601.png|300](/img/user/accessory/Pasted%20image%2020251005181601.png)
之前我们讲过什么是编译器，调试信息就像一些函数，帮我们的指令中的内容映射回C语言的状态机
但非常不完美，可能会出错 映射不回去等等的问题

我们可以自己实现一个backtrace
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/unwind.c

gcc -O0 -fno-omit-frame-pointer -static -g unwind.c -o unwind.out
```
![Pasted image 20251005183028.png|500](/img/user/accessory/Pasted%20image%2020251005183028.png)
能够打印出正确的结果，比如在34行调用了30行（30行使返回结果）调用了26行（返回结果）
![Pasted image 20251005183229.png|500](/img/user/accessory/Pasted%20image%2020251005183229.png)
实际上这个代码是怎么实现的呢？跟我们的函数调用的栈帧有关，这部分在CSAPP中系统学过[[CSAPP Computer-System-A-Program-Perspective/Lecture 07 Machine-Level Programming III：Procedures\|Lecture 07 Machine-Level Programming III：Procedures]]
整体上来说，就是说比如在foo函数的时候，会在栈中先压入现在rbp(栈基指针)的值，然后将rsp(栈顶指针)的值给rbp作为新的栈基，当然还要存返回地址
![Pasted image 20251005183727.png|500](/img/user/accessory/Pasted%20image%2020251005183727.png)


![Pasted image 20251005182719.png|300](/img/user/accessory/Pasted%20image%2020251005182719.png)
实际上就是通过frame存着压进去的当时的retaddress和oldrdp；然后通过链表进行遍历除了backtrace

### 编译和链接
C代码到底怎么翻译成二进制文件的？
```c
// main.c
void hello();

 void main(){
   hello();
 }
```

```c
//hello.c
#include<stdio.h>

 void hello(){
   printf("Hello World\n");
 }
```

我们知道可以通过这样的编译链接，将两个文件链接在一起，然后执行出Hello World
```bash
gcc -c main.c
gcc -c hello.c
gcc -static main.o hello.o -o hello.out
./hello.out
```
但问题是，到底是怎么连接起来的？ main.c调用了一个自己不知道的hello
![Pasted image 20251005190635.png|400](/img/user/accessory/Pasted%20image%2020251005190635.png)
如果我们用nm来解析main.o中的符号，他真的并不知道hello是个啥，不知道在哪，这部分实在后面连接的时候再完成的，但是我们的文件是可以编译照常完成的
如果我们看main.s的话`gcc -S -c main.c`
虽然不认识，但是正确的编译成了一条call指令，但是还不知道call向哪里
![Pasted image 20251005191133.png|400](/img/user/accessory/Pasted%20image%2020251005191133.png)
如果看反汇编的话
![Pasted image 20251005192218.png|400](/img/user/accessory/Pasted%20image%2020251005192218.png)
绝大部分代码都被生成了，但是这里不知道call哪一个地址，后面都填的零(就是e8后面那一块，e8是call吧我觉得)，但是他打了个注释，告诉链接器,这个地方以后该填什么
![Pasted image 20251005192548.png|400](/img/user/accessory/Pasted%20image%2020251005192548.png)
额我的和老师的汇编还不太一样，但是总体上来说，我们虽然摆烂了，但是加了一个规约，实际上我们是希望满足老师所写的那部分代码的条件和规约的
这段代码是老师写在hello.c的hello函数中的，这段代码说的是，我们希望在链接完后，main的开始地址+a实际上是到了e8 00 ... 那条指令，再+1是跳过了第一个字节，也就是说，这里拿到的是**针对PC应该跳转的地址的偏移量**，我们希望执行完这条指令后（PC就跳到f那里了），加上偏移量后，应该会跳转到hello的起始位置
那什么是main.o, 他是一个数据结构，他包含所有的代码，但除此之外他还要包含一些约束，就比如说我们写的这种针对偏移量的约束
可以通过`readelf -a main.o`来看到这些约束
![Pasted image 20251005193723.png](/img/user/accessory/Pasted%20image%2020251005193723.png)
比如图中圈出来的部分，0b实际上就是e8后面的00，重填成S+A-P,S就是hello, A就是-4，P就是main+0xb
为什么caspp等书上说重填成S+A-P呢，实际上就是为了满足assertion
```cpp
assert( 
(char *)hello == (char *)main + 0xf + // call hello 的 next PC 
*(int32_t *)((uintptr_t)main + 0xb) // call 指令中的 offset 
);
```

所以，整个流程就是
gcc(编译器)帮助我们将C语言的状态机翻译成了指令的状态机，同时还存在返回去的f，而汇编器是帮我们将指令翻译成了二进制的数据结构+约束条件，这就变成了一个个.o文件
![Pasted image 20251005194817.png|300](/img/user/accessory/Pasted%20image%2020251005194817.png)
而链接器就是将多个.o文件汇聚到一起，然后来满足所有的约束条件
![Pasted image 20251005195218.png](/img/user/accessory/Pasted%20image%2020251005195218.png)

如果我们想着去自己实现一个executable，我们会发现什么是必不可少的，什么是为了方便加的？
其实我感觉这里数据库的metadata也适用，他就像个数据结构，像个ELF
