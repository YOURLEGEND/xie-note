### ysoserial.jar

ysoserial是一款用于生成利用不安全的Java对象[反序列化](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)的有效负载的概念验证工具。

项目地址：[https://github.com/frohoff/ysoserial](https://github.com/frohoff/ysoserial)

  ysoserial是在常见的java库中发现的一组实用程序和面向属性的编程“小工具链”，在适当的条件下，可以利用执行对象不安全反序列化的Java应用程序。主驱动程序接受用户指定的命令，并将其封装在用户指定的小工具链中，然后将这些对象序列化为stdout。当类路径上具有所需小工具的应用程序不安全地反序列化该数据时，将自动调用该链并导致在应用程序主机上执行该命令。

### ysoserial的使用

![](https://img-blog.csdnimg.cn/20200719153241624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### JRMP

我们经常在执行攻击命令的时候，会看到命令中有 ysoserial.exploit.JRMPListener 和 ysoserial.exploit.JRMPClient，那么JRMP到底是什么呢？

JRMP(Java Remote Method Protocol) Java远程方法协议，JRMP是Java技术协议的具体对象为希望和远程引用。JRMP只能Java特有的，基于流的协议。相对于的RMI - IIOP，JRMP只能是一个对象的Java到Java的远程调用，这使得它依赖语言，意思是客户端和服务器必须使用Java。

以下是 ysoserial.exploit 下的JRMP相关方法。

![](https://img-blog.csdnimg.cn/20200719155039447.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在ysoserial.payloads下也有JRMP相关方法

![](https://img-blog.csdnimg.cn/20200719155914598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

ysoserial 中的 exploit/JRMPClient 是作为攻击方的代码，一般会结合 payloads/JRMPLIstener 使用，攻击流程就是：

1.  先往存在漏洞的服务器发送 payloads/JRMPLIstener，使服务器反序列化该payload后，会开启一个 RMI服务并监听在设置的端口
2.  然后攻击方在自己的服务器使用exploit/JRMPClient与存在漏洞的服务器进行通信，并且发送一个可命令执行的payload（假如存在漏洞的服务器中有使用org.apache.commons.collections包，则可以发送CommonsCollections系列的payload），从而达到命令执行的结果。

参考文章：[java 反序列化 ysoserial exploit/JRMPClient 原理剖析](https://blog.csdn.net/whatday/article/details/106971531)