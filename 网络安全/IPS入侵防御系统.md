**目录**

[IPS入侵防御系统](#t0)

[如何才能更好的防御入侵](#t1) 

[IPS与IDS的区别](#t2) 

[IDS与IPS的部署](#t3) 

[IPS独立部署](#t4)

[IPS分布式部署](#t5)

* * *

IPS入侵防御系统
=========

IPS(Intrusion Prevention System)：入侵防御系统。随着网络攻击技术的不断提高和网络安全漏洞的不断发现，传统[防火墙技术](https://so.csdn.net/so/search?q=%E9%98%B2%E7%81%AB%E5%A2%99%E6%8A%80%E6%9C%AF&spm=1001.2101.3001.7020)加传统IDS的技术，已经无法应对一些安全威胁。在这种情况下，IPS技术应运而生，IPS技术可以深度感知并检测流经的数据流量，对恶意报文进行丢弃以阻断攻击，对滥用报文进行限流以保护网络带宽资源。对于部署在数据转发路径上的IPS，可以根据预先设定的安全策略，对流经的每个报文进行深度检测(协议分析跟踪、特征匹配、流量统计分析、事件关联分析等)，如果一旦发现隐藏于其中的网络攻击，可以根据该攻击的威胁级别立即采取抵御措施，这些措施包括(按照处理力度)向管理中心告警、丢弃该报文、切断此次应用会话、切断此次TCP连接。

进行了以上分析以后，我们可以得出结论：在办公网中至少需要在以下区域部署IPS：即办公网与外部网络的连接部位(入口/出口)、重要服务器集群前端、办公网内部接入层。至于其它区域，可以根据实际情况与重要程度，酌情部署。目前，下一代防火墙也集成了IPS的功能。但是IPS仍然是不可代替的。

IPS的接入方式：**串行接入(串联)**

工作机制：检测+阻断

不足：单点故障、性能瓶颈、误报

![](https://img-blog.csdnimg.cn/20181122170636224.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如何才能更好的防御入侵 
------------

![](https://img-blog.csdnimg.cn/20181122170802465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181122170831623.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IPS与IDS的区别 
-----------

![](https://img-blog.csdnimg.cn/20181122170909667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IDS与IPS的部署 
-----------

![](https://img-blog.csdnimg.cn/20181122171008513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IPS独立部署
-------

![](https://img-blog.csdnimg.cn/20181122171116265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IPS分布式部署
--------

![](https://img-blog.csdnimg.cn/20181122171129655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章： [防火墙Firewall](https://blog.csdn.net/qq_36119192/article/details/84423452) 

                   [IDS入侵检测系统](https://blog.csdn.net/qq_36119192/article/details/84343269)