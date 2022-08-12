**目录**

[Pystinger](#t0 "Pystinger")

[实现多主机上线cs](#t1 "实现多主机上线cs")

[目标主机执行](#t2 "目标主机执行")

[VPS执行](#t3 "VPS执行")

[CS开启监听](#t4 "CS开启监听")

[生成木马](#t5 "生成木马")

[内网横向移动](#t6 "内网横向移动")

* * *

Pystinger
=========

一款利用webshell实现[内网](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91&spm=1001.2101.3001.7020)socks4代理、端口映射的工具，可直接用于MSF/CS等上线。使用python编写， 当前支持php,jsp(x),aspx三种代理脚本 。

项目地址： [GitHub - FunnyWolf/pystinger: Bypass firewall for traffic forwarding using webshell 一款使用webshell进行流量转发的出网工具](https://github.com/FunnyWolf/pystinger "GitHub - FunnyWolf/pystinger: Bypass firewall for traffic forwarding using webshell 一款使用webshell进行流量转发的出网工具")

原地介绍： [红队攻防实践：不出网主机搭建内网隧道新思路](https://mp.weixin.qq.com/s/45AKbRS677fxynKW6Qfz7w "红队攻防实践：不出网主机搭建内网隧道新思路")

实现多主机上线cs
---------

### 目标主机执行

首先，将对应的脚本文件上传到目标网站目录，访问成功如下： [http://192.168.10.132:88/proxy.php](http://192.168.10.132:88/proxy.php "http://192.168.10.132:88/proxy.php")

![](https://img-blog.csdnimg.cn/2020083022234566.png)

然后将 stinger\_server.exe上传到目标服务器 ，执行如下命令：

```
start stinger_server.exe 0.0.0.0
```


![](https://img-blog.csdnimg.cn/2020083022241058.png)

### VPS执行

然后将stinger\_client上传到vps，执行如下命令

```
./stinger_client -w http://192.168.10.132:88/proxy.php -l 0.0.0.0 -p 60000
```


![](https://img-blog.csdnimg.cn/20200830222437623.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

此时已经将目标服务器的60020[端口映射](https://so.csdn.net/so/search?q=%E7%AB%AF%E5%8F%A3%E6%98%A0%E5%B0%84&spm=1001.2101.3001.7020)到VPS的60020端口了

### CS开启监听

这里的http host填目标机器的**内网IP**，端口填上面RAT Config中的端口，默认为60020

![](https://img-blog.csdnimg.cn/20200830222530592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 生成木马

生成木马，[监听](https://so.csdn.net/so/search?q=%E7%9B%91%E5%90%AC&spm=1001.2101.3001.7020)器选择刚刚创建的test监听器，将木马放到目标主机运行。运行成功后，我们的vps可以看到有数据交互

![](https://img-blog.csdnimg.cn/20200830222549673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

并且CS这边也上线了

![](https://img-blog.csdnimg.cn/20200830222604319.png)

### 内网横向移动

内网继续横向移动时，由于内网其他主机也是对外不通的，所以监听器使用刚刚创建的test监听器即可。

![](https://img-blog.csdnimg.cn/20200830222623297.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)