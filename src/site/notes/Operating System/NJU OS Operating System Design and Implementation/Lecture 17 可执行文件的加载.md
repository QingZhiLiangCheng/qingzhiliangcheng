---
{"created":"2025-10-12T14:25","updated":"2025-10-12T17:48","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 17 可执行文件的加载/","dgPassFrontmatter":true,"noteIcon":""}
---

### 静态ELF加载器: 实现
#### Loader-Static
之前我们学过程序是状态机，我们学过ELF文件实际上一个描述程序状态的状态机的数据结构，在execve的时候将文件的状态映射进去
我们可以从`vi /usr/include/elf.h`看这个数据结构的代码
![Pasted image 20251012145308.png|400](/img/user/accessory/Pasted%20image%2020251012145308.png)
在这里面我们真的能够见识到比如说elf文件32位的header和64位的header的组成的结构，这跟[[CSAPP Computer-System-A-Program-Perspective/Lecture 13 Linking\|Lecture 13 Linking]]所学到的内容是对应的，这与`readelf -h hello.out`中的内容也是一直的
![Pasted image 20251012145808.png|400](/img/user/accessory/Pasted%20image%2020251012145808.png)
比如说我这里面的Machine是x86-64机器，实际上在`/usr/include/elf.h`中下面是有一些宏的，给了struct中的e_machine, 然后翻译成了我们所看到的Advanced Micro Devices X86-64.
![Pasted image 20251012150215.png|300](/img/user/accessory/Pasted%20image%2020251012150215.png)

ELF实际上就是一个数据结构，他有header, 有PHT(program header table),有要加载的部分
当我们调用execve的时候，loader(加载器)会帮助我们按照ELF数据结构在操作系统中创建这样一个对象
那我们可不可以创建一个自己的loader？
实际上loader就是通过mmap，根据PHT中的内容，将要加载的内容搬到内存的位置，除此之前，还要创建进程运行时初始的状态，比如环境变量等等
![Pasted image 20251012143854.png|400](/img/user/accessory/Pasted%20image%2020251012143854.png)
老师实际上给我们提供了一段简单的loader的程序
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/loader-static.c
```
这段代码核心上实际上就是调用了mmap系统调用，甚至连execve系统都没用到，然后他真的可以运行程序，比如我们之前写过的minimal.S
![Pasted image 20251012151948.png|400](/img/user/accessory/Pasted%20image%2020251012151948.png)

![Pasted image 20251012153134.png|400](/img/user/accessory/Pasted%20image%2020251012153134.png)

在源码中，实际上我们是自己写了一个与execve功能相同的函数，`execve_`
我可可以借助gdb来看一下execve到底是怎么写的`gdb ./loader`, 然后就可以`b main` `r minimal.out`
![Pasted image 20251012152942.png|450](/img/user/accessory/Pasted%20image%2020251012152942.png)
在执行进`execve_`的时候，p就是minimal.out
然后后面就是打开这个文件，然后做了一件事儿是把前4090全部mmap了, 然后我们就会得到一个elf的文件头
当我们执行过这条语句，拿到并mmap header后，我们可以打印，看到header中的信息
![Pasted image 20251012153339.png|450](/img/user/accessory/Pasted%20image%2020251012153339.png)

然后执行过pht，就能看到pht中的内容
![Pasted image 20251012153453.png|450](/img/user/accessory/Pasted%20image%2020251012153453.png)
pht中标注了一些程序需要加载的地方
![Pasted image 20251012153620.png|400](/img/user/accessory/Pasted%20image%2020251012153620.png)

![Pasted image 20251012153646.png|400](/img/user/accessory/Pasted%20image%2020251012153646.png)

然后发现0x400000，说明ret的返回值是这一页真的被映射上去了，可以通过objdump检查
![Pasted image 20251012153830.png|400](/img/user/accessory/Pasted%20image%2020251012153830.png)
也确实是从0x400000这里开始的
然后加载第二个，这时候就真的就是0x401000了
![Pasted image 20251012153947.png|300](/img/user/accessory/Pasted%20image%2020251012153947.png)
等到所有的地址都映射上去的时候，就可以看pmap了
![Pasted image 20251012154344.png|400](/img/user/accessory/Pasted%20image%2020251012154344.png)
这就是loader是形成了一个内存地址空间镜像，minimal的代码和数据都到了该放的位置
事实上，在这里，我们的地址空间中有两份程序，一份是loader，一份是minimal，但实际上真正的loader是会把不属于minimal的东西都回收掉
![Pasted image 20251012154623.png|400](/img/user/accessory/Pasted%20image%2020251012154623.png)
接下来的事情，就是构建需要运行minimal这个程序 初始化的堆栈，参数（包括环境变量等)
这一部分的来源：  RTFM: [System V ABI](https://jyywiki.cn/pages/OS/manuals/sysv-abi.pdf) Figure 3.9 (Initial Process Stack)，手册的34页有一张图叫Initial Process Stack
![Pasted image 20251012154900.png|450](/img/user/accessory/Pasted%20image%2020251012154900.png)
在这张表中说，我们的%rsp寄存器,要指向argc(argument count), 然后下一格rsp+8，就是argument pointer，就是`argv[0],argv[1],...`
![Pasted image 20251012155737.png|500](/img/user/accessory/Pasted%20image%2020251012155737.png)
所以后面的这部分代码就是按照手册的要求，将该放的都压入了
![Pasted image 20251012155929.png|400](/img/user/accessory/Pasted%20image%2020251012155929.png)


事实上，在我们的静态连接中，实际上我们使用open. mmap, close系统调用实现了一个execve系统调用
这些都是运行在操作系统上的，那加载操作系统的loader呢？
#### Boot Block Loader
事实上，我们之前学过，操作系统也是一个程序，他是由一个小的512字节的bootloader(启动加载器)加载的，通过固件将我们的数据一点一点从磁盘搬到内存中
![Pasted image 20251012160902.png|300](/img/user/accessory/Pasted%20image%2020251012160902.png)
前512字节是bootblock，里面由boot loader，后面1024字节是该传的参数，后面的就是Kernel，Kernel也是一个ELF文件! 本质上一样，boot loader也是需要把这部分内容搬到我们的内存中
唯一的区别是底层调用的机制不一样，那时候还没有加载操作系统，所以是用的硬件约定好的机制实现的，换句话说，就是读取的api更底层一点
#### Linux Kernel ELF Loader
与loader-static和boot block loader本质上是一样的
![Pasted image 20251012162351.png|500](/img/user/accessory/Pasted%20image%2020251012162351.png)
呃呃linux内核代码也是遍历程序头表，只不过做的事情多一点

### 动态链接和加载
为什么需要动态链接？
- 减少库函数的磁盘和内存拷贝，但是要遵守约定，不挑库函数的版本
- 便于分解大型项目

需求时运行 链接
直接去讲ELF并不好讲，因为工业界为了性能，概念上紧密相关的东西在数据结构中被强行 “拆散” 了
但我们可以考虑，如果编译器，连接器，加载器都受我们控制，我们怎么设计，实现一个在理解上最直观最简单的动态链接格式，然后考虑怎么去改进，就得到了ELF
那实现一个动态库和可执行文件的需求是什么？ -- 我们希望实现一个“libc”, 里面有一个"putchar", 然后我们的a.out文件可以调用这个putchar，但是他不是直接调用的这个putchar，而是一个动态符号DSYM(putchar)
![Pasted image 20251012164137.png|400](/img/user/accessory/Pasted%20image%2020251012164137.png)
也就是说，像不需要依赖外部库的句子都正常写，包括正常的call自己库的函数，call自己库的函数编译器编译一个正常的相对的跳转就行了，但是遇到像用外部库的时候，需要包装一下，这里就先用DSYM这样一个宏
既然我们想调用库函数里面的代码，我们就需要能够加载一个动态库
如果我们想用别的库的函数的话，就需要加载外部符号，import putchar
如果我们想让这个函数被别人用的话，就需要导出一个符号: export hello.

![Pasted image 20251012164939.png|500](/img/user/accessory/Pasted%20image%2020251012164939.png)

最终老师给出了实现完的数据结构定义和工具链hh
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/dl/dl.h
wget https://jyywiki.cn/pages/OS/2022/demos/dl/dlbox.c

# 示例代码
wget https://jyywiki.cn/pages/OS/2022/demos/dl/libc.S
wget https://jyywiki.cn/pages/OS/2022/demos/dl/libhello.S
wget https://jyywiki.cn/pages/OS/2022/demos/dl/main.S
```

![Pasted image 20251012165243.png|400](/img/user/accessory/Pasted%20image%2020251012165243.png)
libc.S 提供了putchar和exit库函数
![Pasted image 20251012165404.png|400](/img/user/accessory/Pasted%20image%2020251012165404.png)
libhello.S 调用了putchar，提供hello
![Pasted image 20251012165444.png|300](/img/user/accessory/Pasted%20image%2020251012165444.png)
main.S调用了hello，提供了main
dlbox.c 提供了gcc, readdl, objdump, interp
然后我们就可以调用dlbox中的gcc来调用libc.S，libhello.S, main.S
```shell
 > gcc dlbox.c -g -o dlbox.out
 > ./dlbox.out gcc libc.S
```

readdl能够正确解析dl
![Pasted image 20251012165953.png|450](/img/user/accessory/Pasted%20image%2020251012165953.png)

![Pasted image 20251012170049.png|450](/img/user/accessory/Pasted%20image%2020251012170049.png)

能够正确执行
![Pasted image 20251012170359.png|400](/img/user/accessory/Pasted%20image%2020251012170359.png)

dl.h
![Pasted image 20251012170620.png|450](/img/user/accessory/Pasted%20image%2020251012170620.png)

![Pasted image 20251012170755.png|400](/img/user/accessory/Pasted%20image%2020251012170755.png)

假如我们有一个`call DSYM(putchar)`
IMPORT是说，symbol中offset编译的时候暂时还不知道，所以先填0
DSYM是一个简介的跳转`*sym(%rip)`，如果call putchar的话，实际上展开就是`call *putchar(%rip)`, 相对于PC寻址，也就是说向另外一个动态链接库的跳转回变成一个间接跳转，`*putchar(%rip)`指向的位置就是符号表中前面的那个offset的位置
如果是IMPORT, 除了前面offset填0，后面的tyle会填个问号，然后后面填上putchar
如果是导出符号EXPORT，会把符号的offset填上(这是符号相对头部的偏移量)，然后填#，代表是个符号
如果是Load的话，填0，+ ...

main.S就编译成了
![Pasted image 20251012171823.png|500](/img/user/accessory/Pasted%20image%2020251012171823.png)


比如说14行是IMPORT(hello)说，上来是32字节对齐，先填了8字节0，然后填了一个问号，然后填上hello的名字
当链接器一旦在别的文件中看到了hello的地址，就会把这个地方的8字节0重写
`call *hello(%rip)`是说，我会把上面符号表中的hello的地址的值给读出来，然后跳转到这个地址，就正确跳转到hello了
这张图就是对上面的结果的真正的文件
![Pasted image 20251012172542.png|500](/img/user/accessory/Pasted%20image%2020251012172542.png)

### 重新回到ELF
#### 代码问题
我们的dl其中一个最大的缺陷，DSYM是间接内存访问
我们的dl不知道某个函数是静态链接的
call hello,但是编译器不知道实在外部的libc.so(x.o)还是b.o中
![Pasted image 20251012173319.png|300](/img/user/accessory/Pasted%20image%2020251012173319.png)
有可能a.o和b.o 编译成了一个a.out, 如果call 的hello在b.o中的话，如果用间接跳转是浪费的，因为后来在同一个文件中，用相对PC跳转就可以，而如果在x.o中，就必须查表了

然后这就有了PLT和GOT
plt就在a.out，如果hello在plt中的话，就会告诉你，直接根据pc相对寻址就可以
![Pasted image 20251012173548.png|300](/img/user/accessory/Pasted%20image%2020251012173548.png)

![Pasted image 20251012174416.png|400](/img/user/accessory/Pasted%20image%2020251012174416.png)

#### 数据问题
![Pasted image 20251012174601.png|300](/img/user/accessory/Pasted%20image%2020251012174601.png)
数据应该只有一个副本
![Pasted image 20251012174806.png](/img/user/accessory/Pasted%20image%2020251012174806.png)
加载器保证 在世界中会只有一个副本，所有的库就会看到同样一个stdout
