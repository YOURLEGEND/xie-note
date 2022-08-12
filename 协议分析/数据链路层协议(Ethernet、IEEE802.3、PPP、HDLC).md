**目录**

[数据链路层协议](#t0)

[Ethernet以太网协议](#t1)

[以太网数据帧的封装](#t2)

[IEEE802.3协议](#t3)

[PPP协议](#t4)

[HDLC协议](#t5)

* * *

数据链路层协议
=======

首先Ethernet、IEEE802.3、PPP和HDLC都是数据链路层的协议，只不过后面三个不常用而已，数据链路层最常用的协议是Etnernet以太网协议。

Ethernet和IEEE802.3属于以太链路层协议

 广域网中经常会使用串行链路来提供远距离的数据传输，高级数据链路控制HDLC（High-Level Data Link Control）和点对点协议PPP（ Point to Point Protocol）是两种典型的串口封装协议。

![](https://img-blog.csdnimg.cn/20181119081551203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Ethernet以太网协议
-------------

Ethernet以太网协议，用于实现链路层的数据传输和地址封装

### 以太网数据帧的封装

![](https://img-blog.csdnimg.cn/20181119082105147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181119081902313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

从上图可以看到 Ethernet II帧，目的地址、源地址字段各占6个字节，目的地址字段确定帧的接收者，源地址字段标识帧发送者。  
当使用六个字节的源地址字段时，前三个字节表示由IEEE分配给厂商的地址，将烧录在每一块网络接口卡的ROM中。而制造商通  
常为其每一网络接口卡分配后字节。其实目的、源地址就是我们经常说的MAC地址，比如00:1A:A0:31:39:D4就是一个MAC地址。  
类型字段，为2字节，用来标识上一层所使用的协议类型，如IP协议（0x0800）,ARP(0x0806)等。  
数据字段 以太网包最小规定为64字节，不足的也会填充到64字节。以太网包的最大长度是1518字节，数据字段长度范围为46到1500，  
这是为什么呢？因为以太网包最小规定为64字节，不足的也会填充到64字节。而以太网帧格式的其他部分加起来是6+6+2+4=18字节，  
所以数据部分的最小长度为64-18=46字节；而以太网包的最大长度是1518字节，因此1518-18=1500字节。  
FCS字段是帧校验字段，即Frame Check Sequence，用来保存CRC(循环冗余校验)校验值。

IEEE802.3协议
-----------

IEEE 802.3 通常指以太网，一种网络协议。描述物理层和数据链路层的MAC子层的实现方法，在多种物理媒体上以多种速率采用CSMA/CD访问方式  
MAC（MediaAccessControl）媒体访问控制层，该层定义了数据包怎样在介质上进行传输。  
LLC （LogicalLinks Control）逻辑链路控制层

![](https://img-blog.csdnimg.cn/20181119082208691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PPP协议
-----

PPP协议是一种点到点(一根链路两端只有两个接口)链路层协议，主要用于在全双工的同异步链路上进行点到点的数据传输。

![](https://img-blog.csdnimg.cn/20181119082414640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

LCP是用来创建二层连接的，是有连接的(以太协议无连接)；NCP是用来实现三层通信的

![](https://img-blog.csdnimg.cn/20181119082445880.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20181119082456421.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181119082510229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

HDLC协议
------

HDLC(High-level Data Link Control)，高级数据链路控制，简称HDLC，是一种面向比特的链路层协议，思科私有协议，现在几乎不用