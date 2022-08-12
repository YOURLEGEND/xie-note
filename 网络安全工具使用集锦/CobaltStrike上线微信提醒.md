**目录**

[利用ServerChan实现CobaltStrike上线微信提醒](#t0 "利用ServerChan实现CobaltStrike上线微信提醒")

[获取SCKEY](#t1 "获取SCKEY")

[绑定微信](#t2 "绑定微信")

[CobaltStrike安装插件](#t3 "CobaltStrike安装插件")

* * *

在红蓝对抗中，我们经常会遇到机器上线了，但是没有及时的发现，导致机器在发现之前掉线的情况。本文我们将利用ServerChan来实现CobaltStrike上线微信提醒。

利用ServerChan实现CobaltStrike上线微信提醒
--------------------------------

   ServerChan是一款 程序员 和 服务器 之间的通信软件，也就是从服务器推送报警和日志信息到手机的工具。网站地址：[http://sc.ftqq.com/3.version](http://sc.ftqq.com/3.version "http://sc.ftqq.com/3.version")

### 获取SCKEY

用Github账号登录 [http://sc.ftqq.com/3.version](http://ServerChan "http://sc.ftqq.com/3.version") ，点击发送消息，获取SCKEY

![](https://img-blog.csdnimg.cn/20200623180157848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 绑定微信

用微信绑定ServerChan

![](https://img-blog.csdnimg.cn/20200623180404730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200623180439552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### CobaltStrike安装插件

下载 http\_ftqq.cna 文件，修改24行的SreverChan链接为刚刚获取的SCKEY

![](https://img-blog.csdnimg.cn/20200623180837699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在CobaltStrike脚本管理器中加载该脚本即可

![](https://img-blog.csdnimg.cn/20200623181342240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

用虚拟机测试上线，微信成功收到通知。

![](https://img-blog.csdnimg.cn/20200623210131961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200623210102978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是在客户端加载插件，当客户端没连接上服务端的时候，是不提醒的。所以，我们需要在服务端运行该插件。关于如何在服务端运行cna插件，传送门：[CobaltStrike服务端加载插件](https://xie1997.blog.csdn.net/article/details/106088279#%E6%9C%8D%E5%8A%A1%E7%AB%AF%E5%8A%A0%E8%BD%BD "CobaltStrike服务端加载插件")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Cobalt Strike 上线微信提醒](http://www.nmd5.com/?p=567&from=timeline&isappinstalled=0 "Cobalt Strike 上线微信提醒")