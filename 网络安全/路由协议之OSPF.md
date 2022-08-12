**目录**

[OSPF协议](#t0)

[OSPF的七种状态](#t1)

[OSPF的11种LSA](#t2)

[Stub和Nssa](#t3)

[OSPF中的防环机制](#t4)

[OSPF中的路由汇总和路由过滤](#t5)

[OSPF中的虚拟链路](#t6)

[虚拟链路有两种存在的意义](#t7)

[OSPF中的认证](#t8)

[华为模拟器中的配置](#t9)

* * *

OSPF协议
======

**OSPF**(Open Shortest Path First开放式[最短路径](https://so.csdn.net/so/search?q=%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84&spm=1001.2101.3001.7020)优先）是一个内部网关协议IGP，用于在单一自治系统内决策路由。是对链路状态路由协议的一种实现，隶属内部网关协议（IGP），故运作于自治系统内部。SPF算法用作生成最短生成树。OSPF分为OSPFv2和OSPFv3两个版本,其中OSPFv2用在IPv4网络，OSPFv3用在IPv6网络。OSPFv2是由RFC 2328定义的，OSPFv3是由RFC 5340定义的。与RIP相比，OSPF是链路状态协议，而RIP是距离矢量协议，它选择路由的度量标准是带宽，延迟。适合在大中区域，目前最流行的路由协议。Rip协议使用 **224.0.0.5** 作为组播地址，向外组播路由信息。DR监听 **224.0.0.6** 端口来获取从DOther发来的LSA信息，[ospf](https://so.csdn.net/so/search?q=ospf&spm=1001.2101.3001.7020)协议号 **89**  
Router-ID  
每一台OSPF路由器只有一个Router-ID，Router-ID使用IP地址的形式来表示，确定Router-ID的方法为：

1.  手工指定Router-ID。
2.  路由器上活动Loopback接口中IP地址最大的，也就是数字最大的，如C类地址优先于B类地址，一个非活动的接口的IP地址是不能被选为Router-ID的。
3.  如果没有活动的Loopback接口，则选择活动物理接口IP地址最大的。

注：如果一台路由器收到一条链路状态，无法到达该Router-ID的位置，就无法到达链路状态中的目标网络。Router-ID只在OSPF启动时计算，或者重置OSPF进程后计算。                                                                                                                                                            
**COST值**  
OSPF使用接口的带宽来计算Metric值，例如一个10 Mbit/s的接口，计算Cost的方法为：  
将10 Mbit换算成 bit，为10 000 000 bit，然后用10000 0000除以该带宽，结果为 10000 0000/10 000 000 bit = 10，所以一个10 Mbit/s的接口，OSPF认为该接口的Metric值为10，需要注意的是，计算中，带宽的单位取bit/s，而不是Kbit/s，例如一个100 Mbit/s的接口，Cost 值为 10000 0000 /100 000 000=1，因为Cost值必须为整数，所以即使是一个1000 Mbit/s（1GBbit/s）的接口，Cost值和100Mbit/s一样，为1。如果路由器要经过两个接口才能到达目标网络，那么很显然，两个接口的Cost值要累加起来，才算是到达目标网络的Metric值，所以OSPF路由器计算到达目标网络的Metric值，必须将沿途中所有接口的Cost值累加起来，在累加时，同EIGRP一样，只计算出接口，不计算进接口。  
OSPF会自动计算接口上的Cost值，但也可以通过手工指定该接口的Cost值，手工指定的优先于自动计算的值。  
OSPF计算的Cost，同样是和接口带宽成反比，带宽越高，Cost值越小。到达目标相同Cost值的路径，可以执行负载均衡，最多6条链路同时执行负载均衡。  
**链路状态（Link-State）**  
链路状态通告（LSA）就是通告OSPF接口上的描述信息，例如接口上的IP地址，子网掩码，网络类型，Cost值等等，OSPF路由器之间交换的并不是路由表，而是链路状态通告（LSA），OSPF通过获得网络中所有的链路状态信息，从而计算出到达每个目标精确的网络路径。OSPF路由器会将自己所有的链路状态毫不保留地全部发给邻接，邻接将收到的链路状态全部放入链路状态数据库（Link-State Database），邻接再发给自己的所有邻接，并且在传递过程中，绝对不会有任何更改。通过这样的过程，最终，网络中所有的OSPF路由器都拥有网络中所有的链路状态，并且所有路由器的链路状态应该能描绘出相同的网络拓朴.  
注：邻居是指路由器的接口处在同一网段，而邻接是指互相进行LSA交换的路由器，所以邻居不一定是邻接。一般情况下，邻居也是邻接，下面这种特殊情况除外。

也是邻接，下面这种特殊情况除外。

![](https://img-blog.csdnimg.cn/20181121081811711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181121081830317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**DR和BDR的作用：**   
    能够减少邻接关系的数量，从而减少链路状态信息以及路由信息的交换次数，这样可以节省带宽，减少路由器硬件的负担。一个既不是DR也不是BDR的路由器只与DR和BDR形成邻接关系并交换链路状态信息以及路由信息，这样就大大减少了大型广播型网络和NBMA网络中的邻接关系数量。 

![](https://img-blog.csdnimg.cn/20181121081900995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

DR和BDR是由OSPF的Hello报文进行选举的，选举是根据端口的路由优先级（Router Priority）进行的。  
如果Router Priority被设置为0，那么该路由器将不允许被选举成DR或者BDR。  
Router Priority越大越优先（这和STP中是相反的）。如果相同，Router ID大者优先。  
为了维护网络上邻接关系的稳定性，如果网络中已经存在DR和BDR，则新添加进该网段的路由器不会成为DR和BDR，不管该路由器的Router Priority是否最大。   
如果当前DR故障，当前BDR自动成为新的DR，网络中重新选举BDR，如果当前BDR故障，则DR不变，重新选举BDR。  
这种选举机制的目的是为了保持邻接关系的稳定，减少拓扑结构的改变对邻接关系的影响。

![](https://img-blog.csdnimg.cn/20181121081932670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

ospf dr-priority value：修改端口的Router Priority。   
Router Priority的取值范围是0~255，默认值为1；   
如果两台路由器的Router Priority值相同，则比较Router ID，Router ID大的更优先。   
如果修改了Router Priority，需要重启ospf进程才能重新参与选举DR和BDR

**OSPF区域**  
因为OSPF路由器之间会将所有的链路状态（LSA）相互交换，毫不保留，当网络规模达到一定程度时，LSA将形成一个庞大的数据库，势必会给OSPF计算带来巨大的压力；为了能够降低OSPF计算的复杂程度，缓存计算压力，OSPF采用分区域计算，将网络中所有OSPF路由器划分成不同的区域，每个区域负责各自区域精确的LSA传递与路由计算，然后再将一个区域的LSA简化和汇总之后转发到另外一个区域，这样一来，在区域内部，拥有网络精确的LSA，而在不同区域，则传递简化的LSA。区域的划分为了能够尽量设计成无环网络，所以采用了Hub-Spoke的拓朴架构，也就是采用核心与分支的拓朴。  
区域的命名可以采用整数数字，如1、2、3、4，也可以采用IP地址的形式，0.0.0.1、0.0.0.2，因为采用了Hub-Spoke的架构，所以必须定义出一个核心，然后其它部分都与核心相连，OSPF的区域0就是所有区域的核心，称为BackBone 区域（骨干区域），而其它区域称为Normal 区域（常规区域），在理论上，所有的常规区域应该直接和骨干区域相连，常规区域只能和骨干区域交换LSA，常规区域与常规区域之间即使直连也无法互换LSA，如图中Area 1、Area 2、Area 3、Area 4只能和Area 0互换LSA，然后再由Area 0转发，Area 0就像是一个中转站，两个常规区域需要交换LSA，只能先交给Area 0，再由Area 0转发，而常规区域之间无法互相转发。  
OSPF区域是基于路由器的接口划分的，而不是基于整台路由器划分的，一台路由器可以属于单个区域，也可以属于多个区域，如下图：  
如果一台OSPF路由器属于单个区域，即该路由器所有接口都属于同一个区域，那么这台路由器称为Internal Router（IR），如下图中的R2，R3和R4；如果一台OSPF路由器属于多个区域，即该路由器的接口不都属于一个区域，那么这台路由器称为Area Border Router （ABR），如下图中的R1，ABR可以将一个区域的LSA汇总后转发至另一个区域；如果一台OSPF路由器将外部路由协议重分布进OSPF，那么这台路由器称为Autonomous System Boundary Router （ASBR），如下图中，R5将EIGRP重分布进OSPF，那么R5就是ASBR，但是如果只是将OSPF重分布进其它路由协议，则不能称为ASBR。

![](https://img-blog.csdnimg.cn/20181121082024567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181121082046528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181121082058706.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181121082112499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181121082124744.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

OSPF中存在三张表：

*   邻居表(所有已知的邻居的数据库)
*   拓扑表(同一个area内的设备的拓扑信息)
*   路由表

**问题：**   
1\. 邻居关系和邻接关系有什么区别？   
答：只要右端口连接到同一个网段的两个路由器就可以形成邻居关系，邻接关系是指可以交换链路状态信息以及路由信息的邻居关系，只有部分邻居关系可以形成邻接关系。   
2\. OSPF支持的网络类型有哪些：   
答：点到点网络，点到多点网络，广播型网络，非广播多路访问网络。   
3\. 什么是DR和BDR？   
答：DR是广播型网段或者NBMA网段上的指定路由器，用于和其它路由器形成邻接关系，交换路由信息。   
BDR是广播型网段或者NBMA网段上的备份指定路由器，用于和DR以及其它路由器形成邻接关系，交换路由信息。作为DR的备份路由器，当DR失效时，BDR将自动成为DR。   
4\. Router Priority最大的一定是DR吗？   
答：不一定，为了保持邻接关系的稳定性，拓扑结构的改变（不涉及当前DR和BDR）不会引起DR和BDR的重新选举。   
5\. 配置虚连接的时候如何表示对端路由器？   
答：使用对端路由器的Router ID表示。  
6\. OSPF中多久更新一次LSA,多久老化LSA  
答：1800秒周期性更新LSA，超过1800秒没收到LSA的话，3600秒在LSA表中老化起LSA

OSPF的七种状态
---------

**Down**：接口没有启用OSPF的状态，邻居失效后变为该状态  
**Init(initial)**：初始化状态，发送完Hello包，状态就从Down变为init  
**2-way**：接收到了对方的hello包之后，从init变为2-way邻居状态。开启40秒定时器，相互间周期发送hello报文(10秒发一次)，给足够的时间深度发现其他路由器发送的Hello包，等待40秒定时器超时  
**Exstart**：定时器40秒超时后，转态变为Exstart转态，但是在变为Exstart转态前的短暂时间内，会完成DR和BDR的选举（2-way和Exstart转态之间选举好）。进入Exstart转态后，发送初始DBD报文（不包含本地的LSA的摘要信息）选举主从路由器，引导接下来报文的发送。  
**Exchange**：主从关系确认后，开始交换DBD报文（本地的LSA摘要信息），该状态下，相互间发送DBD，告知对端本地所有的LSA的摘要信息  
**Loading**：发送完LSA摘要信息后，再通过发送LSR,LSU,LSACK报文更新LSA，专门学习对端的LSA的详细信息  
**Full**：收到了对方的LSACK报文后，状态变为Full邻接状态（学习完的状态），彼此的LSDB同步，即所有的LSA相同

OSPF的11种LSA
-----------

一共有11种LSA，1、2、3 、4、5、7的属于ospfV2版本的  
LSA三要素：  
由谁通告的  
传播范围  
承载的是什么信息（路由信息或链路拓扑信息）  
   
 LS id:   LSA的id，没有特别的含义，一般是目标ip或者目标网段  
Adv rtr：产生此LSA的路由器的 router-id  
**type-1类LSA(Router)：**  
1：每一个运行ospf的节点，都会向外发送type1类的LSA  
2：只在本area内传递  
3：既有路由信息，也有链路拓扑信息  
注：type-1类报文包含area中的每个路由器的每个接口的邻接DR的接口(Series接口除外，Series没有DR)的拓扑信息和本地回环地址的路由信息，如果是Series接口的话，则包含这条线上的链路信息和路由信息(这条Series邻接的路由器的router-id的邻接口的ip，还有就是这条Series线路的路由信息)  
![](https://img-blog.csdnimg.cn/20181122081349664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**type-2类LSA(Network)：**  
1：由每个MA环境下的DR通告  
2：只在本area内传递  
3：既有路由信息，也有链路拓扑信息  
注：type2类LSA要和type1类LSA配合才能生效

![](https://img-blog.csdnimg.cn/20181122081456499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**type-3类LSA(Summary-net)：**  
1：由ABR通告，向area外通告(ABR完成由type1转type3的工作)  
2：除始发area外的所有area  
3：只有路由信息  
注：Adv router-id 是会变的，是本区域的ABR

![](https://img-blog.csdnimg.cn/20181122081531482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**type-4类LSA（ Sum-Asbr）：**  
1：由ABR通告  
2：除ASBR所在的area，其他area都有  
3：到达ASBR的开销，用来累加ospf内部cost  
注：type-4类是给type-5类辅助完成cost值的累加  
![](https://img-blog.csdnimg.cn/2018112208155888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**type-5类LSA（External）：**  
1：由ASBR通告  
2：所有ospf的area  
3：只有路由信息  
注：type-5类LSA没有区域隶属关系  
![](https://img-blog.csdnimg.cn/20181122081712466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

E type 是外部路由类型，当是类型2时，则外部路由传到ospf内部，ospf都不对此路由做cost值的累加，当是类型1时，在ospf内部会对此路由做cost累加  
E type默认是2，将E  type手动修改为类型1，在ASBR上的ospf进程内改，import-route rip 1 route-policy XIE type 1

**type-7类LSA：**  
1：由ASBR通告  
2：只存在nssa区域，由nssa区域的ABR完成7类转5类  
3：只有路由信息  
![](https://img-blog.csdnimg.cn/20181122081742360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**LSA总结**

![](https://img-blog.csdnimg.cn/20181122081755225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Stub和Nssa
---------

OSPF的四种特殊区域

1.  Stub 末节区
2.  T-Stub (totally-Stub) 完全末节区
3.  Nssa 不完全末节区
4.  T-nssa (totally-Nssa)

*   Stub区不接收来自区域外的四类和五类LSA
*   T-Stub区不接收来自区域外的三类、四类和五类LSA
*   Nssa区的ABR不接收来自区域外的四类和五类LSA，ASBR接受来自区域外的七类LSA，并且转化为五类LSA传到其他区域，七类LSA只存在于Nssa区中，此时T-Nssa中的ABR则成了假的ASBR 
*   T-Nssa区的ABR不接收来自区域外的三类、四类和五类LSA，ASBR接受来自区域外的七类LSA，并且转化为五类LSA传到其他区域，七类LSA只存在于Nssa区中 ，此时T-Nssa中的ABR则成了假的ASBR 

OSPF中的防环机制
----------

**一、区域防环**  
为了避免区域间的环路，ospf规定不允许直接在两个非骨干区域之间发送路由信息，只允许在一个区域内部或者骨干区域和非骨干区域之间发布路由信息。因此，每个区域边界路由器都必须连接到骨干区域。  
   
**二、区域间水平分割**  
ABR只会将三类LSA转发到骨干区域，如果始发的是本区域的路由是不会被再次注入进来的，目的是为了阻止环路。  
   
**三、LSA三类防环**  
ABR不会向区域0传播它在区域0中学习到的类型为三类LSA的路由，因为这本来就是从区域0传来的，ABR不会计算从非骨干区域来的三类LSA，防止次优路径

OSPF中的路由汇总和路由过滤
---------------

![](https://img-blog.csdnimg.cn/201811220821307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**一： 域间汇总(inter-area)**  
在ABR上做  
ospf 100 router-id   77.7.7.7  
area 1  
abr-summary  188.1.0.0  255.255.252.0  advertise(默认是通告)  
**二： 域外汇总(extend-area)**  
在ASBR上做  
ospf  100  router-id  44.4.4.4  
asbr-summary  155.1.0.0  255.255.252.0    advertise (默认是通告)  
   
注：如果上面的advertise改成 not-advertise ,则对汇总路由也不通告，实现了路由过滤  
   
**路由过滤**  
设置前缀列表： ip ip-prefix  RIP\_TO\_OSPF  index 10 permit 55.5.5.5 32  
设置路由过滤策略：route-policy  XIE  permit  node  20  
                            if-match ip-prefix RIP\_TO\_OSPF  
再进ospf进程里面： import-route rip 1 route-policy XIE 

OSPF中的虚拟链路
----------

### 虚拟链路有两种存在的意义

**一：**

![](https://img-blog.csdnimg.cn/2018112208224078.png)

area3和area0没有相连，因为area3就无法与area0交换LSA，area3就不能与其他area通信。这时，在AR2和AR3之间形成一条虚拟链路，使得area3逻辑上与area0相连，这样area3就可以与area0区域交换LSA了。

**二：** 

![](https://img-blog.csdnimg.cn/20181122082312672.png)

area1把两边的area0给隔开了，而OSPF中的area0必须是整体的不能隔开。所以，就可以在AR2和AR3之间形成一条虚拟链路，使得两边的area0逻辑上是相连的。  
   
形成虚拟链路很简单，只需要进入两个路由器的ospf进程对应下的area内，设置vlink-peer  
\[AR2\]ospf 100 rout 22.2.2.2  
\[AR2-ospf-100\]area 2  
\[AR2-ospf-100-area-0.0.0.2\]vlink-peer 33.3.3.3  
\[AR3\]ospf 100 rout 33.3.3.3  
\[AR3-ospf-100\]area 2  
\[AR3-ospf-100-area-0.0.0.2\]vlink-peer 22.2.2.2  
![](https://img-blog.csdnimg.cn/20181122082333397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

OSPF中的认证
--------

进入端口下  
int g0/0/0  
ospf authentication-mode simple  cipher  xiaoxie   （基于明文的认证）  
ospf authentication-mode md5  1  cipher  xiaoxie     (基于md5的认证)

![](https://img-blog.csdnimg.cn/20181122082400503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

华为模拟器中的配置
---------

```
ospf 100（ospf的进程值，必须设置为同样的）  router-id  77.77.77.77      
area 0  (区域)      
network   10.0.0.1 0.0.0.0 (反子网掩码)       
给一个接口配入ospf，还可以进入接口，然后 ospf enable 100 area 0      
删除一个ospf进程 ：undo ospf 100 （得先进去ospf里面，把network和area都给undo了）      
查看ospf的设置：  display current-configuration | begin ospf      
查看通过ospf学习到的路由：  display ip routing-table protocol ospf      
查看ospf的链路状态数据： display  ospf lsdb      1
查看某条一类LSA的明细：display ospf lsdb router  11.1.1.1      1
查看ospf邻居摘要信息： display  ospf  peer  brief      1
查看ospf邻居详细信息：display  ospf  peer      1
重启ospf进程： reset ospf process      1
查看路由器某个端口的metric值：  display ospf  interface  g0/0/0      1
rip中导入ospf路由：import-route ospf 100 cost 2      1
ospf中导入rip路由:  import-route rip 1      1
下发默认路由，进入ospf进程内:  default-route-advertise always
```
