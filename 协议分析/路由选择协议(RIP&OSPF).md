**目录**

[IGP](#t0)

[RIP协议](#t1)

[OSPF协议](#t2)

[IS-IS协议](#t3)

[EIGRP协议](#t4)

[EGP](#t5)

[BGP](#t6)

* * *

我们可能会想，在偌大的网络中，我们是如何跟其他人通信的呢？我们是如何跟远在太平洋对面的美国小伙伴对话的呢？

这就要多亏了我们的路由选择协议了。路由协议通过一层一层路由把我们的消息发送给对方。

在讲路由协议之前，我们还需要了解这么一个概念——自治系统AS

自治系统(AS)：由同一个管理机构管理，使用统一路由策略的路由器的集合。

所以由自治系统AS可以引出两种协议： **IGP** 和 **EGP** 。IGP是负责自治系统 AS 内部通信的路由协议，而 EGP 是负责自治系统AS 间通信的路由协议

![](https://img-blog.csdnimg.cn/20181121080614680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IGP
---

**IGP**（Interior Gateway Protocol，内部网关协议）是在一个自治网络内网关（主机和路由器）间交换路由信息的协议。路由信息能用于网间协议（IP）或者其它网络协议来说明路由传送是如何进行的。Internet网被分成多个域或多个自治系统。一个域（domain）是一组主机和使用相同路由选择协议的路由器集合，并由单一机构管理。IGP协议包括 RIP、[OSPF](https://so.csdn.net/so/search?q=OSPF&spm=1001.2101.3001.7020)、IS-IS、IGRP、EIGRP（思科私有协议）等。

IGP的选路原则：

*   优先级
*   开销
*   负载均衡

### RIP协议

**RIP协议**是一种内部网关协议（IGP），底层是贝尔曼福特算法，是一种动态路由选择协议，用于自治系统（AS）内的路由信息的传递。[RIP协议](https://so.csdn.net/so/search?q=RIP%E5%8D%8F%E8%AE%AE&spm=1001.2101.3001.7020)基于距离矢量算法（DistanceVectorAlgorithms），使用“跳数”(即metric)来衡量到达目标地址的路由距离。这种协议的路由器只关心自己周围的世界，只与自己相邻的路由器交换信息，范围限制在**15跳**(15度)之内，再远，它就不关心了。所以rip只适用于小区域。Rip协议运行在 **UDP** 协议之上，使用 **520** 端口，使用 **224.0.0.9** 作为组播地址，向外组播路由信息

**工作原理**：RIP通过广播UDP报文来交换路由信息，每30秒发送一次路由信息更新。RIP提供跳跃计数(hopcount)作为尺度来衡量路由距离，跳跃计数是一个包到达目标所必须经过的路由器的数目。如果到相同目标有二个不等速或不同带宽的路由器，但跳跃计数相同，则RIP认为两个路由是等距离的。RIP最多支持的跳数为15，即在源和目的网间所要经过的最多路由器的数目为15，跳数16表示不可达。

相关文章：[路由协议之RIP](https://blog.csdn.net/qq_36119192/article/details/84312897)

### OSPF协议

**OSPF**(Open Shortest Path First开放式最短路径优先）是一个内部网关协议IGP，用于在单一自治系统内决策路由。是对链路状态路由协议的一种实现，隶属内部网关协议（IGP），故运作于自治系统内部。SPF算法用作生成最短生成树。OSPF分为OSPFv2和OSPFv3两个版本,其中OSPFv2用在IPv4网络，OSPFv3用在IPv6网络。OSPFv2是由RFC 2328定义的，OSPFv3是由RFC 5340定义的。与RIP相比，OSPF是链路状态协议，而RIP是距离矢量协议，它选择路由的度量标准是带宽，延迟。适合在大中区域，目前最流行的路由协议。Rip协议使用 **224.0.0.5** 作为组播地址，向外组播路由信息。DR监听 **224.0.0.6** 端口来获取从DOther发来的LSA信息，ospf协议号 **89**  
  
相关文章： [路由协议之OSPF](https://blog.csdn.net/qq_36119192/article/details/84312903)                                                                                                                                                   
 

### IS-IS协议

**IS-IS**（Intermediate System-to-Intermediate System，中间系统到中间系统）路由协议最初是ISO（the International Organization for Standardization，国际标准化组织）为CLNP（Connection Less Network Protocol，无连接网络协议）设计的一种动态路由协议

### EIGRP协议

**EIGRP协议**：它是思科私有路由协议，高级距离矢量路由协义，适用大中型网络  
   
Egrip路由协议（路由器之间相互学习网段）：   
router eigrp 10  
network  网段   反子网掩码

EGP
---

**EGP**（Exterior Gateway Protocol，外部网关协议）是AS之间使用的路由协议,其目前只有一个协议--BGP协议

### BGP

**BGP**(Border Gateway Protocol)边界网关协议是运行于 TCP 上的一种自治系统的路由协议。 BGP 是唯一一个用来处理像因特网大小的网络的协议，也是唯一能够妥善处理好不相关路由域间的多路连接的协议。 BGP 构建在 EGP 的经验之上。 BGP 系统的主要功能是和其他的 BGP 系统交换网络可达信息。网络可达信息包括列出的自治系统（AS）的信息。这些信息有效地构造了 AS 互联的拓朴图并由此清除了路由环路，同时在 AS 级别上可实施策略决策。  
BGP属于EGP(外部网关路由协议)，可以实现自治系统间无环路的域间路由。BGP是沟通Internet广域网的主用路由协议，例如不同省份、不同国家之间的路由大多要依靠BGP协议。BGP可分为IBGP（Internal BGP）和EBGP（External BGP）。BGP的邻居关系（或称通信对端/对等实体）是通过人工配置实现的，对等实体之间通过TCP（端口179)会话交互数据。BGP路由器会周期地发送19字节的保持存活keep-alive消息来维护连接（默认周期为30秒）。在路由协议中，只有BGP使用TCP作为传输层协议