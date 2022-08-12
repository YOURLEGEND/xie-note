**目录**

[TCP](#t0)

[三次握手](#t1)

[四次挥手](#t2)

[为什么建立连接是三次而断开连接是四次呢？](#t3)

[TCP和UDP的区别](#t4)

[TCP数据包的封装](#t5)

[UDP数据包封装](#t6)

[SCTP](#t7)

[SYN Flood泛洪攻击](#t8)

* * *

TCP
---

**TCP**(Transmission Control Protocol) 传输控制协议，是面向连接的协议，属于[OSI七层模型](https://so.csdn.net/so/search?q=OSI%E4%B8%83%E5%B1%82%E6%A8%A1%E5%9E%8B&spm=1001.2101.3001.7020)中的传输层协议  
我们所熟知的FTP、[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)、Telnet、HTTP、HTTPS、SMTP、POP3等都是使用TCP协议

TCP最重要的一个特点就是面向连接的协议，一次TCP的会话连接需要三次握手，会话断开需要四次挥手

在分析三次握手和四次挥手前先看一下数据包交互过程中各信号代表着什么

  （1）序号：seq序号，占32位，用来标识从TCP源端向目的端发送的字节流，发起方发送数据时对此进行标记。   
  （2）确认序号：Ack序号，占32位，当Ack=seq+1时，确认序号有效。   
  （3）标志位：共6个，即URG、ACK、PSH、RST、SYN、FIN等，具体含义如下：   
  （A）URG：紧急指针（urgent pointer）有效。   
  （B）ACK：确认序号有效。   
  （C）PUSH：接收方应该尽快将这个报文交给应用层。   
  （D）RST：重置连接。   
  （E）SYN(_Synchronize Sequence Numbers_)：发起一个新连接。   
  （F）FIN：释放一个连接。   
需要注意的是：   
  （A）不要将确认序号Ack与标志位中的ACK搞混了。   
  （B）确认方Ack=发起方seq+1，两端配对。

### 三次握手

![](https://img-blog.csdnimg.cn/20181029080851378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

  （1）第一次握手：Client将标志位SYN置为1，随机产生一个值 **Seq=x**，并将该数据包发送给Server，Client进入 **SYN\_SENT** 状态，等待Server确认。   
  （2）第二次握手：Server收到数据包后由标志位SYN=1知道Client请求建立连接，Server将标志位SYN和ACK都置为1，**Ack=x+1**，随机产生一个值 **Seq=y**，并将该数据包发送给Client以确认连接请求，Server进入 **SYN\_RCVD** 状态。   
  （3）第三次握手：Client收到确认后，检Ack是否为 x+1，ACK是否为1，如果正确则将标志位ACK置为1,**Seq=x+1,Ack=y+1**，并将该数据包发送给Server，Server检查Ack是否为y+1，ACK是否为1，如果正确则连接建立成功，Client和Server进入**ESTABLISHED** 状态，完成三次握手，随后Client与Server之间可以开始传输数据了

### ![](https://img-blog.csdnimg.cn/20181029081252998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)  
四次挥手

![](https://img-blog.csdnimg.cn/20181029081553528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

  （1）第一次挥手：Client发送一个FIN，Seq=u,用来关闭Client到Server的数据传送，Client进入FIN\_WAIT\_1状态。   
  （2）第二次挥手：Server收到FIN后，发送一个ACK=1给Client，确认序号Ack=u+1，Server进入CLOSE\_WAIT状态。   
  （3）第三次挥手：Server发送一个FIN，Seq=w,用来关闭Server到Client的数据传送，Server进入LAST\_ACK状态。   
  （4）第四次挥手：Client收到FIN后，Client进入TIME\_WAIT状态，接着发送一个ACK=1给Server，Ack=w+1,Seq=u+1，Server进入CLOSED状态，完成四次挥手。

###   
为什么建立连接是三次而断开连接是四次呢？

  
这是因为服务端的LISTEN状态下的SOCKET当收到SYN报文的建连请求后，它可以把ACK和SYN（ACK起应答作用，而SYN起同步作用）放在一个报文里来发送。但关闭连接时，当收到对方的FIN报文通知时，它仅仅表示对方没有数据发送给你了；但未必你所有的数据都全部发送给对方了，所以你未必可以马上关闭SOCKET，也许你还需要发送一些数据给对方之后，再发送FIN报文给对方来表示你同意现在可以关闭连接了，所以它这里的ACK报文和FIN报文多数情况下都是分开发送的

TCP和UDP的区别
----------

1.    基于连接与无连接
2.    TCP要求系统资源较多，UDP较少； 
3.    UDP程序结构较简单 
4.    流模式（TCP）与数据报模式(UDP); 
5.    TCP保证数据正确性，UDP可能丢包 
6.    TCP保证数据顺序，UDP不保证 

 TCP数据包的封装
----------

![](https://img-blog.csdnimg.cn/20181030082313815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

TCP段首部的定长部分为20个字节，即5个单位的长度

![](https://img-blog.csdnimg.cn/20181030082327812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 UDP数据包封装
---------

![](https://img-blog.csdnimg.cn/20181030082416894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181030082429535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SCTP
----

SCTP（Stream Control Transmission Protocol，流控制传输协议）是IETF（Internet Engineering Task Force，因特网工程任务组）在2000年定义的一个传输层（Transport Layer）协议，是提供基于不可靠传输业务的协议之上的可靠的数据报传输协议。SCTP的设计用于通过IP网传输SCN（Signaling Communication Network，信令通信网）窄带信令消息。后期广泛用于EPC网络中的S6a/S1/Sgs/Sv等接口中。  
SCTP是一个面向连接的流传输协议，它可以在两个端点之间提供稳定、有序的数据传递服务。SCTP可以看做是TCP协议的改进，它继承了TCP较为完善的拥塞控制并改进TCP的一些不足之处

SYN Flood泛洪攻击
-------------

SYN Flood是当前最流行的DoS(拒绝服务攻击)之一，SYN攻击利用的是TCP的三次握手机制的缺陷，攻击端利用伪造的IP地址向被攻击端发出SYN请求，而被攻击端发出的ACK响应报文将永远发送不到目的地，也就是得不到回应，那么这个TCP连接就处于挂起状态，也就是所谓的半连接状态。服务器收不到确认的话，在**SYN timeout**时间内(一般为30秒-2分钟)还会重复发送ACK报文给攻击者，直到SYN timeout超时。如果有成千上万的这种连接，被攻击的主机资源将被耗尽，从而达到攻击的目的。

**防御SYN Flood攻击**

*   降低SYN timeout时间，使得主机尽快释放半连接的占用
*   采用SYN cookie设置，如果短时间内连续收到某个IP的重复SYN请求，则认为受到了该IP的攻击，丢弃来自该IP的后续请求报文
*   合理地采用防火墙等外部网络安全设施