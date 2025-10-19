---
{"created":"2025-10-19T10:19","updated":"2025-10-19T10:37","dg-publish":true,"permalink":"/Operating System/NJU OS Operating System Design and Implementation/qemu/","dgPassFrontmatter":true,"noteIcon":""}
---

我的Ubuntu最高能找到的qemu为5.6版本，但是xv6-riscv要求qemu的版本要在7.2以上
![Pasted image 20251016195544.png|400](/img/user/accessory/Pasted%20image%2020251016195544.png)

最后是参考了这篇blog[Ubuntu 22.04 安装 QEMU 流程 - arcsin2 的个人博客](https://arcsin2.cloud/2023/03/03/Ubuntu-22-04-%E5%AE%89%E8%A3%85-QEMU-%E6%B5%81%E7%A8%8B/)
```bash
wget https://download.qemu.org/qemu-7.2.0.tar.xz
tar xvJf qemu-7.2.0.tar.xz # 这个解压了好久
cd qemu-7.2.0

./configure
```
如果`./configure`的时候报错，需要执行
```shell
sudo apt-get install ninja-build  
sudo apt-get install zlib1g zlib1g-dev  
sudo apt-get install libglib2.0-dev  
sudo apt-get install libpixman-1-dev
```

然后重新执行`./configure`
```shell
make
# 呃呃我这个make了好几天 一共9000多个文件

sudo make install

```

输入`qemu-`按两次TAB，若出现下面类似的输出，则安装成功
![Pasted image 20251019102945.png|400](/img/user/accessory/Pasted%20image%2020251019102945.png)



