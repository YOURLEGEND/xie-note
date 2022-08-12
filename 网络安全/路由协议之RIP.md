**目录**

[RIP协议](#t0)

[RIP的路由汇总和过滤](#t1)

[RIP的认证](#t2)

[RIP的防环机制](#t3)

[华为/思科中的配置](#t4)

* * *

RIP协议
=====

**RIP协议**是一种内部网关协议（IGP），底层是贝尔曼福特算法，是一种动态路由选择协议，用于自治系统（AS）内的路由信息的传递。[RIP协议](https://so.csdn.net/so/search?q=RIP%E5%8D%8F%E8%AE%AE&spm=1001.2101.3001.7020)基于距离矢量算法（DistanceVectorAlgorithms），使用“跳数”(即metric)来衡量到达目标地址的路由距离。这种协议的路由器只关心自己周围的世界，只与自己相邻的路由器交换信息，范围限制在 **15跳** (15跳)之内，再远，它就不关心了。所以rip只适用于小区域。Rip协议运行在 **UDP** 协议之上，使用 **520** 端口，使用 **224.0.0.9** 作为组播地址，向外组播路由信息

**工作原理**：RIP通过广播UDP报文来交换路由信息，每30秒发送一次路由信息更新。RIP提供跳跃计数(hopcount)作为尺度来衡量路由距离，跳跃计数是一个包到达目标所必须经过的路由器的数目。如果到相同目标有二个不等速或不同带宽的路由器，但跳跃计数相同，则RIP认为两个路由是等距离的。RIP最多支持的跳数为15，即在源和目的网间所要经过的最多路由器的数目为15，跳数16表示不可达。

![](https://img-blog.csdnimg.cn/20181121081341242.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

RIP的路由汇总和过滤
-----------

**路由汇总**  
 手工自动汇总  进入接口下，然后  rip summary-address 122.1.0.0  255.255.252.0      
rip在做手工汇总时，会默认抑制明细路由。在做手工汇总时，要记得两边的端口都做汇总  
rip中路由的cost值是路由传递入向接口的累加值，进入端口，然后 rip metricin 2  
   
**路由过滤**  
用前缀列表将要过滤的路由匹配,并且拒绝  ip  ip-prefix  FILTER\_RIP   index  序号值   deny  33.3.3.0  24  
允许其他的路由   ip  ip-prefix  FILTER\_RIP  index  序号值   permit 0.0.0.0   0   greater-equal   0   less-equal  32  
在rip下，调用这个命令  filter-policy  ip-prefix   FILTER\_RIP  export  g0/0/0

RIP的认证
------

进入端口下  
rip  authentication-mode   simple     cipher/plain  密码    
rip  authentication-mode   md5    usual/nonstandard   cipher  密码   id值

RIP的防环机制
--------

![](https://img-blog.csdnimg.cn/20181122080317972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

路由器从某个接口学到的路由，不会再从该接口发回给邻居路由，这样就避免了由于网络发生故障时，发生环路

![](https://img-blog.csdnimg.cn/20181122080416774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

毒性反转是指路由器从某个接口学到跳数为16的路由条目后，从原接收接口发回给邻居路由器该路由条目(毒性反转打破了水平分隔) (只适用于RB指定了默认路由是RA)  
路由毒化是值路由器从某个接口学到跳数为16的路由条目后，从另外的接口发出此路由给其他路由器  
 

![](https://img-blog.csdnimg.cn/20181122080445874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

触发更新是指当路由信息发生变化时，立即向邻居设备发送触发更新报文  
更新有时间更新和内容更新，时间更新包括触发和周期，内容更新包括完整更新和增量更新，rip会每隔30秒将全部信息更新给其他人

华为/思科中的配置
---------

**华为模拟器中的配置**  
RIP动态路由协议（路由器之间相互学习网段）：   
  rip  1  
 version 2            Version 1版本只支持有类地址  
 undo summary         关闭自动汇总，如果不关闭的话，会把子网所有的路由条目汇总成超网一条路由条目  
 network   网段 (不支持子网划分)  
一般是路由器面对主机的网关接口开启静默 silenct-interface g0/0/0    
通告默认路由    default-route originate cost 10  
   
**思科模拟器中的配置**  
router rip  
version  2  
network  网段 (支持子网划分)