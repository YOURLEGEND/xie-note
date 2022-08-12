**目录**

[主备备份双机热备配置](#%E4%B8%BB%E5%A4%87%E5%A4%87%E4%BB%BD%E5%8F%8C%E6%9C%BA%E7%83%AD%E5%A4%87%E9%85%8D%E7%BD%AE)

[负载分担双机热备配置](#%E8%B4%9F%E8%BD%BD%E5%88%86%E6%8B%85%E5%8F%8C%E6%9C%BA%E7%83%AD%E5%A4%87%E9%85%8D%E7%BD%AE)

* * *

为了解决多个VRRP备份组状态不一致的问题，华为防火墙引入VGMP（VRRP Group Management Protocol）来实现对VRRP备份组的统一管理，保证多个VRRP备份组状态的一致性。我们将防火墙上的所有VRRP备份组都加入到一个VGMP组中，由VGMP组来集中监控并管理所有的VRRP备份组状态。如果VGMP组检测到其中一个VRRP备份组的状态变化，则VGMP组会控制组中的所有VRRP备份组统一进行状态切换，保证各VRRP备份组状态的一致性。双机热备协议HRP（Huawei Redundancy Protocol）协议是承载在VGMP(Vrrp Group Management Protocol)报文上进行传输的，在Master和Backup防火墙设备之间备份关键配置命令和会话表状态信息，是双机热备协议。VGMP、HRP是华为私有协议

主备备份双机热备配置
----------

在主备备份双机热备模式下，FW1既是PC1的网关也是PC2的网关，也是AR1回去的路由的默认下一跳。在主备备份双机热备模式下，假如FW1宕机了，FW2会立刻切换到Active状态，从而不影响主机与外界通信。

如图，PC1和PC2是企业内网的主机，属于不同的VLAN。AR1是边界路由器。FW1和FW2是企业内部防火墙，他们之间形成双机热备，FW1是active状态，FW2是standby状态。企业内网通过AR1的出端口通过NAT方式访问外网。在防火墙FW1上还配置链路追踪功能，追踪AR2的g0/0/0口，如果到达不了，则切换状态。AR1下面的配置

![](https://img-blog.csdn.net/20180818142322619?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### PC1

```
PC>ipconfig       
IPv4 address......................: 192.168.1.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.1.254      
Physical address..................: 54-89-98-30-73-39      
Vlan .............................: 10
```


### PC2

```
PC>ipconfig       
IPv4 address......................: 192.168.2.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.2.254      
Physical address..................: 54-89-98-AF-62-C9      
Vlan..............................: 20
```


### LSW1

```
#      
vlan batch 10 20      
#      
interface GigabitEthernet0/0/1      
 port link-type trunk      
 port trunk allow-pass vlan 10 20      
#      
interface GigabitEthernet0/0/2      
 port link-type trunk      1
 port trunk allow-pass vlan 10 20      1
#      1
interface GigabitEthernet0/0/3      1
 port link-type access      1
 port default vlan 10      1
#      1
interface GigabitEthernet0/0/4      1
 port link-type access      1
 port default vlan 20      1
#
```


### FW1

```
#      
 hrp enable      
 hrp interface GigabitEthernet1/0/0 remote 10.1.1.2      
 hrp preempt delay 10      
 hrp track ip-link track      
#                                              
ip-link check enable                            
ip-link name track                              
 destination 20.0.0.2 mode icmp                 1
#                                                                                       1
interface GigabitEthernet1/0/0                  1
 undo shutdown                                  1
 ip address 10.1.1.1 255.255.255.252                                    1
#                                               1
interface GigabitEthernet1/0/1.10               1
 vlan-type dot1q 10                             1
 ip address 192.168.1.1 255.255.255.0           1
 vrrp vrid 10 virtual-ip 192.168.1.254 active      1
#                                               2
interface GigabitEthernet1/0/1.20               2
 vlan-type dot1q 20                             2
 ip address 192.168.2.1 255.255.255.0           2
 vrrp vrid 20 virtual-ip 192.168.2.254 active      2
#                                               2
interface GigabitEthernet1/0/2                  2
 undo shutdown                                  2
 ip address 192.168.3.1 255.255.255.0           2
 vrrp vrid 30 virtual-ip 192.168.3.254 active                                                                2
#                                               3
firewall zone trust                             3
 set priority 85                                3
 add interface GigabitEthernet0/0/0             3
 add interface GigabitEthernet1/0/1.10          3
 add interface GigabitEthernet1/0/1.20          3
#                                               3
firewall zone untrust                           3
 set priority 5                                 3
 add interface GigabitEthernet1/0/2                                                                            3
#                                               4
firewall zone name heartbeat id 4               4
 set priority 80                                4
 add interface GigabitEthernet1/0/0             4
#                                                                                                4
ip route-static 0.0.0.0 0.0.0.0 192.168.3.3      4
#                                                            4
#                                               4
security-policy                                 4
 rule name TRUST_UN_TRUST                       4
  source-zone trust                             5
  destination-zone untrust                      5
  service icmp                                  5
  action permit                                 5
#
```


### FW2

```
#      
 hrp enable      
 hrp interface GigabitEthernet1/0/0 remote 10.1.1.1      
 hrp preempt delay 10                     
#                                                                                       
interface GigabitEthernet1/0/1                  
 undo shutdown                                  
#                                               
interface GigabitEthernet1/0/1.10               1
 vlan-type dot1q 10                             1
 ip address 192.168.1.2 255.255.255.0           1
 vrrp vrid 10 virtual-ip 192.168.1.254 standby      1
#                                               1
interface GigabitEthernet1/0/1.20               1
 vlan-type dot1q 20                             1
 ip address 192.168.2.2 255.255.255.0           1
 vrrp vrid 20 virtual-ip 192.168.2.254 standby      1
#                                               1
interface GigabitEthernet1/0/2                  2
 undo shutdown                                  2
 ip address 192.168.3.2 255.255.255.0           2
 vrrp vrid 30 virtual-ip 192.168.3.254 standby                                                               2
#                                               2
firewall zone trust                             2
 set priority 85                                2
 add interface GigabitEthernet0/0/0             2
 add interface GigabitEthernet1/0/1.10          2
 add interface GigabitEthernet1/0/1.20          2
#                                               3
firewall zone untrust                           3
 set priority 5                                 3
 add interface GigabitEthernet1/0/2             3
#                                                                                                            3
firewall zone name heartbeat id 4               3
 set priority 80                                3
 add interface GigabitEthernet1/0/0                                                                        3
#                                               3
ip route-static 0.0.0.0 0.0.0.0 192.168.3.3      3
#                                                             4
security-policy                                 4
 rule name TRUST_UN_TRUST                       4
  source-zone trust                             4
  destination-zone untrust                      4
  service icmp                                  4
  action permit                                 4
#
```


### AR1

```
#      
acl number 2000        
 rule 5 permit source 192.168.2.0 0.0.0.255       
 rule 10 permit source 192.168.1.0 0.0.0.255       
#      
interface GigabitEthernet0/0/0      
 ip address 192.168.3.3 255.255.255.0       
#      
interface GigabitEthernet0/0/2      1
 ip address 20.0.0.1 255.255.255.0       1
 nat outbound 2000      1
#      1
ip route-static 192.168.1.0 255.255.255.0 192.168.3.254      1
ip route-static 192.168.2.0 255.255.255.0 192.168.3.254      1
#
```


### AR2

```
#      
interface GigabitEthernet0/0/0      
 ip address 20.0.0.2 255.255.255.0       
#
```


### 负载分担双机热备配置

在负载分担双机热备模式下，FW1是PC1的网关，FW2是PC2的网关。AR1回往PC1的下一跳是PC1，AR2回往PC2的下一跳是PC2。FW1和FW2之间形成负载分担双机热备关系，其实任何一个防火墙的接口down了，都不影响主机和外接通信。

如图，PC1和PC2是企业内网的主机，属于不同的VLAN。AR1是边界路由器。FW1和FW2是企业内部防火墙，他们之间形成双机热备，FW1既有active状态也有standby状态，FW2既有active状态也有standby状态。企业内网通过AR1的出端口通过NAT方式访问外网。

![](https://img-blog.csdn.net/20180818142418953?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### PC1

```
PC>ipconfig       
IPv4 address......................: 192.168.1.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.1.254      
Physical address..................: 54-89-98-30-73-39      
Vlan..............................: 10
```


### PC2

```
PC>ipconfig       
IPv4 address......................: 192.168.2.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.2.254      
Physical address..................: 54-89-98-AF-62-C9      
Vlan..............................: 20
```


### LSW1

```
[SW1]display current-configuration       
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
 stp edged-port enable      1
#      1
interface GigabitEthernet0/0/4      1
 port link-type access      2
 port default vlan 20      2
 stp edged-port enable      2
#
```


### FW1

```
HRP_M[FW1]display current-configuration       
#      
 hrp enable      
 hrp interface GigabitEthernet1/0/0 remote 10.1.1.2      
#      
interface GigabitEthernet1/0/1.10      
 vlan-type dot1q 10      
 ip address 192.168.1.1 255.255.255.0      
 vrrp vrid 10 virtual-ip 192.168.1.254 active      1
#      1
interface GigabitEthernet1/0/1.20      1
 vlan-type dot1q 20      1
 ip address 192.168.2.1 255.255.255.0      1
 vrrp vrid 20 virtual-ip 192.168.2.254 standby      1
#      1
interface GigabitEthernet1/0/2      1
 undo shutdown      1
 ip address 192.168.3.1 255.255.255.0      1
 vrrp vrid 30 virtual-ip 192.168.3.253 active      2
 vrrp vrid 40 virtual-ip 192.168.3.254 standby      2
#      2
firewall zone trust      2
 set priority 85      2
 add interface GigabitEthernet0/0/0      2
 add interface GigabitEthernet1/0/1.10      2
 add interface GigabitEthernet1/0/1.20      2
 add interface GigabitEthernet1/0/1      2
#      2
firewall zone untrust      3
 set priority 5      3
 add interface GigabitEthernet1/0/2      3
#      3
firewall zone name heartbeat id 4      3
 set priority 80      3
 add interface GigabitEthernet1/0/0      3
#      3
ip route-static 0.0.0.0 0.0.0.0 192.168.3.3      3
#      3
security-policy      4
 rule name TRUST_TO_UNTRUST      4
  source-zone trust      4
  destination-zone untrust      4
  service icmp      4
  action permit      4
#
```


### FW2

```
HRP_S[FW2]display current-configuration       
#      
 hrp enable      
 hrp interface GigabitEthernet1/0/0 remote 10.1.1.1      
#      
interface GigabitEthernet1/0/0      
 undo shutdown      
 ip address 10.1.1.2 255.255.255.252      
#      1
interface GigabitEthernet1/0/1.10      1
 vlan-type dot1q 10      1
 ip address 192.168.1.2 255.255.255.0      1
 vrrp vrid 10 virtual-ip 192.168.1.254 standby      1
#      1
interface GigabitEthernet1/0/1.20      1
 vlan-type dot1q 20      1
 ip address 192.168.2.2 255.255.255.0      1
 vrrp vrid 20 virtual-ip 192.168.2.254 active      1
#      2
interface GigabitEthernet1/0/2      2
 undo shutdown      2
 ip address 192.168.3.2 255.255.255.0      2
 vrrp vrid 30 virtual-ip 192.168.3.253 standby      2
 vrrp vrid 40 virtual-ip 192.168.3.254 active      2
#      2
firewall zone trust      2
 set priority 85      2
 add interface GigabitEthernet0/0/0      2
 add interface GigabitEthernet1/0/1      3
 add interface GigabitEthernet1/0/1.10      3
 add interface GigabitEthernet1/0/1.20      3
#      3
firewall zone untrust      3
 set priority 5      3
 add interface GigabitEthernet1/0/2      3
#      3
firewall zone name heartbeat id 4      3
 set priority 80      3
 add interface GigabitEthernet1/0/0      4
#      4
ip route-static 0.0.0.0 0.0.0.0 192.168.3.3      4
#      4
security-policy      4
 rule name TRUST_TO_UNTRUST      4
  source-zone trust      4
  destination-zone untrust      4
  service icmp      4
  action permit      4
#
```


### AR1

```
[AR1]display current-configuration       
#      
acl number 2000        
 rule 5 permit source 192.168.1.0 0.0.0.255       
 rule 10 permit source 192.168.2.0 0.0.0.255       
#      
interface GigabitEthernet0/0/0      
 ip address 192.168.3.3 255.255.255.0       
#      1
interface GigabitEthernet0/0/2      1
 ip address 20.0.0.1 255.255.255.0       1
 nat outbound 2000      1
#      1
ip route-static 192.168.1.0 255.255.255.0 192.168.3.253      1
ip route-static 192.168.2.0 255.255.255.0 192.168.3.254      1
#
```


### AR2

```
[AR2]display current-configuration       
#      
interface GigabitEthernet0/0/0      
 ip address 20.0.0.2 255.255.255.0       
#
```


VRRP和VGMP官方参考：[https://forum.huawei.com/enterprise/zh/thread-282263.html](https://forum.huawei.com/enterprise/zh/thread-282263.html)

附上负载分担模式下的ensp源文件：链接: [https://pan.baidu.com/s/1S5xzALnyg8cmn\_lTB\_jd\_w](https://pan.baidu.com/s/1S5xzALnyg8cmn_lTB_jd_w) 密码: yez5

基于路由器的 VRRP ------------>[基于路由器的VRRP技术--VRRP的应用](https://blog.csdn.net/qq_36119192/article/details/81569790)
------------------------------------------------------------------------------------------------------------

基于三层交换机的VRRP-------->[基于三层交换机的VRRP--MSTP、VRRP的综合运用](https://blog.csdn.net/qq_36119192/article/details/81806299)
---------------------------------------------------------------------------------------------------------------