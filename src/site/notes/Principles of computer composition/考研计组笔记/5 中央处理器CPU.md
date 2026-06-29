---
{"created":"2026-05-26T11:29","updated":"2026-06-25T11:34","dg-publish":true,"permalink":"/Principles of computer composition/考研计组笔记/5 中央处理器CPU/","dgPassFrontmatter":true,"noteIcon":"","dg-note-properties":{"created":"2026-05-26T11:29","updated":"2026-06-25T11:34"}}
---

### Overview
上一章已经解决了对各种各样的指令怎么用二进制编码的问题 这一章设计控制器 -- 控制器负责从主存取指令 然后对指令进行译码 根据操作码邦二进制的组合就能知道这条指令的功能 然后产生有时间先后顺序的控制信号控制各个模块来完成这条指令所完成的功能 通常一条指令处理完了之后它会顺序的从内存里面取下一条指令 然后再处理下一条指令
![Pasted image 20260629172634.png\|500](/img/user/accessory/Pasted%20image%2020260629172634.png)
讲控制器的设计就是用这么一个简单的CPU模型来说的