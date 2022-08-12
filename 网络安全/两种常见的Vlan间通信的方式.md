**目录**

[一：三层交换机方式](#t0)

[二：单臂路由方式](#t1)

* * *

一：三层交换机方式
=========

![](https://img-blog.csdn.net/20180815171158589?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

     如图，PC1和PC2是企业内网的主机，属于不同的部门，故属于不同的VLAN。在交换机上配置vlan 10和vlan 20，并且配上主机的网关。交换机与路由器相邻的接口配置上Vlan30，配置上和路由器接口同一网段的ip。在交换机上配置默认路由指向路由器。AR1是边界路由器，AR1上配置静态路由去往192.168.10.0网段和192.168.20.0网段。AR1还配了默认路由指向AR2。AR1与AR2通过OSPF路由。主机通过NAT方式以边界路由公网地址访问外网，故在边界路由AR1上配置PAT(端口多路复用)。以下是各设备的配置。

### PC1

```
PC1>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-20-70-11      
Vlan..............................: 10
```


### PC2

```
PC2>ipconfig       
IPv4 address......................: 192.168.20.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.20.254      
Physical address..................: 54-89-98-0C-28-2D      
Vlan..............................: 20
```


###  

### LSW1

```
[SW1]display current-configuration      
#      
vlan batch 10 20 30      
#                                               
interface Vlanif10                              
 ip address 192.168.10.254 255.255.255.0        
#                                               
interface Vlanif20                              
 ip address 192.168.20.254 255.255.255.0        1
#                                               1
interface Vlanif30                              1
 ip address 192.168.30.1 255.255.255.0                               1
#                                               1
interface GigabitEthernet0/0/1                  1
 port link-type access                          1
 port default vlan 10                           1
#                                               1
interface GigabitEthernet0/0/2                  1
 port link-type access                          2
 port default vlan 20                           2
#                                               2
interface GigabitEthernet0/0/3                  2
 port link-type access                          2
 port default vlan 30                           2
#                                               2
#                                             2
ip route-static 0.0.0.0 0.0.0.0 192.168.30.2      2
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
interface GigabitEthernet0/0/0                  
 ip address 192.168.30.2 255.255.255.0          
#                                               1
interface GigabitEthernet0/0/1                  1
 ip address 12.1.1.1 255.255.255.0              1
 nat outbound 2000                              1
#                                                                                     1
ospf 100 router-id 11.1.1.1                     1
 area 0.0.0.0                                   1
  network 12.1.1.1 0.0.0.0                      1
  network 192.168.30.2 0.0.0.0                  1
#                                               1
ip route-static 192.168.10.0 255.255.255.0 192.168.30.1      2
ip route-static 192.168.20.0 255.255.255.0 192.168.30.1      2
#
```


### AR2

```
[AR2]display current-configuration      
#                                         
interface GigabitEthernet0/0/0                  
 ip address 12.1.1.2 255.255.255.0                                        
#                                               
interface LoopBack0                             
 ip address 22.2.2.2 255.255.255.255            
#                                               
ospf 100 router-id 22.2.2.2                     1
 area 0.0.0.0                                   1
  network 12.1.1.2 0.0.0.0                      1
  network 22.2.2.2 0.0.0.0                      1
#
```


二：单臂路由方式
========

![](https://img-blog.csdn.net/20180815185552602?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如图，PC1和PC2是企业内网的主机，属于不同的部门，故属于不同的VLAN。他们之间通过路由器AR1单臂路由通信。他们通过路由器AR1的g0/0/1口以PAT(端口多路复用)方式与外界通信。AR1和AR2之间运行OSPF协议，AR1配置了指向AR2的默认路由。以下是各设备的配置

### PC1

```
PC1>ipconfig       
IPv4 address......................: 192.168.10.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.10.254      
Physical address..................: 54-89-98-20-70-11      
Vlan..............................: 10
```


### PC2

```
PC2>ipconfig       
IPv4 address......................: 192.168.20.10      
Subnet mask.......................: 255.255.255.0      
Gateway...........................: 192.168.20.254      
Physical address..................: 54-89-98-0C-28-2D      
Vlan..............................: 20
```


### SW1

```
[SW1]display current-configuration       
#      
vlan batch 10 20      
#                                                                                       
#                                               
interface GigabitEthernet0/0/1                  
 port link-type access                          
 port default vlan 10                           
#                                               1
interface GigabitEthernet0/0/2                  1
 port link-type access                          1
 port default vlan 20                           1
#                                               1
interface GigabitEthernet0/0/3                  1
 port link-type trunk                           1
 port trunk allow-pass vlan 10 20           1
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
 dot1q termination vid 10                       
 ip address 192.168.10.254 255.255.255.0        1
 arp broadcast enable                           1
#                                               1
interface GigabitEthernet0/0/0.20               1
 dot1q termination vid 20                       1
 ip address 192.168.20.254 255.255.255.0        1
 arp broadcast enable                           1
#                                               1
interface GigabitEthernet0/0/1                  1
 ip address 12.1.1.1 255.255.255.0              1
 nat outbound 2000                                                                                              2
#                                               2
ospf 100 router-id 11.1.1.1                     2
 area 0.0.0.0                                   2
  network 12.1.1.1 0.0.0.0                      2
#
```


### AR2

```
[AR2]display current-configuration                                    
#                                               
interface GigabitEthernet0/0/0                  
 ip address 12.1.1.2 255.255.255.0              
#                                                                                     
interface LoopBack0                             
 ip address 22.2.2.2 255.255.255.255            
#                                               
ospf 100 router-id 22.2.2.2                     1
 area 0.0.0.0                                   1
  network 12.1.1.2 0.0.0.0                      1
  network 22.2.2.2 0.0.0.0                      1
#
```
