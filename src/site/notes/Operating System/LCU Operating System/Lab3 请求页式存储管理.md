---
{"dg-publish":true,"tags":["LCU操作系统"],"permalink":"/Operating System/LCU Operating System/Lab3 请求页式存储管理/","dgPassFrontmatter":true,"noteIcon":"","created":"2025-04-27T16:24:57.411+08:00","updated":"2025-04-30T23:11:08.342+08:00"}
---

Lab3主要是关于page table, virtual address 和 physical address的转换 还有 页面置换算法LRU算法
其中 page table, virtual address, physical address的知识 在CSAPP中都讲过 主要看下面这两篇笔记
- [[CSAPP Computer-System-A-Program-Perspective/Lecture 17 Virtual Memory：Concepts\|Lecture 17 Virtual Memory：Concepts]]
- [[CSAPP Computer-System-A-Program-Perspective/Lecture 18 Virtual Memory：Systems\|Lecture 18 Virtual Memory：Systems]]

第一篇blog主要是一些基础的知识，第二篇blog主要是一些例子 还有Intel i7的真实情况


LRU算法在CMU15445中我学过 主要看下面这两篇笔记
- [[DataBase Systems/CMU 15-445：Database Systems/Lecture 06 Memory & Disk IO Management\|Lecture 06 Memory & Disk IO Management]] -- Replacement Policies 部分
- [[DataBase Systems/CMU 15-445：Database Systems/Project 1 Buffer Pool\|Project 1 Buffer Pool]]  -- LRUK算法的实现

所以这个实验没啥好整理的 给的代码也没有缺失的部分 自己理解理解就行了
