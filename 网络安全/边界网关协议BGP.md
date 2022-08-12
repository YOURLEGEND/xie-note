**目录**

[BGP](#t0)

[BGP的邻居​](#t1)

[BGP路由的注入方式](#t2)

[BGP的通告原则和选路原则](#t3)

[BGP的属性](#t4)

[BGP的防环机制](#t5)

[BGP的同步](#t6)

[BGP的负载均衡](#t7)

[路由反射器](#t8)

[华为模拟器中的设置](#t9) 

* * *

BGP
===

BGP(Border Gateway Protocol)边界网关协议是运行于 TCP 上的一种自治系统的路由协议。 BGP 是唯一一个用来处理像因特网大小的网络的协议，也是唯一能够妥善处理好不相关路由域间的多路连接的协议。 BGP 构建在 EGP 的经验之上。 BGP 系统的主要功能是和其他的 BGP 系统交换网络可达信息。网络可达信息包括列出的自治系统（AS）的信息。这些信息有效地构造了 AS 互联的拓朴图并由此清除了路由环路，同时在 AS 级别上可实施策略决策。  
BGP属于EGP(外部网关路由协议)，可以实现自治系统间无环路的域间路由。BGP是沟通Internet广域网的主用路由协议，例如不同省份、不同国家之间的路由大多要依靠BGP协议。BGP可分为IBGP（Internal BGP）和EBGP（External BGP）。BGP的邻居关系（或称通信对端/对等实体）是通过人工配置实现的，对等实体之间通过TCP（端口179)会话交互数据。BGP路由器会周期地发送19字节的保持存活keep-alive消息来维护连接（默认周期为30秒）。在路由协议中，只有BGP使用TCP作为传输层协议

![](https://img-blog.csdnimg.cn/20181122082932806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

BGP的邻居
------

![](https://img-blog.csdnimg.cn/20181122083100913.png)
------------------------------------------------------

AR4与AR3的邻居关系是IBGP(Internal BGP)  
AR3与AR2的邻居关系是EBGP(External BGP)

![](https://img-blog.csdnimg.cn/20181122083117198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

完成TCP三次握手后的状态是Connect，发送完Open报文的状态是Opensen，收到对方的Open报文的状态是OpenComfirm，收到对方的Keepalive报文的状态是Established。建立好了邻居关系之后，通过发送Update报文更新路由信息  
![](https://img-blog.csdnimg.cn/20181122083135102.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**BGP邻居关系的配置**

```
IBGP关系(邻居设置为对方的环回口地址)      
[AR4]bgp 200       
[AR4-bgp] router-id  44.4.4.4               
[AR4-bgp]peer 33.3.3.3 as-number 200      
[AR4-bgp]peer 33.3.3.3 connect-interface lo0      
[AR3]bgp 200      
[AR3-bgp] router-id  33.3.3.3      
[AR3-bgp]peer 44.4.4.4 as-number 200      
[AR3-bgp]peer 44.4.4.4 connect-interface LoopBack 0
```

```
EBGP关系(邻居设置为与对方的接口ip)      
[AR3]bgp 200      
[AR3-bgp]peer 23.1.1.2 as-number 100      
[AR2]bgp 100      
[AR2-bgp]router-id 22.2.2.2      
[AR2-bgp]peer 23.1.1.3 as-number 200
```


![](https://img-blog.csdnimg.cn/20181122083236818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

BGP路由的注入方式
----------

![](https://img-blog.csdnimg.cn/2018112208340017.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**1.Network**  
Network命令是逐条将IP路由表中已经存在的路由引入到BGP路由表中

```
[AR3]bgp 200      
[AR3-bgp]ipv4-family unicast       
[AR3-bgp-af-ipv4]network 44.4.4.4 32
```


**2.Import**  
import注入是用前缀列表匹配出路由条目，然后注入路由策略

```
[AR3]ip ip-prefix F   index 10   permit  144.1.3.1 32              
[AR3]route-policy  F2  permit  node 10      
[AR3-route-policy]if-match  ip-prefix  F      
[AR3]bgp 200      
[AR3-bgp]ipv4-family unicast       
[AR3-bgp-af-ipv4]import-route rip 1 route-policy F2
```


**3.aggregate(路由聚合)**  
aggregate是将本地的收到路由条目的明细做一个汇总

R1把三台路由条目注入bgp中

```
[AR1]bgp 100      
[AR1-bgp]ipv4-family unicast       
[AR1-bgp-af-ipv4]network 111.1.1.1 32      
[AR1-bgp-af-ipv4]network 111.1.2.1 32      
[AR1-bgp-af-ipv4]network 111.1.3.1 32
```


在R2中的bgp routing-table中会显示三条路由条目

![](https://img-blog.csdnimg.cn/2018112208350281.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后进入R2中的bgp中，对以上路由条目做汇总

```
[AR2]bgp 100      
[AR2-bgp]ipv4-family unicast      
[AR2-bgp-af-ipv4]aggregate 111.1.0.0 22  detail-suppressed (抑制明细)
```


![](https://img-blog.csdnimg.cn/20181122083511731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181122083520135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
 

BGP的通告原则和选路原则
-------------

**BGP路由通告原则**

*   BGP只会将本地最优的BGP路由通告给自己的BGP对等体(peer)
*   BGP只会将本地最优的BGP路由加到本地路由表中作为转发依据
*   IBGP通告获得的最优路由不会发布给其他的IBGP邻居
*   EBGP通告获得的最优路由发布给所有BGP邻居
*   BGP与IGP同步

   
**BGP路由优选原则**

*   BGP路由器将路由通告给邻居后，每个BGP邻居都会进行路由优选，路由选择有三种情况：
*   该路由是到达目的地的唯一路由，直接优选。
*   对到达同一目的地的多条路由，优选优先级最高的。
*   对到达同一目的地且具有相同优先级的多条路由，必须用更细的原则去选择一条最优的。
*   一般来说，BGP计算路由优先级的规则如下：
*   丢弃下一跳不可达的路由。
*   优选Preference\_Value值最高的路由（私有属性，仅本地有效）。
*   优选本地优先级（Local\_Preference）最高的路由。
*   优选手动聚合>自动聚合>network>import>从对等体学到的。
*   优选AS\_Path短的路由。
*   起源类型IGP>EGP>Incomplete(i > e > ?)。
*   对于来自同一AS的路由，优选MED值小的。
*   优选从EBGP学来的路由（EBGP>IBGP）。
*   优选AS内部IGP的Metric最小的路由。
*   优选Cluster\_List最短的路由。
*   优选Orginator\_ID最小的路由。
*   优选Router\_ID最小的路由器发布的路由。
*   优选具有较小IP地址的邻居学来的路由

注：Orginator ID 和 Router ID 只在路由反射器中存在

BGP的属性
------

![](https://img-blog.csdnimg.cn/20181122083824364.png)
------------------------------------------------------

公认必遵( Origin , AS\_path , next\_hop)  
origin：起源代码，有三个  i-IGP    e-EGP     ?- incomplete。  
 i 表明是network进来的  
e 表明是EGP学过来的路由，EGP现在已经不用了，所以基本看不到e，  
? 表明是import进来的  
AS\_path：AS路径，别的AS发过来的话就有，本AS发过来的话就是空,AS路径会叠加

![](https://img-blog.csdnimg.cn/20181122083906946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

   
next\_hop：下一跳，0.0.0.0表示本地就存在的路由(可以是直连的，静态的，rip或者ospf学到的)，然后network出去的，127.0.0.1的路由是基于明细产生的。

![](https://img-blog.csdnimg.cn/2018112208391963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下一跳属性在被EBGP邻居传过去后会被自动修改成EBGP接口地址。在默认情况下，IBGP邻居之间传递EBGP路由时，下一跳属性不发生变化，除非手动修改，比如设置默认下一跳  
比如，AR3宣告33.3.3.3的路由，在R1上收到的BGP信息是这样的

![](https://img-blog.csdnimg.cn/20181122083927482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可是如果在R2上指定peer 11.1.1.1 的下一跳是本地的话，下一跳地址的话，就会发生变化了，那么下一跳地址就是R2了  
\[AR2-bgp\] peer 11.1.1.1 next-hop-local  
![](https://img-blog.csdnimg.cn/20181122083935802.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
公认任意(Local Preference、Atomic\_aggregate) ：  
Local Preference  
本地优先级，默认值为100，数值越大，BGP路由被优选的可能性越高，但是这个属性不能跨AS传递。比如这里R2给R3传路由的时候，LocPrf这个属性不传给R3。R3重新给这个路由赋予本地优先级。本地优先级可以影响本地路由传递出去的方向

![](https://img-blog.csdnimg.cn/20181122083947720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

比如，R1通告了一条路由，在R1上这条路由没有本地优先级，然后R1传给R2，R1赋予这条路由本地优先级为100，所以在R2上可以看到这条路由的本地优先级为100。R2传给R3，跨AS传递，本地优先级不传递，所以在R3上看不到本地优先级，R3传给R4，R3重新赋予这条路由本地优先级为100，所以在R4上看到这条路由的本地优先级还是100  
   
Atomic\_aggregate 原子聚合：  
这个属性代表的是这条BGP路由是曾经被聚合而产生的。要抑制路由明细了才会显示原子聚合

![](https://img-blog.csdnimg.cn/20181122083957970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
可选过渡(Community )意思就是这个属性可以选择支持也可以选择不支持  
   
可选非过渡(MED)，类似于BGP的cost值，数值越小，优先级越高，也是影响流量转发方向。当把IGP路由导入BGP中时，如果IGP本身就有cost值，那么BGP中就会继承成MED值。MED属性仅在相邻两个AS之间传递，收到此属性的AS不会再将其通告给任何其他第三方AS，用于判断流量进入AS时的最佳路由

![](https://img-blog.csdnimg.cn/20181122084009300.png)  
**修改BGP的属性值**  
   
第一步: 用前缀列表匹配出该路由条路  
ip ip-prefix  NAME1  permit   xx.xx.xx.xx  0-32  
第二步: 设置路由策略，匹配该路由，给该路由施加策略  
route-policy  NAME2  permit  node  10  
ifmatch  ip-prefix   NAME1  
apply   XXXXX  
第三步: 调用到进程下 (BGP / ospf / RIP)  
peer  xx.xx.xx.xx   route-policy  F2  import/export  
   
例如修改属性

```
[AR7]ip ip-prefix F permit 144.1.1.1 32      
[AR7]route-policy F2 permit node 10      
[AR7-route-policy]if-match ip-prefix F      
[AR7-route-policy]apply preferred-value 1      
                            apply local-preference 110      
                            apply as-path 100 100 additive      
                            apply cost 1 (修改Med值)      
[AR7-bgp]ipv4-family unicast      
[AR7-bgp-af-ipv4]peer 27.1.1.2 route-policy F2 import
```


BGP的防环机制
--------

**BGP的防环机制主要有2条：**  
第一是AS内部防环：  
通过IBGP水平分割来实现的，IBGP水平分割的基本思想是不把从IBGP邻居学到的路由传递给其他IBGP邻居，但是这样也有一个弊端，就是AS内部的所有路由器都要两两之间建立邻居关系(full-mash)，如果有N个路由器，那么就要建立N(N-1)/2个邻居  
   
第二是AS间的防环：  
通过属性AS-PATH来实现，基本思想是：如果某台BGP路由器从其外部对等体收到某条路由的AS\_PATH中包含有自己的AS号那么该路由器就知道出现了环路，因而丢弃该路由

BGP的同步
------

**1.BGP同步规则的定义:**  
在bgp同步打开的情况下,一个BGP路由器不会把那些通过ibgp邻居学到的bgp路由通告给自己的ebgp邻居;除非自己的igb路由表中存在这些路由,才可以向ebgp路由器通告.  
**2.BGP同步规则的目的:**  
防止一个AS(不是所有的路由器都运行bgp)内部出现路由黑洞,即向外部通告了一个本AS不可达的虚假的路由.  
**3.BGP同步规则的基本需求**  
如果一个AS内部存在非bgp路由器,那么就出现了bgp和igp的边界,需要在边界路由器将bgp路由发布到igp中,才能保证AS所通告到外部的bgp路由在AS内部是连通的.实际上是要求bgp路由和igp路由的同步.  
**4.满足BGP同步规则的基本需求的结果**  
如果将bgp路由发布到igp中,由于bgp路由主要是来自AS外部的路由(来自internet),那么结果是igp路由器要维护数以万计的外部路由,对路由器的cpu和memeory以及AS内部的链路带宽的占用将带来巨大的开销.  
   
**BGP同步规则的总结**  
1.在所有的方案中, 既要保证传递bgp路由,还要保证bgp路由的连通性.  
2.关闭同步能够实现bgp路由的传递,不一定能保证AS内部连通性,除非AS内所有路由器都运行bgp才可以保证连通性;否则,仍然需要路由再发布(bgpàigp)  
3.最后,在AS内部一般需要部署igp来维持AS内部网络路径的连通性,以保证AS内部的所通告的bgp路由的下一跳的可达性.这样bgp网络就具有更好的灵活性和扩展性.  
4\. BGP同步默认是开启的，可以手工关闭它，但是只有当一个AS内部的路由器都运行了BGP时，关闭同步功能才是安全的。

BGP的负载均衡
--------

![](https://img-blog.csdnimg.cn/20181122084220932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

在IBGP中设置[负载均衡](https://so.csdn.net/so/search?q=%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1&spm=1001.2101.3001.7020)  
如果，AR1 network进BGP里面 111.1.1.1  32的路由，传到AR2，AR2-->AR3-->AR5、AR2-->AR4-->AR5、这条路由到达AR5有两条路径，而不配置负载均衡的情况下，AR5会选出一条路由作为最优路由。现在在AR5上配置IBGP里面的负载均衡  
\[AR5-bgp\] ipv4-family unicast   
\[AR5bgp-af-ipv4\] maximum load-balancing  ibgp  

![](https://img-blog.csdnimg.cn/20181122084248377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这是没配置负载均衡的路由表

![](https://img-blog.csdnimg.cn/20181122084302593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 这是配置了负载均衡的路由表

![](https://img-blog.csdnimg.cn/2018112208432097.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

在EBGP中设置负载均衡  
\[AR2-bgp\] ipv4-family unicast   
\[AR2-bgp-af-ipv4\] maximum load-balancing  ebgp 2

路由反射器
-----

在这个图中，R2、R4、R5是reflect-client  , R3是RR，R6是普通的IBGP邻居

![](https://img-blog.csdnimg.cn/2018112208435717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
RR收到R2发的IBGP路由会将该路由[反射](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%B0%84&spm=1001.2101.3001.7020)给RR的其他client(R4、R5)，这就是路由反射器，还会传递该路由给RR的其他IBGP对等体(R6)。路由反射器打破了水平分隔原则(即收到的IBGP通告不会发给其他的IBGP邻居这个原则)  
   
路由反射器防环  
那么，路由反射器打破了水平分隔原则后怎么防止环路呢？如下图，可以设立两个RR。  
R2、R4、R5是reflect-client ，R3、R6是RR，R7、R8是普通IBGP邻居  
![](https://img-blog.csdnimg.cn/20181122084434973.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

防环一：  
当R2给R6传递IBGP路由的时候，R2会在路由中加入自己的router-id作为起源ID，传给R6，这个起源ID一直存在于该路由中。这样当R3给R2反传该路由时，R2发现这个起源ID就是自己，所以对该路由拒收。  
防环二：  
R6给R3传递IDGP路由的时候，会在路由中加入自己的router-id作为cluster-id传递给R3，在一个簇内，RR应该具有相同的cluster-id，即R3和R6应该具有相同的cluster-id，这样当R3收到R6传给自己的路由信息中的cluster-id和自己的一样，则拒收。cluster-id需要手工指定，如果不手工指定，默认等于其router-id。  
   
在RR上配置路由反射器客户端:  
\[AR3\] bgp 200  
\[AR3-bgp\] ipv4-family unicast  
\[AR3-bgp-af-ipv4\] peer  22.2.2.2  reflect-client  
   
修改簇ID：  
\[AR3\] bgp 200  
\[AR3-bgp-af-ipv4\] reflector cluster-id 111.111.111.111  
![](https://img-blog.csdnimg.cn/20181122084451281.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

华为模拟器中的设置 
----------

```
查看bgp邻居：  display   bgp   peer      
查看bgp表： dis  bgp   routing-table      
给IGP邻居宣告下一跳本地：  peer   XX.XX.XX.XX   next-hop-local      
给EGP邻居宣告默认路由: peer  XX.XX.XX.XX   default-route-advertise
```
