---
{"created":"2025-09-04T08:27","updated":"2025-09-04T08:57","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/Lecture 08 并发Bug和应对/","dgPassFrontmatter":true,"noteIcon":""}
---

**today's agenda**
- 应对bug和并发bug的方法
- 死锁和数据竞争

### 应对Bug的方法
Bug多的根本原因: 编程语言的缺陷
软件是需求在计算机世界的投影
比如之前用到的那个山寨支付宝的程序
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/alipay.c
```

```cpp
#include "thread.h"  
  
unsigned long balance = 100;  
  
void Alipay_withdraw(int amt) {  
  if (balance >= amt) {  
    usleep(1); // unexpected delays  
    balance -= amt;  
  }  
}  
  
void Talipay(int id) {  
  Alipay_withdraw(100);  
}  
  
int main() {  
  create(Talipay);  
  create(Talipay);  
  join();  
  printf("balance = %lu\n", balance);  
}
```
这个程序最后的结果是一个很大很大的数 这是无符号的原因
我们的编程语言在表达我们物理世界的需求的时候是有损的，损失了钱的很多属性
在编程语言中，balance就是一个数值，和其他的比如时间戳等存储的东西是一样的(在编译器翻译的过程中来看)
所以 我们知道balance可能不会是负数 所以会加一些assert断言 -> 通过断言吧丢失的属性加回来 代表着我们对这个东西的一个认识
再比如一个平衡树旋转的例子
![Pasted image 20250904091522.png|500](/img/user/accessory/Pasted%20image%2020250904091522.png)
当我们知道我们想要什么的时候，就能写出assert来，其实当写出assert的时候，也就知道上面的实现代码怎么写了，甚至可以通过现代的一些工具来帮助实现这些代码
其实有时候写assertion并不要求严格 比如可能只是assertion了一个数值范围，当出现乱码的时候，可能就是memory error

### 并发Bug: 死锁(Deadlock)
A deadlock is a state in which each member of a group is waiting for another member, including itself, to take action.
![Pasted image 20250904092609.png|500](/img/user/accessory/Pasted%20image%2020250904092609.png)
一个线程可能就会发生死锁
比如在不该打开中断的时候开了中断，那么就变成了自己等自己
```cpp
void os_run() {
  spin_lock(&list_lock);  
  spin_lock(&xxx);        
  spin_unlock(&xxx);       
}                          

void on_interrupt() {
  spin_lock(&list_lock);
  spin_unlock(&list_lock);
}

```
如果自旋的时候中断了，自旋锁在等待中断结束才释放，而中断可能第一步就像获取这把锁 那就造成了自己等自己的情况

**ABBA-Deadlock**
比如swap函数
```cpp
void swap(int i, int j) {
spin_lock(&lock[i]);
spin_lock(&lock[j]);
arr[i] = NULL;
arr[j] = arr[i];
spin_unlock(&lock[j]);
spin_unlock(&lock[i]);
}
```
如果swap的顺序有问题的话，比如swap(1,2); swap(2,3); swap(3,1)的时候，并行的时候第一个swap获取了1这把锁，第二个获取了2这把锁，第三个获取了3这把锁 -- 这就相当于哲学家吃饭问题的时候都举起了左手的叉子 -- 就会造成死锁

在很多教科书上，我记得我们当时学的时候也提了
死锁产生的四个必要条件
- 互斥：一个资源每次只能被一个进程使用
- 请求与保持：一个进程请求资阻塞时，不释放已获得的资源
- 不剥夺：进程已获得的资源不能强行剥夺
- 循环等待：若干进程之间形成头尾相接的循环等待资源关系

事实上想要破除这四个条件还是蛮难的
但还是有一些方法可以避免死锁的
AA-Deadlock -- xv6中的自旋锁
```cpp
// Mutual exclusion spin locks.  
  
#include "types.h"  
#include "param.h"  
#include "memlayout.h"  
#include "spinlock.h"  
#include "riscv.h"  
#include "proc.h"  
#include "defs.h"  
  
void  
initlock(struct spinlock *lk, char *name)  
{  
  lk->name = name;  
  lk->locked = 0;  
  lk->cpu = 0;  
}  
  
// Acquire the lock.  
// Loops (spins) until the lock is acquired.  
void  
acquire(struct spinlock *lk)  
{  
  push_off(); // disable interrupts to avoid deadlock.  
  if(holding(lk))  
    panic("acquire");  
  
  // On RISC-V, sync_lock_test_and_set turns into an atomic swap:  
  //   a5 = 1  //   s1 = &lk->locked  //   amoswap.w.aq a5, a5, (s1)  while(__sync_lock_test_and_set(&lk->locked, 1) != 0)  
    ;  
  
  // Tell the C compiler and the processor to not move loads or stores  
  // past this point, to ensure that the critical section's memory  // references happen strictly after the lock is acquired.  // On RISC-V, this emits a fence instruction.  __sync_synchronize();  
  
  // Record info about lock acquisition for holding() and debugging.  
  lk->cpu = mycpu();  
}  
  
// Release the lock.  
void  
release(struct spinlock *lk)  
{  
  if(!holding(lk))  
    panic("release");  
  
  lk->cpu = 0;  
  
  // Tell the C compiler and the CPU to not move loads or stores  
  // past this point, to ensure that all the stores in the critical  // section are visible to other CPUs before the lock is released,  // and that loads in the critical section occur strictly before  // the lock is released.  // On RISC-V, this emits a fence instruction.  __sync_synchronize();  
  
  // Release the lock, equivalent to lk->locked = 0.  
  // This code doesn't use a C assignment, since the C standard  // implies that an assignment might be implemented with  // multiple store instructions.  // On RISC-V, sync_lock_release turns into an atomic swap:  //   s1 = &lk->locked  //   amoswap.w zero, zero, (s1)  __sync_lock_release(&lk->locked);  
  
  pop_off();  
}  
  
// Check whether this cpu is holding the lock.  
// Interrupts must be off.  
int  
holding(struct spinlock *lk)  
{  
  int r;  
  r = (lk->locked && lk->cpu == mycpu());  
  return r;  
}  
  
// push_off/pop_off are like intr_off()/intr_on() except that they are matched:  
// it takes two pop_off()s to undo two push_off()s.  Also, if interrupts  
// are initially off, then push_off, pop_off leaves them off.  
  
void  
push_off(void)  
{  
  int old = intr_get();  
  
  intr_off();  
  if(mycpu()->noff == 0)  
    mycpu()->intena = old;  
  mycpu()->noff += 1;  
}  
  
void  
pop_off(void)  
{  
  struct cpu *c = mycpu();  
  if(intr_get())  
    panic("pop_off - interruptible");  
  if(c->noff < 1)  
    panic("pop_off");  
  c->noff -= 1;  
  if(c->noff == 0 && c->intena)  
    intr_on();  
}
```

在其中用到了很多防御性的编程，比如`if (holding(lk)) panic();`
-  `release` 里的 `if (!holding(lk)) panic("release");` → 防止 不是自己解锁。
- `acquire` 里的 `if (holding(lk)) panic("acquire");` → 防止 自己递归上锁

ABBA-Deadlock
- 任意时刻死锁都是有限的
- 如果我们所有的线程都按照同样的顺序来获得锁的话 -- 就可以避免死锁

### 并发Bug：数据竞争(Data Race)
不上锁 -- 会数据竞争
有点像数据库的那个啊hh 其实数据库的那个也是并发导致的
![Pasted image 20250904101506.png|400](/img/user/accessory/Pasted%20image%2020250904101506.png)
但数据竞争不代表就不正确，比如说前面的peterson
消灭数据竞争 -- 互斥锁
![Pasted image 20250904101954.png](/img/user/accessory/Pasted%20image%2020250904101954.png)
最简单的方法就是上一把大锁，就相当于串行了其实，但是有性能问题，所以有粒度的问题(数据库中也是这样，只不过数据库里面更具体了，比如是page, table, turple的锁)

### 更多类型的并发Bug
回顾并发控制的工具
- 互斥锁 -- 原子性
- 条件变量 -- 同步

事实上，97%的非死锁问题其实都是违背了原子性和顺序
![Pasted image 20250904143717.png|400](/img/user/accessory/Pasted%20image%2020250904143717.png)

![Pasted image 20250904144030.png|400](/img/user/accessory/Pasted%20image%2020250904144030.png)

![Pasted image 20250904144100.png|400](/img/user/accessory/Pasted%20image%2020250904144100.png)


### 应对并发Bug的方法
跟应对普通Bug的方法是一样的，就是认为自己会犯错，然后检查，在普通Bug中用的是assertion
事实上并发Bug的测试并不是那么简单的，因为有些并发Bug确实很罕见，但我们可以检查
比如Lock Ordering(前面讲的上锁的顺序) -- 这是可以检查的 -- Lockdep规范
**Lockdep规范: 运行时的死锁检查**
老师给了一段代码
```bash
wget https://jyywiki.cn/pages/OS/2022/demos/lock-site.c
```
这段代码的作用简单来说就是给每一个锁获取了一个唯一的标识，然后在调用的时候打印，这样就形成了一个上锁解锁的日志
![Pasted image 20250904145650.png|500](/img/user/accessory/Pasted%20image%2020250904145650.png)
这样其实我们是维护了一个队列或者说图
![Pasted image 20250904145350.png|400](/img/user/accessory/Pasted%20image%2020250904145350.png)
如果出现环就说明有人违反了lock ordering
Linux和Harmony中都有这个Lockdep规范

**ThreadSanitizer: 运行时的数据竞争检查**
为所有事件建立happen-before的关系
上锁和解锁会建立一个线程间的happen-before关系；线程内部也有happen-before关系
给两个happen-before做一个传递闭包 就会有所有事件中能排序的事件的前后关系
![Pasted image 20250904150938.png|400](/img/user/accessory/Pasted%20image%2020250904150938.png)

但是会存在一些时间是不能排序的，比如说最下面的两个蓝色的点就不能排序，这就意味着可以往前移，只要两个撞一起了，就造成了数据竞争
如果一个点到一个点 如果有路径 就不是数据竞争； 如果没有路径 就是数据竞争

其实这两个方法本质上就是在事件发生的时候做记录，然后做分析 -- 这属于动态程序分析
开源社区给了好多好多动态程序分析工具
比如C语言有一个很常见的问题
```c
#include <stdlib.h>  
#include <string.h>  
  
int main() {  
  int *ptr = malloc(sizeof(int));  
  *ptr = 1;  
  free(ptr);  
  *ptr = 1;  
}
```
太多太多这样的问题了，C语言可以可能偷偷覆盖掉任何一个内存
我们平时编译器 编译的时候其实都是开启的默认的编译，默认的编译是不会抱任何错误的
但其实有工具可以检查
```bash
gcc -Og uaf.c -fsanitize=address -o uaf.out
```

![Pasted image 20250904152813.png|500](/img/user/accessory/Pasted%20image%2020250904152813.png)

同样的，比如之前的sum.c
```bash
gcc -Og sum.c -lpthread -fsanitize=thread -o sum.out
```

上面这些 不都是 防御性编程吗？ -- 但上面这些的好处的没有写入那么多assertion，没有改变代码的可读性，而是悄悄地给你做检查

计算机系统中的 canary
- “牺牲” 一些内存单元，来预警 memory error 的发生

我好像在csapp中看过啊
当时好像讲的是字符的那个缓冲区
总之核心思想就是牺牲一些内存来存放一些特殊的字符，然后定期去检查
![Pasted image 20250904154816.png|400](/img/user/accessory/Pasted%20image%2020250904154816.png)

![Pasted image 20250904154922.png|500](/img/user/accessory/Pasted%20image%2020250904154922.png)

在Visual Statio的模式中，不浪费空间，但是给没有初始化的栈和堆都赋值
windows的中文是GBK来解码 就是烫烫烫
