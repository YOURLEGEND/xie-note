**目录**

[网卡的配置](#t0)

[NetworkManager的使用](#t1)

[Team网卡绑定](#t2)

[Centos6.5、Redhat7、Kali网卡配置的不同](#t3)

[Kali桥接模式配置静态ip](#t4)

* * *

### 网卡的配置

网卡命名的不同：

*   Rhel6 及其之前命名都是以 eth 开头，第一块网卡就叫 eth0 ，第二块网卡就叫 eth1
*   Rhel7 开始命名规则变了，eno1代表由主板BIOS内建的网卡 ；ens1代表由主板BIOS内建的PCI-E界面的网卡；enp2s0 代表PCI-E界面的独立网卡，可能有多个网卡接口，因此会有s0，s1的编号。以下是命名的规则

```
en:eethernet 以太网-双绞线      
wl:wirelessLAN：无线网卡      
ww：WAN广域网：串行线缆       
o：on-board板载网卡      
s:hotplug热插拔      
p：PCI接口       
N：序号或者ID
```


修改网卡名称 

Rhel7中网卡的命名很多人觉得不熟，也不如以前那么直观。所以，我们可以把网卡的名称改成以前的 eth0 的形式

1.  vim /etc/default/grub   找到 GRUB\_CMDLINE\_LINUX行，直接到最后添加，net.ifnames=0 biosdevname=0
2.  grub2-mkconfig -o /boot/grub2/grub.cfg    更新配置文件
3.  进入 /etc/sysconfig/network-scripts/ 目录，将网卡配置文件名称修改为ifcfg-eth0： mv  ifcfg-eno16777736   ifcfg-eth0     然后打开修改后的配置文件 ifcfg-eth0 将NAME修改为eth0:   NAME=eth0
4.  reboot    重启，使网卡名称重置

**脚本实现修改网卡名称**

```
#! /bin/bash      
# Author=谢公子      
# Date=2018-10-10      
NAME=`ifconfig | awk -F: 'NR==1{print $1}'`  ##网卡的名字      
path="/etc/sysconfig/network-scripts/ifcfg-$NAME"  ##网卡配置文件的路径      
sed -i '/^GRUB_CMDLINE_LINUX/s/$/net.ifnames=0 biosdevname=0/' /etc/default/grub      
grub2-mkconfig -o /boot/grub2/grub.cfg      
sed -i '/^NAME/d' $path      
sed -i '$a NAME='eth0'' $path      1
mv $path /etc/sysconfig/network-scripts/ifcfg-eth0      1
reboot
```


修改 hostname主机名

*   临时配置： hostname  xie   (Rhel6、7)
*   永久配置： hostnamectl   set-hostname   xie    或        打开 /etc/hostname  ，修改为 xie    (Rhel7)

网络测试命令

*   ifconfig    、  ip  address   、  ip  address  show 、nmcli  device   show (Rehl7)      查看网卡信息
*   ip  route   、  route -n  查看路由表
*   ip  neigh        查看邻居，也就是同一网段的地址。相当于一个ARP地址表
*   ip -s link show eth0   可以看到网卡设备接受和发送数据包的情况
*   tarceroute / tracepath  www.baidu.com        跟踪到达目标主机所经过的每一跳

网卡的设置(临时)

*   设置网卡ip： ifconfig  eth0  192.168.10.10/24     或   ifconfig  eth0  192.168.10.10  netmask   255.255.255.0
*   设置网卡MAC地址：ifconfig  eth0  hw  ether  00:1c:3g:df:sf:4f
*   设置网卡虚拟ip： ifconfig  eth0:0   10.10.10.10
*   启用网卡：ifconfig  eth0  up        或   ifup  eth0
*   禁用网卡：ifconfig  eth0  down    或   ifdown  eth0

路由条目的设置(临时)

*   添加路由表中默认网关记录：route add  default  gw  网关ip
*   添加路由表中指定网段的路由记录：route  add  -net  10.10.10.10/24   gw   网关ip
*   删除路由表中默认网关记录：route  del  default  gw  网关ip
*   删除指定网段路由记录：route  del  -net   10.10.10.10/24

网卡、路由、DNS的永久设置(Rhel6、7)：

Rhel7中，网卡配置文件在 /etc/sysconfig/network-script/ifcfg-xxxx  ，路由条目和网关在同一个目录，只不过名称为 route-xxx，如果之前不存在这个文件，则新建该文件, xxx为网卡名称。hosts文件在 /etc/hosts 下，DNS配置文件在 /etc/resolv.conf

```
//修改网卡配置，vim  /etc/sysconfig/network-script/ifcfg-xx      
DEVICE=eth0                         //设备名称      
ONBOOT=yes|no                       //是否启用该设备      
BOOTPROTO=none|static|dhcp          //手动(none/static)还是自动(dhcp)      
IPADDR1=192.168.182.123             //根据自动获取的IP配置      
PREFIX1=24                          //子网掩码      
IPADDR2=100.100.100.100             //第2个ip      
PREFIX2=24                          //第2个ip的掩码      
GATEWAY=192.168.182.1               //网关      1
TYPE="Ethernet"                     //网络类型      1
HWADDR=00:ff:ce:88:82:1d            //MAC地址      1
NAME="eth0"                         //连接名      1
DNS1=8.8.8.8                        //DNS1   会自动添加到 /etc/resolv.conf文件中      1
DNS2=114.114.114.114                //DNS2       1
//永久增加一个路由条目  vim /etc/sysconfig/network-script/route-xx  (网关地址必须是我们主机可达的)      1
10.1.0.0/8  via  192.168.10.10         1
//永久增加一个DNS解析，打开hosts文件      vim /etc/hosts      2
192.168.100.100   www.baidu.com       2
//永久增加一个DNS服务器，打开resolv.conf文件    vim /etc/resolv.conf      2
nameserver  8.8.8.8
```


### NetworkManager的使用

Rhel6和7中都采用了网络管理器(NetworkManager)管理网络( Rhel6中建议关闭NetworkManager,因为支持不完善，经常出错)，NetworkManager是一个动态的网络控制器与配置系统，它可在网络设备保持可用和连接时对设备进行操作。NetworkManager是服务名就是NetworkManager.service ，但是命令是 nmcli 。在Rhel7中，一个网卡设备可以有多个connection连接，但是同一时刻只能启用其中一个connection连接。每新建一个连接，都会在 /etc/sysconfig/network-scripts/ 目录下新建文件。这样的好处是针对一个网络接口，可以设置多个网络连接，比如静态IP和动态IP，再根据需要激活响应的connection连接。（connection连接就相当于是对网卡的一个配置，多个connection就是多个配置，但是同一时刻只能激活一个配置）

NetworkManager提供的工具：

*   nmcli (network manager command line interface) 
*   nmtui (network manager text user interface)
*   nm-connect-editor（network manager-connect-editor）

NetworkManager的使用：

*   查看所有连接：nmcli  connection  show 
*   查看某个连接具体信息：nmcli  connection  show  xie
*   查看所有网卡物理状态：nmcli  device status
*   查看所有网卡配置：nmcli  device  show
*   查看指定网卡配置：nmcli  device  show  eth0    或   ifconfig  eth0
*   重启网卡：  systemctl  restart  network  或  nmcli  connection reload;nmcli connection down xie;nmcli connection up xie

**connection连接的设置(Rhel7)：**

*   为网卡增加新的连接：nmcli  connection  add  con-name  eth0-manual  ifname  eth0  type  ethernet  ip4  192.168.20.20/24  gw4  192.168.20.254        //建立连接的同时指定ip和网关，ipv4.method=manual
*   为网卡增加新的连接：nmcli  conection add  con-name  eth0-auto  ifname eth0  type  ethernet   //建立连接但是不指定ip和网段，自动获取ip和网关 , ipv4.method=auto
*   删除一个连接：nmcli connection  delete  eth0-auto
*   修改连接的ip：nmcli  connection  modify   eth0-auto  ipv4.addresses  192.168.20.100/24
*   给连接增加一个从ip：nmcli  connection  modify   eh0-auto   +ipv4.addresses  192.168.20.200/24
*   给连接增加DNS服务器：nmcli  connection  modify  eth0-auto  ipv4.dns  192.168.20.254
*   激活连接：nmcli  connection  up  eth0-auto
*   关闭连接：nmcli  connection  down eth0-auto

### Team网卡绑定

在 linux 中, Rhel7 之前都是使用 bond 机制来实现多网络绑定同一个 IP 地址,来对网络提供访问,并按不同的模式来负载均衡或者轮回接替管理处理数据。而到了 Rhel7 之后，提供了一种强大的工具， nmcli 工具命令,使用此工具,将会根据命令参数的配置来重新生成特定的配置文件来供网络接口使用,方便而又灵活。在 Rhel7中，不再使用 bond 机制定义,而是使用网路组 team 机制,把 team 组当作一个设备

Team网络组的模式：

*   broadcast：每个网卡都传送数据包
*   roundrobin：每个网卡轮询方式传送数据
*   activebackup：网卡之间有主备，主传送数据包，备的不传送，主网卡down了，备才传送
*   loadbalance：监控流量并使用哈希函数，使得每个网卡传送的数据包达到相等
*   lacp：需要借助交换机，实现高级的负载均衡

Team的配置：

*   创建一个Team设备，命名为team0：  nmcli  connection  add   type   team   con-name   xie    ifname   team0   config   ' { "runner":{" name " : " loadbalance " } }'
*   设置team0组的ip地址获取方式： nmcli connection modify xie ipv4.method manual
*   设置team0组的ip、网关等信息： nmcli connection modify xie ipv4.addresses  192.168.100.100/24  ipv4.gateway 192.168.100.254
*   将指定网卡加入team0中： nmcli  connection  add type  team-slave  connection-name  team0-port1  ifname  eth1  masher   team0
*   开启/关闭 team0：nmcli  connection  up/down  team0
*   查看team0状态：teamdctl  state  team0

管理Team0的网络组成员：

*   从team0中移除eth1： nmcli  device  disconnect  eth1
*   从team0中增加eth1： nmcli   device  connection  eth1
*   将team0组中的port1关闭：nmcli  connection  down  team0-port1
*   将team0组中的port1开启：nmcli  connection  up  team0-port1   

### **Centos6.5、Redhat7、Kali网卡配置的不同**

Centos6.5 和 Redhat7 中网络服务是 network ，而[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020)中是 networking 。

首先三者都有 NetworkManager管理工具，但是Centos6.5中支持的不好，经常容易出错，所以建议在Centos6.5中关闭NetworkManager。  
  
Centos6.5和Redhat7中的网卡的配置文件都是在目录：/etc/sysconfig/network-scripts/  下，每个连接都有对应 ifcfg-连接名 的配置文件。因为Centos6.5中建议关闭NetworkManager管理工具，所以Centos6.5中的网卡配置文件就是  ifcfg-eth0 ，而Redhat7中的话，每个连接都会在该目录下有一个 ifcfg-连接名  的配置文件  
而kali的网卡配置文件则是：/etc/network/interfaces  这个文件，这个文件用于描述主机中所有的网络接口的信息，不论Linux主机中使用DHCP获取ip地址还是配置使用静态ip地址，都需要对该文件进行修改。

### Kali桥接模式配置静态ip

修改网卡配置文件：vim  /etc/network/interfaces

![](https://img-blog.csdnimg.cn/20190916103321137.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改dns配置文件：vi /etc/resolv.conf

![](https://img-blog.csdnimg.cn/20190916103445619.png)

重启网络：service networking restart

防止网卡没开，再开一遍：ifup eth0

相关文章：[Redhat中网络启动错误解决办法( Failed to start LSB: Bring up/down networking RTNETLINK answers: File exists)](https://blog.csdn.net/qq_36119192/article/details/82895241)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中