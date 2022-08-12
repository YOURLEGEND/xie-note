**目录**

[ARP](#t0)

[ARP数据包的封装](#t1) 

[免费ARP](#t2)

[ARP代理](#t3)

[ARP欺骗](#t4)

[ARP欺骗的实现](#t5)

[ARP欺骗的危害](#t6)

[ARP欺骗的防御](#t7)

[RARP](#t8)

* * *

ARP
---

当一个局域网中有很多台主机时，假如PC1要和PC2通信，但是PC1只知道PC2的ip地址，而不知道PC2的MAC地址，那么PC1将数据包发给交换机进行[ARP](https://so.csdn.net/so/search?q=ARP&spm=1001.2101.3001.7020)广播，询问IP地址对应的MAC地址是哪个，此时局域网中的所有主机都会受到ARP请求，但是PC3，PC4收到数据包之后，发现这个IP地址不属于自己，就会把包丢弃。而PC2收到了数据包之后，发现这个IP地址是自己的，就会发一个ARP单播回应包，告诉他这个IP地址对应的MAC地址。此时，在PC1内就会有一个ARP缓存表，在交换机内部也有会一个CAM表，这就是**ARP协议（Address Resolution Protocol）**

![](https://img-blog.csdnimg.cn/20181031082126236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

主机通信需要查找ARP表，交换机通信需要查找CAM表，而路由器通信需要查找Route表

*   ARP表： ip <--> MAC地址   (存储在主机、路由器的ARP缓存表中)
*   CAM表：MAC地址 <--> 端口 (存储在交换机的CAM缓存表中)
*   Route表: route <--> 端口 (存储在路由器的Route表中)

查看ARP信息： arp  -a   
清除ARP信息： arp  -d   
arp静态绑定:      arp  -s  ip  mac 

![](https://img-blog.csdnimg.cn/20181031082609210.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ARP数据包的封装 

![](https://img-blog.csdnimg.cn/20181031083020913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181031083036650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

硬件类型字段表示硬件地址的类型.它的值为1即表示以太网  
协议类型字段表示的协议类型.它的值为 0x0800 即表示 IP 地址  
硬件地址长度和协议地址长度分别指硬件地址和协议地址的长度,以字节为单位.对于以太网上 IP 地址的 ARP 请求或应答来说,它们的值分别为 6 和 4  
操作字段op出四种操作类型,它们是 ARP 请求(值为1)、ARP 应答(值为 2 )、RARP 请求(值为 3 )和 RARP 应答(值为 4 )  
接下来的四个字段是发送端的mac址、发送端的ip址、目的端的mac址和目的端的ip址.  
 

### 免费ARP

免费ARP主要是用来进行地址的冲突性检测，当为某台主机配置完IP地址后，此设备会连续发送两次免费ARP，如果无回应，说明不存在地址冲突。免费ARP还有一个作用是刷新同网段内其他终端的ARP缓存表

![](https://img-blog.csdnimg.cn/20181031082524330.png)

![](https://img-blog.csdnimg.cn/20181031082544577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ARP代理

ARP代理的工作原理：R1和R3虽然属于不同的广播域，R1要和R3进行通信，R1发出ARP请求广播包，请求获得R3的MAC地址。由于路由器不会转发广播包，因此ARP请求只能到达路由器，不能到达R3。当在路由器上启用ARP代理后，路由器会查看ARP请求，发现IP地址192.168.20.1属于它连接的另一个网络，因此路由器用自己的接口MAC地址代替R3的MAC地址，向R1发送了一个ARP应答。R1收到ARP应答后，会认为R3的MAC地址就是R2的MAC地址，而不会感知到ARP代理的存在。proxy ARP应该使用在主机没有配置默认网关或没有任何路由策略的网络上 (windows系统不配网关与不同网段通信的话，直接不发送数据包，Linux会发送)

![](https://img-blog.csdnimg.cn/20181031082835998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181031082849950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ARP欺骗

假设内网中有下列几台主机：

<table border="1" cellpadding="1" cellspacing="1" style="width:500px;"><tbody><tr><td>主机</td><td>IP</td><td>MAC</td></tr><tr><td>A</td><td>192.168.1.1</td><td>aa:aa:aa:aa:aa:aa</td></tr><tr><td>B</td><td>192.168.1.2</td><td>bb:bb:bb:bb:bb:bb</td></tr><tr><td>C</td><td>192.168.1.3</td><td>cc:cc:cc:cc:cc:cc</td></tr></tbody></table>

在同一局域网内，主机A和B想要通信，于是主机A就会向整个局域网广播ARP请求，请求B主机的ip(192.168.1.2)对应的MAC地址。正常情况下，B和C都会收到整个ARP请求。B看到目标ip地址是自己，于是会回一个ARP回复包，告诉A主机IP 192.168.1.2对应的MAC地址 bb:bb:bb:bb:bb:bb。C看到目标ip地址不是自己，则丢弃整个数据包。  
但是，事情并没有想象的那么美好！C是黑客，他想监听A和B之间通信的流量包。他收到这个请求后，也向A回复ARP回复包，并且大量的回复ARP回复包。于是A将会收到B和C回复的ARP回复包。由于ARP回复包遵循后到优先原则。C大量回复的ARP回复包会把B回复的数据包给覆盖了。所以A最终会相信C的ARP回复包。认为192.168.1.2对应的MAC地址是cc:cc:cc:cc:cc:cc。  
同理，C也会用同样的手段去欺骗B。  
这样,A就会认为C是B，B会认为C是A。于是，他们两之间通信的数据包都会发送给C。

那么，现在C就可以对A和B之间的数据流量做手脚了！

而如果C是欺骗A和路由器或者B和路由器，那么，C将可截获A和B访问外网的所有流量了！这是非常可怕的

### ARP欺骗的实现

在arp欺骗之前，先开启流量转发，不然将直接导致被攻击者断网！

```
echo 1 > /proc/sys/net/ipv4/ip_forward       #开启流量转发
```


**arpspoof** 

aspspoof是kali下一款命令行进行ARP欺骗的工具，操作简单

```
arpspoof -i eth0  -t 192.168.10.10  192.168.10.254  #第二个ip是网关的ip
```


**ettercap**

ettercap是Kali下一款用于ARP欺骗的工具，可以图形化操作也可以命令行

```
ettercap -G   #图形化操作      
ettercap -i eth0 -Tq  -M  arp:remote  //192.168.10.10//  //192.168.10.254//   #命令行操作，两个ip地址可以互换
```


**bettercap**

bettercap是ettercap的继承者，功能强大。传送门——>[Bettercap2.X版本的使用](https://blog.csdn.net/qq_36119192/article/details/84582109)

**driftnet** 

driftnet是kali下能抓取流经网卡的图片和链接。和ARP欺骗工具进行结合，可以探测局域网内其他用户流量中的图片

```
driftnet  -b  -i  eth0  查看图片      
-b  捕获到新的图片时发出嘟嘟声      
-i  interface     选择监听接口      
-f  file   读取一个指定pcap数据包中的图片      
-p  不让所监听的接口使用混杂模式      
-a  后台模式：将捕获的图片保存到目录中（不会显示在屏幕上）      
-m number 指定保存图片数的数目      
-d directory  指定保存图片的路径      
-x prefix  指定保存图片的前缀名
```


**urlsnarf** 

urlsnarf是kali下能抓取流经指定网卡80端口、8080端口或3128的数据，也就是流经的http协议，和arp欺骗工具结合可以探测局域网内其他用户浏览过的网站

```
urlsnarf -i eth0
```


![](https://img-blog.csdnimg.cn/20181130183640268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ARP欺骗的危害

*   断网攻击，直接把截获的数据包丢弃
*   限速，断网攻击很容易被发现，很多黑客会对同一个局域网内的其他用户进行限速，这样，黑客自己上网的速度就快了
*   窃取隐私，如果截取的数据是通过明文传输的，那么，就可以看到账号密码等东西。常见的明文传输的协议有HTTP、FTP、Telnet、POP3、SMTP等

### ARP欺骗的防御

**网络设备**

支持DAI(Dynamic ARP Inspection)动态ARP监测的交换机或无线路由器

*   交换机记录每个接口对应的IP地址和MAC地址，即 port<->mac<->ip，生成DAI监测表
*   交换机监测每个接口发送过来的ARP回应包，根据DAI表判断是否违规，若违规则丢弃此数据包并对接口进行惩罚

但是事实上，大部分能支持这种动态ARP监测技术的交换机或者路由器，基本都是企业级的产品。即便是企业级交换机，具备局域网安全防御功能的设备，价格都要高出不少。所以很多中小型企业或者校园网，基本都愿意买不具备这种功能的产品，因为能通网就行，至于是否安全，这不是他们考虑的问题

**用户端**

对于普通用户，陌生网络不要随意接入。其次，用户的电脑或者手机上安装360卫士，腾讯电脑管家等安全产品

RARP
----

RARP：反向地址转换协议（RARP：Reverse Address Resolution Protocol） 反向地址转换协议（RARP），将MAC地址转换成IP地址，允许局域网的物理机器从网关服务器的 ARP 表或者缓存上请求其 IP 地址。