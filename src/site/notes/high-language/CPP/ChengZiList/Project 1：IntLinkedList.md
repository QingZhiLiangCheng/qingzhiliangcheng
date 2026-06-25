---
{"tags":["project","ChengZiList"],"dg-publish":true,"created":"2025-04-29T16:02:27.866+08:00","updated":"2025-04-29T22:20:06.626+08:00","permalink":"/high-language/CPP/ChengZiList/Project 1：IntLinkedList/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"tags":["project","ChengZiList"],"created":"2025-04-29T16:02:27.866+08:00","updated":"2025-04-29T22:20:06.626+08:00"}}
---

### Overview
在[[high-language/CPP/ChengZiList/Project 0：SimpleList\|Project 0：SimpleList]]中 你已经实现了一个简单的链表SimpleList.
但是很明显SimpleList存在很多问题 丑陋 臃肿 比如
- 对于到底是一个节点还是一个链表 有点模糊不清
- 可以直接访问并修改类中的元素
- size函数需要遍历整个链表
- 一直`.next.next` 晦涩 难懂
- ....

在这个Project中, 这些问题都将得到解决, 我们将通过一步一步Improvement 实现一个适用于int类型数据的更接近现代语言实现的链表(不过到此为止我们还未提及函数模版)

在此之前，假设你在Project 0 所实现的链表结构如下
```cpp
class SimpleList{
public:
	int data_;
	SimpleList* next_;
	...
};
```
那么事实上我们可以通过一下的代码进行头插法
```cpp
SimpleList simple_list = new SimpleList(1,nullptr);
simple_list = new SimpleList(2,simple_list);
```

一定要确保你在写代码之前将项目更新到最新状态
在确保了你push过代码后 然后
```bash
git pull QingZhiLiangCheng main
```
然后可能要接受一些更改 比如把你曾经写过的代码和我新增的代码都接受到新文件中
最后一定要在提交到你自己的git仓库 才算合并完成

我可能会改过文件 你可能需要删掉CLion中的`cmake-build-debug`文件 点构建然后选择重新加载Cmake目录
### Improvement 1: Rebranding and Bureaucracy
不难发现，事实上一个简单的链表是由一个个节点所组成的, 一个好的思想是封装的时候我们要细化到最小的结构和模块，这是一种工程化的思想，所以这里你需要实现`IntNode`类并用`IntNode`类构成`IntList`类，并体会这种思想的好处。


> [!todo] Task 1
> 你需要在`src/int_node.h`文件中实现IntNode类,并在`src/int_linked_list.h`文件中实现一个IntLinkedList类。
> IntNode类:
> - 实现一个名为`IntNode`的类，用于表示整数链表中的单个节点。
> - 每个节点包含两个主要部分：
>     - `data`：存储整数值。
>     - `next`：指向下一个节点的指针（对于最后一个节点，该值应为`nullptr`）
> 
> IntLinkedList 类
> - 实现一个名为`IntLinkedList`类，用于管理由`IntNode`组成的链表
> - 定义一个指向链表第一个节点的指针`first_`
> - 实现必要的构造函数和析构函数
> - 实现AddFirst和GetFirst两个方法
> 	- `void AddFirst(int data);`
> 	- `auto GetFirst() -> int`
>
![Pasted image 20250429163242.png\|500](/img/user/accessory/Pasted%20image%2020250429163242.png) 

其实很容易我们能体会到IntLinkList和SimpleList的区别 IntLinkedList把细节都隐藏起来了 他更像一个中间人 帮我们进入数据结构 这也是我们计算机中一种抽象和模块化的思想 就是说使用者不必再用其中实现的细节 只需要使用易懂的API 像读句子一样 读懂句子就能看懂功能（比如AddFirst 看到就知道什么功能）

### Improvent 2: Public vs. Private
但是上面的IntLinkedList仍然存在问题 因为我们可以绕过这个中间人(IntLinkedList) 直接访问里面的naked data structure 甚至可以操控data和next域 比如`int_linked_list.first_.next_.data_=10;`
我们可以使用private关键字声明来达到想要的效果

> [!todo] Task 2
> 将first_成员变量声明为private，那么这个成员变量只能在IntLinkedList类的成员函数中被访问和修改

### Improvent 3: Nested Classes
你会发现 事实上 IntNode仅仅是一个IntLinkedList的一个特性 IntNode好像并没有作为一个直接交互的项目 它没有自己的函数声明 更像是一个属性
所以我们会使用嵌套类(nested classed)

> [!todo] Task 3
> 1. 将IntNode作为内部类插入IntLinkedList中 并删除`src/int_node.h`文件
> 2. 实现AddLast函数 `void AddLast(int data)`
> 3. 实现Size函数 (如果有能力可以试一下迭代和递归两种方法)

### Improvement 4: Cashing
在Improvement 3中实现的Size和AddLast的缺点非常明显
如果迭代的话需要O(n)的复杂度 如果递归的话 开销可能会更大(可以去理解一下递归的原理)
为此，我们只需在 `SLList` 类中添加一个 `size_` 变量来跟踪当前链表的长度，这种将重要数据保存起来以加快后续访问速度的做法，有时被称为 缓存（caching）
这是计算机中一个典型的哲学思想: 权衡取舍. 说白了 就是拿着空间换效率
这里多说几句 现在计算机发展的水平来说 更缺乏的时间 因为我们的内存和硬盘还是蛮够用的 原来在打孔纸带的时候算力是一种服务(因为需要排队用机房的机子) 而现在算力又成了一种服务 hhh这是听南京大学NJU jyy老师说的

> [!todo] Task 4
> 加入`size_`变量来跟踪链表的长度
> 记得在对应的函数中都要维护`size_`

其实也可以加一个tail_ 来记录尾节点 但是其实会引发一个问题（其实我觉得并不是特别要紧）这个问题将在[[high-language/CPP/ChengZiList/Project 2：LinkedList\|Project 2：LinkedList]]中提到

### Improvement 5: Sentinel Head Node
截止到现在 我猜你已经实现了类似下面的代码
``` cpp
IntLinkedList():size_(0),first_(nullptr) {};

void AddLast(int data) {
	size_++;
	IntNode* p = first_;
	while(p->next != nullptr){
		p = p -> next;
	}
	p->next = new IntLinkedList(data, nullptr);
	...
}
```
这会有一个潜在的bug 就是当建立空`int_linked_list`的时候再调用AddLast 会报错 因为在`p->next`的时候`first_`是nullptr
所以我们要进行特殊判断 be like
```cpp
void AddLast(int x){
	size_++;
	if(size_==0){
		first_=new IntNode(x,nullptr);
		return;
	}
	IntNode* p = first_;
	while(p->next != nullptr){
		p = p -> next;
	}
	p->next = new IntLinkedList(data, nullptr);
}
```

问题是 加上特殊情况判断可能显得代码变得复杂 冗长
而且每当我们增加功能的时候 可能都要加上或多或少的特殊情况判断 而且可能在bug出现之前我们并不能意识到这里存在特殊情况
所以一种更好的方式 是使用有虚拟头结点的链表

> [!todo] Task 5
> 在原有基础上 实现有虚拟头结点的链表 并在必要的函数中进行维护

### 测试并提交自己git仓库
**运行测试**
你需要先前往`test/int_linked_list_test`中将ifdef和endif标签去掉
你可以使用测试框架测试 我们将GTest用于单元测试用例 你可以从命令行单独编译和运行每一个测试(如果您是Ubantu的话)
```bash
cd build
make int_linked_list_test -j
./test/int_linked_list_test
```
在CLion中配置运行调试配置处 配置 `int_linked_list_test` 点击运行也可以
运行通过即意味着Project 1通过啦 🥰
**提交您的代码**
您可以将自己的代码提交到自己的git仓库 不要提交到我的git仓库啊