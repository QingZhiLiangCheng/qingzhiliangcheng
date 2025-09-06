---
{"created":"2025-09-06T09:40","updated":"2025-09-06T09:48","dg-publish":true,"permalink":"/LCU 软件测试/Lecture 01 部署大学生学籍管理系统/","dgPassFrontmatter":true,"noteIcon":""}
---

相关资料见 https://pan.baidu.com/s/1yGQ6YBS0JGZqfTOUEHRhkw?pwd=QZLC 
按照老师的步骤来就行，步骤见ppt
我这里整理了两个我在我自己电脑上部署的时候遇到的问题
1. 我的电脑上的JDK默认版本有点高，Tomcat直接启动不起来，然后我又装了老师的JDK1.6，重新配置了环境变量才解决了Tomcat启动的问题
2. 大学生管理系统的部署成功的标志是要自己跳出一个学号来![Pasted image 20250906094347.png|400](/img/user/accessory/Pasted%20image%2020250906094347.png)一开始我在这里失败了，但是检查配置文件密码，数据库等都没有问题，后来看了tomcat的报错，发现是Java项目中配置的MySQL驱动版本太低了，最后我重新装了老师给的MySQL5.0环境，将端口配置成了3307，服务名叫MySQL50，这样我的电脑上就并存了两个MySQL服务

![a67e5742946bfaa3cf5e3490b9a315c5.png|300](/img/user/accessory/a67e5742946bfaa3cf5e3490b9a315c5.png)

