---
{"created":"2025-09-11T19:43","updated":"2025-09-11T21:34","dg-publish":true,"permalink":"/Operating System/Lecture 10 状态机模型的应用/","dgPassFrontmatter":true,"noteIcon":""}
---

### 状态机: 理解我们的世界
在之前的课中，实际上是从一个模拟晶体管的状态介绍起的，理论上，懂了这个数字电路中状态的转换，就能建立起整个计算机；事实上，这个数字电路就是一个状态机，而程序也是一个状态机，是对寄存器和内存的状态的转变，在特定的时候调用系统调用将权利交给操作系统然后躺平；而操作系统也是一个状态机，不同于普通的程序的是，在它的状态转换中，存在多CPU，多线程，存在中断，存在虚拟地址的转化...
那我们的物理世界是状态机吗？ 在之前学到的，有的在经过规则之后，都会转换成一个唯一的状态，这叫deterministic(确定)的状态机；同样在并发程序的时候，可能会某个线程执行一步，这是non-deterministic
那物理世界是deterministic的还是non-deterministic的？
- 在经典力学的时代，宏观世界是近似于deterministic的
- 在量子力学中，可能是non-deterministic的

？可能可以预测世界？ 时间旅行？ 平行宇宙？
![Pasted image 20250911201558.png|300](/img/user/accessory/Pasted%20image%2020250911201558.png)

状态机还能帮助我们理解编译器和现代CPU
其实之前的课讲过，C程序是个状态机，编译器也是一个状态机，正确的编译是指syscall和syscall保持一致和准确，除此之外，可以做各种各种的优化
前面其实也提到过，CPU在执行的时候，只要让状态机的执行结构看起来一样，没必要严格按照语义来，所以会有超流水线，超标量等，多条指令执行，乱序执行
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/ilp-demo.c
```

```cpp
#include <stdio.h>
#include <time.h>
#define LOOP 1000000000ul
__attribute__((noinline)) void loop() {
  for (long i = 0; i < LOOP; i++) {
    asm volatile(
      "mov $1, %%rax;"
      "mov $1, %%rdi;"
      "mov $1, %%rsi;"
      "mov $1, %%rdx;"
      "mov $1, %%rcx;"
      "mov $1, %%r10;"
      "mov $1, %%r8;"
      "mov $1, %%r9;"
    :::"rax", "rdi", "rsi", "rdx", "rcx", "r10", "r8", "r9");
  }
}
int main() {
  clock_t st = clock();
  loop();
  clock_t ed = clock();
  double inst = LOOP * (8 + 2) / 1000000000;
  double ips = inst / ((ed - st) * 1.0 / CLOCKS_PER_SEC);
  printf("%.2lfG instructions/s\n", ips);
}
```
我的处理器13th Gen Intel(R) Core(TM) i9-13900H (2.60 GHz)
但事实上执行的结果
![Pasted image 20250911204216.png|500](/img/user/accessory/Pasted%20image%2020250911204216.png)

### 查看状态机执行
程序执行=状态机执行
我们能不能看到状态机的执行？
如果我们想关注system call，那么一个好的工具就是strace
```bash
strace ./ilp-demo.out
# 如果想看时间的 还可以加个-T
strace -T ./lip-demo.out
```

观看整个状态机一步一步怎么执行的 -- gdb
今天我还装了一个gdb的可视化工具 pwndbg 看着还行
gdb甚至可以往后退，有快照，可以改变状态机的执行
回溯记录的是什么？ -- 记录状态的话 代价是非常大的 -- 实际上记录的是操作或者说是行为！ 实际上就像数据库中的日志那样
事实上在gdb中开启了记录`record`真的可以回溯`reverse-step/reverse-next(rs,rn)`


