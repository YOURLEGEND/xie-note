**MSTP** (多生成树) 每个VLAN或者几个VLAN拥有一颗生成树，基于实例的生成树。instance 1、instance 2 每个实例拥有一颗生成树。MSTP可以实现多VLAN 的负载分担，可以实现多厂商对接。

**VRRP**虚拟路由冗余协议(Virtual Router Redundancy Protocol)是由IETF提出的解决局域网中配置静态网关出现单点失效现象的路由协议。

**注意**：当MSTP 和VRRP共同存在时，我们必须要做MSTP的区域设置以及实例优先级，网络中二层MSTP的Vlan主根和三层VRRP的master网关要放在同一台设备上。在下面的图中，我们把Vlan10的instance 1的根桥设置为LSW1，Vlan10的网关设备也设置为LSW1。Vlan20的instance 2的根桥设置为LSW2，Vlan20的网关设备也为LSW2。

![](https://img-blog.csdn.net/20180818103429329?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如图，PC1、PC2是企业内网的主机，分别属于VLAN10和VLAN20，LSW1和LSW2之间的g0/0/2口和g0/0/3口之间形成链路聚合。AR1是企业边界路由器，用于和外网通信。AR1、LSW1和LSW2之间运行ospf协议。LSW1、LSW2和LSW3之间运行MSTP协议。要让PC1、PC2出去和回来的流量方向一致，且形成第一跳网关冗余，且负载均衡。如下是配置

### PC1

```
PC>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-55-50-74      
VLAN..............................: 10
```


### PC2

```
PC>ipconfig       
IPv4 address......................: 192.168.20.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.20.254      
Physical address..................: 54-89-98-85-59-50      
Vlan..............................: 20
```


### LSW1

```
[LSW1]display current-configuration       
#      
vlan batch 10 20 100      
#      
stp instance 1 priority 4096      
stp instance 2 priority 8192      
#      
stp region-configuration      
 region-name MSTP      1
 instance 1 vlan 10      1
 instance 2 vlan 20      1
 active region-configuration      1
#      1
interface Vlanif10      1
 ip address 192.168.10.1 255.255.255.0      1
 vrrp vrid 10 virtual-ip 192.168.10.254      1
 vrrp vrid 10 priority 150      1
 vrrp vrid 10 preempt-mode timer delay 10      1
 vrrp vrid 10 track interface GigabitEthernet0/0/4 reduced 60      2
 vrrp vrid 10 authentication-mode md5 GX2lR,}C+Ky~t+Byca8YR#(#      2
#      2
interface Vlanif20      2
 ip address 192.168.20.1 255.255.255.0      2
 vrrp vrid 20 virtual-ip 192.168.20.254      2
 vrrp vrid 20 authentication-mode md5 L>YK#S.jKJu:|l#3M^#3"$f#      2
#      2
interface Vlanif100      2
 ip address 192.168.30.1 255.255.255.0      2
#      3
interface Eth-Trunk1      3
 port link-type trunk      3
 port trunk allow-pass vlan 10 20      3
#      3
interface GigabitEthernet0/0/1      3
 port link-type trunk      3
 port trunk allow-pass vlan 10 20      3
#      3
interface GigabitEthernet0/0/2      3
 eth-trunk 1      4
#      4
interface GigabitEthernet0/0/3      4
 eth-trunk 1      4
#      4
interface GigabitEthernet0/0/4      4
 port link-type access      4
 port default vlan 100      4
#      4
ospf 100 router-id 192.168.30.1      4
 import-route direct route-policy POLICY      5
 area 0.0.0.0      5
  network 192.168.30.1 0.0.0.0      5
#      5
route-policy POLICY permit node 10      5
 if-match ip-prefix F      5
 apply cost 10      5
#      5
route-policy POLICY permit node 20      5
 if-match ip-prefix F2      5
 apply cost 20      6
#      6
ip ip-prefix F index 10 permit 192.168.10.0 24      6
ip ip-prefix F2 index 10 permit 192.168.20.0 24      6
#
```


### LSW2

```
[LSW2]display current-configuration       
#      
vlan batch 10 20 100      
#      
stp instance 1 priority 8192      
stp instance 2 priority 4096      
#      
stp region-configuration      
 region-name MSTP      1
 instance 1 vlan 10      1
 instance 2 vlan 20      1
 active region-configuration      1
#      1
interface Vlanif10      1
 ip address 192.168.10.2 255.255.255.0      1
 vrrp vrid 10 virtual-ip 192.168.10.254      1
 vrrp vrid 10 authentication-mode md5 CN7EFU,9+,'eKRQqbl+O/%;#      1
#      1
interface Vlanif20      2
 ip address 192.168.20.2 255.255.255.0      2
 vrrp vrid 20 virtual-ip 192.168.20.254      2
 vrrp vrid 20 priority 150      2
 vrrp vrid 20 preempt-mode timer delay 60      2
 vrrp vrid 20 track interface GigabitEthernet0/0/4 reduced 60      2
 vrrp vrid 20 authentication-mode md5 :||$@<`|wYu:|l#3M^#36%L#      2
#      2
interface Vlanif100      2
 ip address 192.168.40.2 255.255.255.0      2
#      3
interface Eth-Trunk1      3
 port link-type trunk      3
 port trunk allow-pass vlan 10 20      3
#      3
interface GigabitEthernet0/0/1      3
 port link-type trunk      3
 port trunk allow-pass vlan 10 20      3
#      3
interface GigabitEthernet0/0/2      3
 eth-trunk 1      4
#      4
interface GigabitEthernet0/0/3      4
 eth-trunk 1      4
#      4
interface GigabitEthernet0/0/4      4
 port link-type access      4
 port default vlan 100      4
#      4
ospf 100 router-id 192.168.40.2      4
 import-route direct route-policy POLICY      5
 area 0.0.0.0      5
  network 192.168.40.2 0.0.0.0      5
#      5
route-policy POLICY permit node 10      5
 if-match ip-prefix F      5
 apply cost 20      5
#      5
route-policy POLICY permit node 20      5
 if-match ip-prefix F2      5
 apply cost 10      6
#      6
ip ip-prefix F index 10 permit 192.168.10.0 24      6
ip ip-prefix F2 index 10 permit 192.168.20.0 24      6
#
```


### LSW3

```
[LSW3]display current-configuration       
#      
vlan batch 10 20      
#      
stp region-configuration      
 region-name MSTP      
 instance 1 vlan 10      
 instance 2 vlan 20      
 active region-configuration      1
#      1
interface GigabitEthernet0/0/1      1
 port link-type trunk      1
 port trunk allow-pass vlan 10 20      1
#      1
interface GigabitEthernet0/0/2      1
 port link-type trunk      1
 port trunk allow-pass vlan 10 20      1
#      1
interface GigabitEthernet0/0/3      2
 port link-type access      2
 port default vlan 10      2
 stp edged-port enable      2
#      2
interface GigabitEthernet0/0/4      2
 port link-type trunk      2
 port trunk allow-pass vlan 20      2
 stp edged-port enable      2
#
```


### AR1

```
[AR1]display current-configuration      
#      
interface GigabitEthernet0/0/0      
 ip address 192.168.30.2 255.255.255.0       
#      
interface GigabitEthernet0/0/1      
 ip address 192.168.40.1 255.255.255.0       
#      
ospf 100 router-id 111.1.1.1       1
 area 0.0.0.0       1
  network 192.168.30.2 0.0.0.0       1
  network 192.168.40.1 0.0.0.0       1
#
```


附上ensp源文件：链接: [https://pan.baidu.com/s/1WhN79Z7b8XviOmmFAi5WyQ](https://pan.baidu.com/s/1WhN79Z7b8XviOmmFAi5WyQ) 密码: 8qkg

基于路由器的VRRP---------->[基于路由器的VRRP技术---VRRP的应用](https://blog.csdn.net/qq_36119192/article/details/81569790)
---------------------------------------------------------------------------------------------------------

基于防火墙的VRRP --------->[基于防火墙的VRRP技术--华为防火墙双机热备--VGMP](https://blog.csdn.net/qq_36119192/article/details/81776153)
----------------------------------------------------------------------------------------------------------------