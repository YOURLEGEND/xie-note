**目录**

[JumpServer](#t0 "JumpServer")

[漏洞简述](#t1 "漏洞简述")

[漏洞影响版本](#t2 "漏洞影响版本")

[漏洞环境搭建](#t3 "漏洞环境搭建")

[漏洞复现](#t4 "漏洞复现")

[漏洞修复](#t5 "漏洞修复")

* * *

JumpServer
----------

JumpServer是使用Python  [Django](https://so.csdn.net/so/search?q=Django&spm=1001.2101.3001.7020) 框架进行开发的全球首款完全开源的堡垒机, 使用GNU GPL v2.0 开源协议, 并且符合4A 的专业运维审计系统。

### 漏洞简述

2021年1月15日，JumpServer发布更新，修复了一处远程命令执行漏洞。由于 JumpServer 某些接口未做授权限制，攻击者可通过构造恶意请求获取到日志文件敏感信息，或者执行相关[API](https://so.csdn.net/so/search?q=API&spm=1001.2101.3001.7020)操作控制其中所有机器，执行任意命令，影响巨大。

### 漏洞影响版本

*   JumpServer < v2.6.2
*   JumpServer < v2.5.4
*   JumpServer < v2.4.5
*   JumpServer = v1.5.9

### 漏洞环境搭建

下载安装sh脚本

```
https://www.o2oxy.cn/wp-content/uploads/2021/01/quick_start.zip      
或者      
https://github.com/jumpserver/jumpserver/releases/download/v2.6.1/quick_start.sh
```


然后执行该sh脚本

![](https://img-blog.csdnimg.cn/20210206221636552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后一路回车即可，这个过程有点漫长，等待即可。如下，最终安装完成！

![](https://img-blog.csdnimg.cn/20210206222823253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后执行如下命令： 

```
cd /opt/jumpserver-installer-v2.6.2      
./jmsctl.sh start
```


![](https://img-blog.csdnimg.cn/20210206223050740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后访问url即可：[http://192.168.106.6:8080/core/auth/login/](http://192.168.106.6:8080/core/auth/login/ "http://192.168.106.6:8080/core/auth/login/")  默认账号密码：admin/admin 。初次登陆会叫你修改密码，修改完密码后即可登陆。

![](https://img-blog.csdnimg.cn/20210206223245419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改用户列表中用户名为root，即后面ssh连接时的用户是root

![](https://img-blog.csdnimg.cn/20210206223410589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后创建一个系统用户root

![](https://img-blog.csdnimg.cn/20210206223614230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 新建一个资产列表

![](https://img-blog.csdnimg.cn/20210206225055141.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后资产授权

![](https://img-blog.csdnimg.cn/20210206230005351.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 会话管理——>web终端，即可成功连接机器

![](https://img-blog.csdnimg.cn/20210206230106215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 漏洞复现

通过未授权访问得到三个id——>通过三个ID 进行一个零时token的获取——>通过临时token 进行ws的访问。进而命令执行

查看jumpserver版本

![](https://img-blog.csdnimg.cn/20210206232043332.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

运行脚本，获取asset,system\_user,user三个ID值。这一步的时候我运行脚本获取不到这三个值，所以也就导致后来getshell失败了。详请可以参考最下面的参考文章。

将asset，system\_user，user三个ID值放入脚本，运行Getshell。

### 漏洞修复

将JumpServer升级到安全版本：[重要通知｜JumpServer漏洞通知及修复方案 – 技术博客 – FIT2CLOUD 飞致云](https://blog.fit2cloud.com/?p=1761 "重要通知｜JumpServer漏洞通知及修复方案 –  技术博客 – FIT2CLOUD 飞致云")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[开源堡垒机JumpServer远程命令执行漏洞复现](https://mp.weixin.qq.com/s/1K4qMViaMShvv-Nh-XKoYw "开源堡垒机JumpServer远程命令执行漏洞复现")

                   [https://github.com/Skactor/jumpserver\_rce](https://github.com/Skactor/jumpserver_rce "https://github.com/Skactor/jumpserver_rce")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-137)[Web应用开发](https://edu.csdn.net/skill/python/python-3-137) [Django](https://edu.csdn.net/skill/python/python-3-137)84711 人正在系统学习中