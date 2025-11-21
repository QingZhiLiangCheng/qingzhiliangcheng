---
{"created":"2025-11-17T10:29","updated":"2025-11-18T15:37","dg-publish":true,"permalink":"/LCU软件设计和体系结构/UML类图基础/","dgPassFrontmatter":true,"noteIcon":""}
---

![Pasted image 20251118153233.png](/img/user/accessory/Pasted%20image%2020251118153233.png)

| 关系类型   | 中文含义     | 耦合强度   | 生命周期关系 | 生活比喻    | UML    |
| :----- | :------- | :----- | :----- | :------ | ------ |
| **继承** | 是一个      | **最强** | -      | 你和你的父母  |        |
| **组合** | 拥有（不可分割） | 强      | 同生共死   | 人和心脏    | 实心菱形直线 |
| **聚合** | 拥有（可分割）  | 中      | 独立     | 班级和学生   | 空心菱形直线 |
| **关联** | 有一个      | 较弱     | 独立     | 老师和学生   | 直线     |
| **依赖** | 用一下      | **最弱** | 完全无关   | 我和借来用的笔 | 虚线     |

![Pasted image 20251117103055.png|500](/img/user/accessory/Pasted%20image%2020251117103055.png)

![Pasted image 20251117103345.png|400](/img/user/accessory/Pasted%20image%2020251117103345.png)

实现继承
![Pasted image 20251118153327.png|400](/img/user/accessory/Pasted%20image%2020251118153327.png)
实现接口
![Pasted image 20251118153334.png|400](/img/user/accessory/Pasted%20image%2020251118153334.png)

关联关系
![Pasted image 20251118153401.png|400](/img/user/accessory/Pasted%20image%2020251118153401.png)
```csharp
class Pengui : Bird
{	
	private Climate calimate;
}

```

聚合关系
![Pasted image 20251118153502.png|400](/img/user/accessory/Pasted%20image%2020251118153502.png)
看下大雁和雁群这个两个类，大雁是群居动物，每只大雁都是属于一个雁群，一个雁群可以有多只大雁。所以它们之间就满足聚合关系，聚合关系用空心菱形 + 实线箭头来表示。
```csharp
class WildGooseGroup
{
	private WildGoose[] _WildGooseArray
}

```

合成关系
![Pasted image 20251118153543.png|400](/img/user/accessory/Pasted%20image%2020251118153543.png)
这里鸟类和翅膀类是合成关系，因为它们是部分和整体的关系，并且这里的鸟和翅膀的生命周期是相同的。合成关系用实心菱形 + 实线箭头来表示。
这里连线两端还有两个数字，这被称为基数，这表明一端的类可以有几个实例。若一个类可以有无数个实例，则就可以以用‘n’来表示。关联关系，聚合关系也可以有基数。
代码解析： 在鸟(Bird)类中，初始化时，实例化翅膀(Wing)，它们之间同时生成
```csharp
class Bird : Animal 
{
	private Wing wing;
	private Bird()
	{
		wing = new Wing();
	}
} 

```

依赖关系
![Pasted image 20251118153634.png|400](/img/user/accessory/Pasted%20image%2020251118153634.png)

```csharp
abstract class Animal
{
	public Metabolism(Oxygen oxy, Water water)
	{
	}
}

```