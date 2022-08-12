**目录**

[DHCP](#t0)

[DHCP的报文类型](#t1)

[DHCP的部署](#t2)

[DHCP中继(DHCP代理)](#t3)

* * *

DHCP
----

**DHCP**（Dynamic Host [Configuration](https://so.csdn.net/so/search?q=Configuration&spm=1001.2101.3001.7020) Protocol，动态主机配置协议）是一个局域网的网络协议，使用UDP(67、68)端口工作， 主要有两个用途：给内部网络或网络服务供应商自动分配IP地址，给用户或者内部网络管理员作为对所有计算机作中央管理的手段

客户端使用 [UDP](https://so.csdn.net/so/search?q=UDP&spm=1001.2101.3001.7020) 68 号端口向服务器UDP 67 号端口发送请求，服务器使用UDP 67 号端口给客户端UDP 68 号端口回应

![](https://img-blog.csdn.net/20181018150323473?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](https://img-blog.csdn.net/20181018150430863?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

DHCP的作用：可为大量客户机提供自动分配地址，提高网络配置效率。可为客户机分配 ip 、子网掩码、网关、DNS服务器、默认搜索域名等。  
DHCP有三种机制分配IP地址：

*   自动分配方式（Automatic Allocation），DHCP服务器为主机指定一个永久性的IP地址，一旦DHCP客户端第一次成功从DHCP服务器端租用到IP地址后，就可以永久性的使用该地址。
*   动态分配方式（Dynamic Allocation），DHCP服务器给主机指定一个具有时间限制的IP地址，时间到期或主机明确表示放弃该地址时，该地址可以被其他主机使用。
*   手工分配方式（Manual Allocation），客户端的IP地址是由网络管理员指定的，DHCP服务器只是将指定的IP地址告诉客户端主机

IP租约期限到达50%时，[DHCP](https://so.csdn.net/so/search?q=DHCP&spm=1001.2101.3001.7020)客户端会向DHCP服务器发送单播请求续约，倘若DHCP服务器没回应，客户端在租约期限到达87.5%时，客户端会向DHCP服务器广播申请重绑定IP。如果IP租约到期前都没有收到服务器响应，客户端停止使用此IP地址。  
如果DHCP客户端不再使用分配的IP地址，也可以主动向DHCP服务器发送DHCP RELEASE报文，释放该IP地址。

DHCP的报文类型
---------

![](https://img-blog.csdn.net/20181018144338106?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181018144352268?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 DHCP客户机发送完DHCP discover消息后，如果未能收到DHCP OFFER，它就会重试4次（相隔2,4,8,16s,加上一个0到1000ms之间的随机数）如果DHCP客户机经过努力仍未获得任何有效的IP地址，将使用备用配置。每隔5分钟，该客户机都继续尝试寻找DHCP服务器，如果有某个DHCP服务器成为可用，客户机将接受到合法的IP地址。

DHCP的部署
-------

​​​​安装：yum  install   dhcp  -y   
服务：/usr/sbin/dhcpd  
配置文件目录： /etc/dhcp/dhcpd.conf  

模板文件目录：/usr/share/doc/dhcp-4.2.5/dhcpd.conf.example  
开启/关闭/重启/开机自启/开机不自启：  systemcctl   start / stop / restart /enable /disable dhcpd

我们先把模板文件追加到配置文件中:  cat   /usr/share/doc/dhcp-4.2.5/dhcpd.conf.example >> /etc/dhcp/dhcpd.conf ,然后打开配置文件修改 ，把不用的内容都删除，只保留下面几行。

```
subnet 10.0.0.0 netmask 255.0.0.0 {              //分配的网段      
  range 10.0.0.100 10.0.0.200;                   //分配的地址范围      
  option domain-name-servers redhat.xie.com;      
  option domain-name "xie.com";                 //domain域      
  option routers 10.0.0.254;                    //路由ip      
  option broadcast-address 10.0.0.255;          //广播地址      
  default-lease-time 600;      
  max-lease-time 7200;      
}      1
host BOSS {                                    //给以下MAC地址做特殊的设置      1
  hardware ethernet 00:0C:29:1C:D2:58;         // MAC地址      1
  fixed-address 10.0.0.50;                     // 分配ip      1
}
```


windows客户机获取dhcp ：

*   ipconfig  /release          释放原来的ip配置
*   ipconfig  /renew           重新获得ip

Linux客户机获取dhcp： 

*   在网卡配置文件中设置获取方式为dhcp
*   然后重启网络服务  systemctl restart  network 
*   然后 dhclient  -d  eth0

DHCP中继(DHCP代理)
--------------

![](https://img-blog.csdn.net/20181018144644739?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

PC1以广播的形式发送DHCP请求，AR1的G0/0/0口收到 之后，以源地址192.168.1.1单播的形式向AR2的g0/0/0口12.1.1.2发送DHCP请求，AR2口以单播的形式向192.168.1.1回复DHCP，DHCP把回复包发给PC1。在这之间，PC1只知道DHCP的回复包是12.1.1.2发送的。

![](https://img-blog.csdn.net/20181018144701196?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181018144708639?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中