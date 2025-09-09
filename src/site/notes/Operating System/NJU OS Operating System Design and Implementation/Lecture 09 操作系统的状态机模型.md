---
{"created":"2025-09-09T08:24","updated":"2025-09-09T10:46","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 09 操作系统的状态机模型/","dgPassFrontmatter":true,"noteIcon":""}
---

**today's agenda**
操作系统上的程序是由加载器加载的，那操作系统也是程序，那操作系统是由谁加载的？加载操作系统的加载器也是个程序？
- 软件和硬件的桥梁
- 操作系统的加载和初始化
- AbstractMachine代码导读

jyy老师提到一个好玩的观点
大学的意义 -- 将已有的知识和方法重新消化，为大家建立好 “台阶”，在有限的时间里迅速赶上数十年来建立起的学科体系
大学专业 和 民间的区别 
- 专业人员需要有的素质: 看到一个bug 能够很敏锐的知道问题出现在哪里
- 有东西做不下去的时候 知道该实现什么辅助工具
- 接触经典研究论文

### 硬件和软件的桥梁 
第一个问题，我们要告诉硬件，给我把这段代码加载到我们的计算机上
第二个问题，硬件需要给我们提供一个功能，类似system call，可以让代码访问硬件
为了解决这个问题 -- 软件/硬件约定
![Pasted image 20250722151037.png](/img/user/accessory/Pasted%20image%2020250722151037.png)
就像之前我们最初将的那个数码管的那个例子
为了避免出现11，事实上他就像我们开机一样，是由一个RESET的，而这个RESET事实上就指向了00的状态，然后才能在此状态下循环起来
我们的机器也是一样的，我们都知道CPU实际上是一个执行指令的机器，从PC拿出一条指令来执行然后PC调到下一条指令，这也就是说，我们只要在CPU reset后将PC指向一个特殊的位置 能够读到一条有效的指令 能够执行下去，那么硬件和软件就连起来了
![Pasted image 20250909092128.png|300](/img/user/accessory/Pasted%20image%2020250909092128.png)
事实上，CPU reset之后，PC指针一遍指向一段memory-mapped ROM，这段ROM存储了厂商提供的firmware(固件) -- Firmware是厂商(比如联想，戴尔)提供的代码 -- 将用户数据加载到内存
事实上这是x86 Family的CPU Reset的一个行为
![Pasted image 20250909092156.png|500](/img/user/accessory/Pasted%20image%2020250909092156.png)
在Reset之后，他给一些寄存器确定了值，比如eax为0；栈顶寄存器esp也是0；PC实际上在x86中是由EIP和CS共同决定的，EIP (Extended Instruction Pointer)：保存当前正在执行的指令的偏移地址（在段内的偏移量），CS (Code Segment Register)：保存代码段的基地址和段属性
包括EFLAGS = 0x00000002 -- interrupt disabled
包括这个CR0寄存器的值是60000010，最后一个0对应着其实就是Figure中的最后四个bit是0，其中有一个Real-address model为0 -- 意味着是保护模式 -- 也就是16-bit 模式

事实上，根据x86的手册，PC的那个地方是ffff0 -- 这是一个跳转指令 会跳转到实际的fireware开始执行
![Pasted image 20250909093427.png|300](/img/user/accessory/Pasted%20image%2020250909093427.png)

![Pasted image 20250909093554.png|500](/img/user/accessory/Pasted%20image%2020250909093554.png)
事实上我小时候就见到过这个界面，这是老式的那个BIOS -- Legacy BIOS(Basic IO System) -- 这就是fireware，它负责在启动的时候做一些硬件的初始化，包括我们有多个磁盘的话可以再里面配置启动哪个磁盘，然后它会把控制权交给操作系统，其实BIOS就算是开机后第一个运行的软件
事实上第一个软件做的事情就是扫描所有的硬件，找到有操作系统的那个硬件，然后加载，然后让操作系统接管整个计算机硬件的控制权
操作系统和BIOS的约定是: 不管是什么样的磁盘(SSD, HDD...) 启动磁盘的第一个512字节叫主引导扇区(MBR), 这512个字节有fireware，会被搬到内存上的一个特定的地址上，这个地址是0x7c00
怎么看是不是启动磁盘 -- 前512字节最后会有55aa(小端法)
![Pasted image 20250909100028.png|300](/img/user/accessory/Pasted%20image%2020250909100028.png)
其实我们会发现我们Windows的第一盘都是C盘，A和B实际上是软盘，我们会先去软盘看前512个字节的最后位置是什么
![Pasted image 20250909101012.png|350](/img/user/accessory/Pasted%20image%2020250909101012.png)
所以经过BIOS之后，MBR就被放到了内存的7c00位置，同时PC指向了7c00，当然在这过程中还会初始化一些别的寄存器等等，此时CPU还是16-bit的模式，然后利用这前512字节可以加载更多的字节，加载操作系统，把CPU变成32bit模式，64bit模式...

所以这就解决了先有鸡还是先有蛋的问题 -- 事实上是先有了一个原始的鸡: Firmware, 直接存在于硬件中

今天的Firmware: UEFI
原来的设备 比如 鼠标就只有一种 就是那种圆头的 而今天 各种各样的东西，比如指纹锁，驱动，蓝牙等等.. 很多东西都要在刚刚启动的时候就要派上用场，不能等到操作系统上来之后再启动
统一了驱动的框架
标准化的加载流程
- 盘必须按 GPT (GUID Partition Table) 方式格式化
- 预留一个 FAT32 分区 (lsblk/fdisk 可以看到)
- Firmware 加载任意大小的 PE 可执行文件 `.efi`
    - 没有 legacy boot 512 字节限制
    - EFI 应用可以返回 firmware

### 操作系统的状态机模型
![Pasted image 20250909111105.png|500](/img/user/accessory/Pasted%20image%2020250909111105.png)
操作系统就是一个C语言代码
