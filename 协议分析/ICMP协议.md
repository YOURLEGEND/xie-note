**目录**

[ICMP](#t0)

[ping不通的几种情况](#t1)

[ICMP重定向](#t2)

[一个完整ICMP数据包的封装与解封装](#t3)

[ICMP攻击](#t4) 

[tracert路由追踪原理](#t5) 

* * *

ICMP
----

**ICMP**（Internet Control Message protocol）互联网信息控制协议，用于实现链路连通性测试和链路追踪，可以实现链路差错报告，属于[UDP](https://so.csdn.net/so/search?q=UDP&spm=1001.2101.3001.7020)协议。ICMP有多种类型的报文，同一类型的报文会有多个不同的Code。

ping、tracert 等命令的内部就是用的 icmp 协议。

![](https://img-blog.csdnimg.cn/20181102080212877.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ping不通的几种情况

*   当主机有一个默认网关时，如果他ping其他网段的地址，到不了的话，显示的是request timeout(此时他把icmp包发给网关，至于后面的事他就不管，如果没有包回应，就显示request timeout)
*   当一个主机没有默认网关时或者配置了网关但是和网关不通时，如果他ping其他网段的地址，显示的是Destination host unreachable（此时他发送arp请求包请求网关的mac地址）
*   当一个路由器ping他路由表中没有的地址时，显示的是request timeout(此时不发任何包)
*   当路由器ping一个路由表中存在地址时，如果没有回应，则显示的也是 reuqest timeout (此时发送arp请求包，请求目标ip的mac地址)

### ICMP重定向

ICMP[重定向](https://so.csdn.net/so/search?q=%E9%87%8D%E5%AE%9A%E5%90%91&spm=1001.2101.3001.7020)报文是ICMP控制报文中的一种。在特定的情况下，当路由器检测到一台机器使用非优化路由的时候，它会向该主机发送一个ICMP重定向报文，请求主机改变路由。路由器也会把初始数据包向它的目的地转发。  
发生ICMP重定向通常有两种情况：  
1)当路由器从某个接口收到数据还需要从相同接口转发该数据时；  
2)当路由器从某个接口收到发往远程网络的数据时发现源ip地址与下一跳属于同一网段

![](https://img-blog.csdnimg.cn/20181102080515189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181102080547593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 一个完整ICMP数据包的封装与解封装

![](https://img-blog.csdnimg.cn/20181102080731984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**在数据包的传送过程中，目标ip和源ip是一直不变的。目标mac和源mac是一直变化的**  
**PC1 ping PC2**

**PC1:** 

*   step1：  发送arp广播包，请求网关的mac地址
*   step2:   封装并发送数据包，目标ip: 11.11.11.11   源ip: 10.10.10.10    目标mac：MAC-AR1-0/0/0  源MAC： MAC-pc

**AR1:**

*   step1: 接受数据包，发现数据包的目标mac是自己，解封二层数据，露出三层IP信息。
*   step2: 发送arp广播包，请求192.168.10.2的mac地址
*   step3: 重新封装数据包，目标ip: 11.11.11.11   源ip: 10.10.10.10    目标mac: MAC-AR2-0/0/1   源MAC： MAC-AR1-0/0/1

**AR2：**

*   step1: 接受数据包，发现数据包的目标mac是自己，解封二层数据，露出三层IP信息。
*   step2: 发送arp广播包，请求192.168.2.3的mac地址
*   step3: 重新封装数据包，目标ip: 11.11.11.11   源ip: 10.10.10.10    目标mac: MAC-AR3-0/0/0   源MAC： MAC-AR2-0/0/2

**AR4：**

*   step1: 接受数据包，发现数据包的目标mac是自己，解封二层数据，露出三层IP信息。
*   step2: 发送arp广播包，请求11.11.11.11的mac地址
*   step3: 重新封装数据包，目标ip: 11.11.11.11   源ip: 10.10.10.10    目标mac: MAC-PC2   源MAC： MAC-AR4-0/0/1

**PC2：**

*   step1: 接受数据包，发现数据包的目标mac是自己，解封三层和二层数据，得到数据
*   step2: 发送icmp回应包，目标ip:10.10.10.10   源ip: 11.11.11.11   目标mac: MAC-AR4-0/0/1  源MAC： MAC-PC2 

### ICMP攻击 

ICMP泛洪攻击是最常用的DOS(拒绝服务攻击)之一，顾名思义，就是对目标主机发送洪水般的ping包，使得目标主机忙于处理ping包而无能力处理其他正常请求，造成目标主机无法给其他主机提供服务，从而达到攻击的目的。如果ping包足够多，甚至会造成目标主机资源消耗殆尽而造成宕机。

我们可以使用Kali里面的 hping3 进行 icmp 攻击

```
hping3 --flood -1 --rand-source --icmptype 3 --icmpcode 3 192.168.10.1      
--flood 攻击模式，尽可能快的发送报文      
-1 发送icmp报文      
--ran-source 发送报文时，源IP为随机的IP地址      
--icmptype 3 表示ICMP报文类型为目的不可达      
--icmpcode 3 表示不可达报文为端口不可达
```


### tracert路由追踪原理 

tracert ： 探测本地到目的地的路径，内部调用了ICMP协议

![](https://img-blog.csdnimg.cn/20181102081111190.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181102081128507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**在R1和R2之间的链路之间抓包**  
R1发送的第一个探测报文，在发送的时候会发送三个，第一个探测报文的TTL值为1，第一跳的设备在收到TTL=1的报文后，会回复TTL超时报文

![](https://img-blog.csdnimg.cn/2018110208121583.png)

![](https://img-blog.csdnimg.cn/20181102081228399.png)

R1发送的第二个探测报文也一样，会发送三个，第二个探测报文的TTL值为2，第二跳的设备在收到TTL=2的报文后，也会回复TTL超时报文

![](https://img-blog.csdnimg.cn/20181102081247673.png)

![](https://img-blog.csdnimg.cn/20181102081302118.png)

![](https://img-blog.csdnimg.cn/20181102081314749.png)

一直这样，探测报文会一直发送，TTL值依次加1，直到发送到目标端，目标端会回复目标不可达报文，至此，完成探测

![](https://img-blog.csdnimg.cn/20181102081326379.png)

![](https://img-blog.csdnimg.cn/20181102081338967.png)

![](https://img-blog.csdnimg.cn/20181102081352550.png)