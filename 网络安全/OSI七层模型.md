**目录**

[OSI七层参考模型](#t0)

[应用层](#t1)

[表示层](#t2)

[会话层](#t3)

[传输层](#t4)

[网络层](#t5)

[数据链路层](#t6)

[物理层](#t7)

* * *

OSI七层参考模型
=========

学计算机的人想必都对OSI七层参考模型不陌生，OSI七层参考模型是国际标准化组织（ISO）制定的一个用于计算机或通信系统间互联的标准体系。它是一个七层的、抽象的模型体，不仅包括一系列抽象的术语或概念，也包括具体的协议

那么，OSI七层参考模型包含哪七层呢？

![](https://img-blog.csdnimg.cn/20181119075346468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2018111907484226.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181119075107789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

应用层
---

[应用层](https://so.csdn.net/so/search?q=%E5%BA%94%E7%94%A8%E5%B1%82&spm=1001.2101.3001.7020)：应用层的协议有HTTP协议、FTP协议、Telnet协议、DNS协议、SMTP协议、POP3协议等等

传送门——> [Http协议](https://blog.csdn.net/qq_36119192/article/details/82942185)

表示层
---

表示层：表示层对上层数据或信息进行变换以保证一个主机应用层信息可以被另一个主机的应用程序理解。表示层的数据转换包括数据的加密、压缩、格式转换等

会话层
---

会话层：会话层管理主机之间的会话进程，即负责建立、管理、终止进程之间的会话。会话层还利用在数据中插入校验点来实现数据的同步

传输层
---

传输层：传输层的协议有TCP协议、UDP协议。

数据在传输层层以 segment（分段）形式发送

传送门——> [TCP、UDP协议](https://blog.csdn.net/qq_36119192/article/details/83500569)

网络层
---

[网络层](https://so.csdn.net/so/search?q=%E7%BD%91%E7%BB%9C%E5%B1%82&spm=1001.2101.3001.7020)：主要的设备有路由器

网络层的协议有IP协议、ICMP协议、ARP协议、RARP协议。

数据在网络层以package(包)的形式发送

传送门——>[IP协议](https://blog.csdn.net/qq_36119192/article/details/83536130) 、 [ICMP协议](https://blog.csdn.net/qq_36119192/article/details/83649845) 、[ARP协议](https://blog.csdn.net/qq_36119192/article/details/83572820)

数据链路层
-----

[数据链路层](https://so.csdn.net/so/search?q=%E6%95%B0%E6%8D%AE%E9%93%BE%E8%B7%AF%E5%B1%82&spm=1001.2101.3001.7020)：分为MAC子层 和 LLC子层(IEEE802.3协议中存在，现在几乎不用)。

数据链路层主要的设备有网卡、网桥、交换机 。

数据链路层主要的协议：以太网协议(Ethernet II)、IEEE802.3协议。

数据在数据链路层以frame(帧)的形式发送

传送门——> [数据链路层协议(Ethernet、IEEE802.3、PPP、HDLC)](https://blog.csdn.net/qq_36119192/article/details/84224111)

物理层
---

物理层主要是用来定义电压，接口线缆等标准。

物理层设备有中继器、集线器(HUB)、同轴电缆，双绞线、光纤等。

数据在物理层以bit(比特流)的形式发送

相关文章：[OSI安全体系结构](https://blog.csdn.net/qq_36119192/article/details/84336999)