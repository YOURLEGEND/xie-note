**目录**

[1：部署域名解析](#t0 "1：部署域名解析")

[2：CS开启监听DNS Beacon](#t1 "2：CS开启监听DNS Beacon")

[3：生成DNS木马](#t2 "3：生成DNS木马")

[4：上线](#t3 "4：上线")

* * *

在之前的文章中我介绍了使用 DNS-Shell 和 Dnscat2 利用DNS协议来进行命令控制，通过DNS协议进行通信，使得流量更加隐秘，躲避agent/DLP等安全设备的检测，实现相对隐秘的命令控制。

传送门：[使用DNS进行命令控制(DNS-Shell)](https://xie1997.blog.csdn.net/article/details/92695913 "使用DNS进行命令控制(DNS-Shell)")   、 [使用DNS进行命令控制(dnscat2)](https://xie1997.blog.csdn.net/article/details/104429983 "使用DNS进行命令控制(dnscat2)")

本节我将介绍如何使用CobaltStrike中的DNS Beacon利用DNS协议进行命令控制。

1：部署域名解析
--------

首先，用一台公网的Linux系统的VPS作为C&C服务器(注意：VPS的53端口一定要开放)，并准备好一个可以配置的域名(这里我们假设是hack.com)。然后，去配置域名的记录。首先创建记录A，将自己的域名 www.hack.com 解析到VPS服务器地址。然后，创建NS记录，将 test.hack.com 指向 www.hack.com 。

*   第一条A类解析是在告诉域名系统，www.hack.com 的IP地址是 xx.xx.xx.xx 。
*   第二条NS解析是在告诉域名系统，想要知道 test.hack.com 的IP地址，就去问 www.hack.com 。

![](https://img-blog.csdnimg.cn/20200220235659545.png)

为什么要设置NS类型的记录呢？因为NS类型的记录不是用于设置某个域名的DNS服务器的，而是用于设置某个子域名的DNS服务器的。

**如何验证域名解析设置是否成功？**  
在随便一台电脑上ping域名 www.hack.com ，若能ping通，且显示的IP地址是我们配置的VPS的地址，说明第一条A类解析设置成功并已生效。

![](https://img-blog.csdnimg.cn/20200618184421130.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在我们的VPS上执行以下命令监听UDP53端口

```
tcpdump -n -i eth0 udp dst port 53
```


在任意一台机器上执行  nslookup test.hack.com 命令，如果在我们的VPS监听的端口有查询信息，说明第二条记录设置成功

![](https://img-blog.csdnimg.cn/20200221000107365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

2：CS开启监听DNS Beacon
------------------

![](https://img-blog.csdnimg.cn/20200529140938240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200529140616926.png)

3：生成DNS木马
---------

注意，这里生成的类型得是 Windows Executable(S)

![](https://img-blog.csdnimg.cn/20200529143558516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里勾不勾选x64取决于目标主机的架构

![](https://img-blog.csdnimg.cn/20200529143656947.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

4：上线
----

只要木马在目标主机执行成功，我们的CobaltStrike就能接收到反弹的shell。但是默认情况下，主机信息是黑色的。

![](https://img-blog.csdnimg.cn/20200529143036168.png)

我们需要执行以下命令，让目标主机信息显示出来

```
checkin      
mode dns-txt
```


![](https://img-blog.csdnimg.cn/20200529143435960.png)

![](https://img-blog.csdnimg.cn/20200529143457793.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11142 人正在系统学习中