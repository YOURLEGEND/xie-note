**目录**

[搭建代理](#t0 "搭建代理")

[添加路由](#t1 "添加路由")

[​搭建Socks4a代理](#t2 "​搭建Socks4a代理")

[搭建Socks5代理](#t3 "搭建Socks5代理")

[连接代理](#t4 "连接代理 ") 

* * *

> 注：通过MSF起的socks代理，经常性的不[监听](https://so.csdn.net/so/search?q=%E7%9B%91%E5%90%AC&spm=1001.2101.3001.7020)端口，也就导致代理失败。试过好多次都是这样，应该是MSF的一个bug。

搭建代理
----

当我们通过MSF拿到一个机器的权限后，想通过MSF搭建socks代理，然后通[内网](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91&spm=1001.2101.3001.7020)。

MSF中有三个代理模块，分别是socks4a、socks5、socks\_unc。我们一般用 socks4a和socks5进行代理。socks5可以设置用户名和密码。这里运行代理后，有时候MSF不会监听端口(有可能是个bug，试了好多次都有这种情况)，所以也就导致代理失败。

![](https://img-blog.csdnimg.cn/20191218223038174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 添加路由

在使用代理之前，我们需要先添加路由，让MSF能到达目标机器内网。因为这里socks模块只是将代理设置为监听的端口(默认是1080)，即通过proxychains的流量都转给本地的1080端口，又因为这是MSF起的监听端口，所以通过代理走的流量也都能到达内网。

执行以下命令添加路由

```
route add 0.0.0.0 0.0.0.0 1
```


### ![](https://img-blog.csdnimg.cn/20200430235311956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
搭建Socks4a代理

```
use auxiliary/server/socks4a      
set SRVHOST  0.0.0.0      
set SRVPORT  1080      
run
```


![](https://img-blog.csdnimg.cn/2019121723321910.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 搭建Socks5代理

```
#使用socks5代理      
use auxiliary/server/socks5      
set SRVHOST  0.0.0.0      
set SRVPORT  1080      
set USERNAME root      
set PASSWORD Password@      
run
```


连接代理 
-----

首先修改/etc/proxychains.conf文件

![](https://img-blog.csdnimg.cn/20191218223813287.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后执行命令前加上 proxychains命令

```
#然后就可以使用其他命令了，比如nmap      
proxychains4 nmap -p 1-10000 -Pn -sT x.x.x.x     #在打开其他程序前加上proxychains
```


![](https://img-blog.csdnimg.cn/20191218000745582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)