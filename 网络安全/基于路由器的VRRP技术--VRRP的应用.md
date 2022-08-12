**目录**

[无Vlan的VRRP](#t0)

[有Vlan的VRRP](#t1)

* * *

今天要讲的VRRP都是基于[路由器](https://so.csdn.net/so/search?q=%E8%B7%AF%E7%94%B1%E5%99%A8&spm=1001.2101.3001.7020)的VRRP。

### 一：无Vlan的VRRP

如图，PC1和PC2是企业内网主机，AR1和AR2是企业访问[外网](https://so.csdn.net/so/search?q=%E5%A4%96%E7%BD%91&spm=1001.2101.3001.7020)的路由器，有一个默认路由指向AR3。AR1和AR2之间形成第一跳网关冗余。PC1通过AR1以NAT方式访问外网，PC2通过AR2以NAT方式访问外网。AR1、AR2之间任何一个路由器宕机都不影响PC1和PC2访问外网。AR1、AR2、AR3之间运行OSPF协议。

技术要点：AR1和AR2的g0/0/0接口配置两个ip地址，再结合VRRP技术

![](https://img-blog.csdn.net/20180818192803349?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### PC1

```
PC>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-8E-41-17
```


### PC2

```
PC>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-8E-41-17
```


### AR1

```
[AR1]display current-configuration       
#      
acl number 2000                             //匹配需要NAT的网段      
 rule 5 permit source 192.168.10.0 0.0.0.255       
 rule 10 permit source 192.168.20.0 0.0.0.255       
#      
interface GigabitEthernet0/0/0      
 ip address 192.168.10.1 255.255.255.0       
 ip address 192.168.20.1 255.255.255.0 sub       //一个接口配两个地址      1
 vrrp vrid 10 virtual-ip 192.168.10.254      1
 vrrp vrid 10 priority 150                       //设置优先级      1
 vrrp vrid 10 preempt-mode timer delay 10        //抢占时间10秒      1
 vrrp vrid 10 track interface GigabitEthernet0/0/1 reduced 60 //端口状态监测      1
 vrrp vrid 10 authentication-mode md5 %$%$7_&c4,7Hl+O8!oBync[2o~1,%$%$  //认证      1
 vrrp vrid 20 virtual-ip 192.168.20.254      1
 vrrp vrid 20 authentication-mode md5 %$%$l,rF;gtS<J9zB}O1H:CMo~dG%$%$      1
#      1
interface GigabitEthernet0/0/1      1
 ip address 13.1.1.1 255.255.255.0       2
 nat outbound 2000                               // NAT      2
#      2
ospf 100 router-id 13.1.1.1       2
 area 0.0.0.0       2
  network 13.1.1.1 0.0.0.0       2
#      2
ip route-static 0.0.0.0 0.0.0.0 13.1.1.3      2
#
```


### AR2

```
[AR2]display current-configuration       
#      
acl number 2000        
 rule 5 permit source 192.168.10.0 0.0.0.255       
 rule 10 permit source 192.168.20.0 0.0.0.255       
#      
interface GigabitEthernet0/0/0      
 ip address 192.168.10.2 255.255.255.0       
 ip address 192.168.20.2 255.255.255.0 sub      1
 vrrp vrid 10 virtual-ip 192.168.10.254      1
 vrrp vrid 10 authentication-mode md5 %$%$RcPz'1Xr;Gj1#=~x*hC5o@SW%$%$      1
 vrrp vrid 20 virtual-ip 192.168.20.254      1
 vrrp vrid 20 priority 150      1
 vrrp vrid 20 preempt-mode timer delay 10      1
 vrrp vrid 20 track interface GigabitEthernet0/0/1 reduced 60      1
 vrrp vrid 20 authentication-mode md5 %$%$~B9v,^2%6KSZB4@WB#DTo@NI%$%$      1
#      1
interface GigabitEthernet0/0/1      1
 ip address 23.1.1.2 255.255.255.0       2
 nat outbound 2000      2
#      2
ospf 100 router-id 23.1.1.2       2
 area 0.0.0.0       2
  network 23.1.1.2 0.0.0.0       2
#      2
ip route-static 0.0.0.0 0.0.0.0 23.1.1.3      2
#
```


### AR3

```
[AR3]display current-configuration       
#      
interface GigabitEthernet0/0/0      
 ip address 13.1.1.3 255.255.255.0       
#      
interface GigabitEthernet0/0/1      
 ip address 23.1.1.3 255.255.255.0       
#      
ospf 100 router-id 33.3.3.3       1
 area 0.0.0.0       1
  network 13.1.1.3 0.0.0.0       1
  network 23.1.1.3 0.0.0.0       1
#
```


### 有Vlan的VRRP

如图，PC1和PC2是企业内网主机，分别属于Vlan10和Vlan20，AR1和AR2是企业访问外网的路由器，有一个[默认路由](https://so.csdn.net/so/search?q=%E9%BB%98%E8%AE%A4%E8%B7%AF%E7%94%B1&spm=1001.2101.3001.7020)指向AR3。AR1和AR2之间形成第一跳网关冗余。PC1通过AR1以NAT方式访问外网，PC2通过AR2以NAT方式访问外网。AR1、AR2之间任何一个路由器宕机都不影响PC1和PC2访问外网。AR1、AR2、AR3之间运行OSPF协议。

技术要点：AR1和AR2配置虚拟地址，再结合VRRP技术

![](https://img-blog.csdn.net/2018081819542922?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### PC1

```
PC>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-8E-41-17      
Vlan..............................: 10
```


### PC2

```
PC>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-8E-41-17      
Vlan..............................: 20
```


### LSW1

```
[LSW1]display current-configuration       
#      
vlan batch 10 20      
#      
interface GigabitEthernet0/0/1      
 port link-type trunk      
 port trunk allow-pass vlan 10 20      
#      
interface GigabitEthernet0/0/2      1
 port link-type trunk      1
 port trunk allow-pass vlan 10 20      1
#      1
interface GigabitEthernet0/0/3      1
 port link-type access      1
 port default vlan 10      1
#      1
interface GigabitEthernet0/0/4      1
 port link-type access      1
 port default vlan 20      2
#
```


### AR1

```
[AR1]display current-configuration       
#      
acl number 2000        
 rule 5 permit source 192.168.10.0 0.0.0.255       
 rule 10 permit source 192.168.20.0 0.0.0.255       
#      
interface GigabitEthernet0/0/0.10      
 dot1q termination vid 10                         //使虚拟接口能识别Vlan10      
 ip address 192.168.10.1 255.255.255.0       1
 vrrp vrid 10 virtual-ip 192.168.10.254      1
 vrrp vrid 10 priority 150      1
 vrrp vrid 10 preempt-mode timer delay 10      1
 vrrp vrid 10 track interface GigabitEthernet0/0/1 reduced 60      1
 vrrp vrid 10 authentication-mode md5 %$%$}'&x<I!T>,WFD'08G=yToU,o%$%$      1
 arp broadcast enable                            //虚拟接口恩给你发送arp广播      1
#      1
interface GigabitEthernet0/0/0.20      1
 dot1q termination vid 20                        //使虚拟接口能识别Vlan20      1
 ip address 192.168.20.1 255.255.255.0       2
 vrrp vrid 20 virtual-ip 192.168.20.254      2
 vrrp vrid 20 authentication-mode md5 %$%$<w9#Ez1k!~Y3+},DQtjGoUQ^%$%$      2
 arp broadcast enable      2
#      2
interface GigabitEthernet0/0/1      2
 ip address 13.1.1.1 255.255.255.0       2
 nat outbound 2000      2
#      2
ospf 100 router-id 13.1.1.1       2
 area 0.0.0.0       3
  network 13.1.1.1 0.0.0.0       3
#      3
ip route-static 0.0.0.0 0.0.0.0 13.1.1.3      3
#
```


### AR2

```
[V200R003C00]      
#      
acl number 2000        
 rule 5 permit source 192.168.10.0 0.0.0.255       
 rule 10 permit source 192.168.20.0 0.0.0.255       
#      
interface GigabitEthernet0/0/0.10      
 dot1q termination vid 10      
 ip address 192.168.10.2 255.255.255.0       1
 vrrp vrid 10 virtual-ip 192.168.10.254      1
 vrrp vrid 10 authentication-mode md5 %$%$ei}p>OY2kXEOij8TquxQoV*0%$%$      1
 arp broadcast enable      1
#      1
interface GigabitEthernet0/0/0.20      1
 dot1q termination vid 20      1
 ip address 192.168.20.2 255.255.255.0       1
 vrrp vrid 20 virtual-ip 192.168.20.254      1
 vrrp vrid 20 priority 150      1
 vrrp vrid 20 preempt-mode timer delay 10      2
 vrrp vrid 20 track interface GigabitEthernet0/0/1 reduced 60      2
 vrrp vrid 20 authentication-mode md5 %$%$&G-7Us\hbQxS@vFhpdoRoV"8%$%$      2
 arp broadcast enable      2
#      2
interface GigabitEthernet0/0/1      2
 ip address 23.1.1.2 255.255.255.0       2
 nat outbound 2000      2
#      2
ospf 100 router-id 23.1.1.2       2
 area 0.0.0.0       3
  network 23.1.1.2 0.0.0.0       3
#      3
ip route-static 0.0.0.0 0.0.0.0 23.1.1.3      3
#
```


### AR3

```
[AR3]display current-configuration       
#      
interface GigabitEthernet0/0/0      
 ip address 13.1.1.3 255.255.255.0       
#      
interface GigabitEthernet0/0/1      
 ip address 23.1.1.3 255.255.255.0       
#      
ospf 100 router-id 33.3.3.3       1
 area 0.0.0.0       1
  network 13.1.1.3 0.0.0.0       1
  network 23.1.1.3 0.0.0.0       1
#
```


附上存在Vlan时的ensp源文件：链接: [https://pan.baidu.com/s/1S5xzALnyg8cmn\_lTB\_jd\_w](https://pan.baidu.com/s/1S5xzALnyg8cmn_lTB_jd_w) 密码: yez5

基于防火墙的VRRP--------------->[基于防火墙的VRRP技术--华为防火墙双击热备----VGMP](https://blog.csdn.net/qq_36119192/article/details/81776153)
-----------------------------------------------------------------------------------------------------------------------

基于三层交换机的VRRP -------->[基于三层交换机的VRRP技术--MSTP、VRRP的综合运用](https://blog.csdn.net/qq_36119192/article/details/81806299)
------------------------------------------------------------------------------------------------------------------