**目录**

[配置防火墙使之禁止访问 ftp 服务](#t0)

[配置防火墙使得指定网段的才可以访问FTP服务](#t1)

[禁止PING](#t2)

[按网段禁止Ping](#t3)

[禁止Telnet服务](#t4)

[按网段禁止Telnet服务](#t5)

* * *

![](https://img-blog.csdn.net/2018091415134829?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

先说明一下环境，这里有四台主机，中间的Centos充当防火墙。右上角的win XP和右下角的Rhel7充当服务器，最左边的win7充当主机。四者之间的网卡都已经配置好。而且我们已经在Centos6.5上开启了[端口转发](https://so.csdn.net/so/search?q=%E7%AB%AF%E5%8F%A3%E8%BD%AC%E5%8F%91&spm=1001.2101.3001.7020)功能。

*   echo  1 > /proc/sys/net/ipv4/ip\_forward  （临时开启）
*   sysctl  -w  net.ipv4.ip\_forward=1  (临时开启)
*   vim  /etc/sysctl.conf  , 将net.ipv4.ip\_forward=0 改为 =1，然后 sysctl -p   /etc/sysctl.conf 使之生效 (永久开启IP转发)

防火墙开启了IP转发功能后，然后清空[iptables](https://so.csdn.net/so/search?q=iptables&spm=1001.2101.3001.7020)里面的所有规则，放行所有。win7、winXP、Rhel7都把防火墙给关了，然后四者之间就可以相互通信了。

### **配置防火墙使之禁止访问 ftp 服务**

我们在win xp上开启ftp服务，通过win7 和 Rhel7 可以 ftp WinXP。

![](https://img-blog.csdn.net/20180914155425205?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180914160625682?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

现在我们要配置防火墙，使之禁止FTP服务的流量经过。因为Win7要想FTP访问WinXP，流量先经过防火墙，然后再由防火墙转发。所以我们对防火墙的 filter 表的 FORWARD 链进行配置，使之拒绝FTP流量经过

```
iptables -t filter -A FORWARD -p tcp --dport 21 -j DROP
```


![](https://img-blog.csdn.net/20180914155849149?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，filter表中的FORWARD链已经加了一条拒绝TCP的21号端口通过的 规则了。然后我们再从Win7 FTP WinXP，可以看到，已经不能FTP了。

![](https://img-blog.csdn.net/20180914160125527?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/2018091416072014?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **配置防火墙使得指定网段的才可以访问FTP服务**

我们在防火墙上配置，使得Rhel7的才可以访问FTP服务，而Win7不能访问FTP服务。

```
iptables -t filter -A FORWARD -s 10.0.0.2  -p tcp --dport 21  -j ACCEPT   //filter表的FORWARD链允许源地址10.0.0.2的主机，目的端口21号的流量      
iptables -t filter -A FORWARD -s 192.168.1.2 -p tcp --dport 21 -j DROP  //filter表的FORWAARD链拒绝源地址192.168.1.2的主句，目的端口21号的流量
```


![](https://img-blog.csdn.net/20180914164434978?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，filter表中的FORWARD链默认拒绝所有流量经过，放行源地址分别为10.0.0.0/24和20.0.0.0/24的ftp服务。

![](https://img-blog.csdn.net/20180914164457765?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/2018091416361968?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **禁止PING**

在防火墙上配置丢弃 icmp 协议的 request 包，也就是icmp-type为8的包

```
iptables -A FORWARD -p icmp --icmp-type 8 -j DROP
```


![](https://img-blog.csdn.net/20180914165417435?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，已经ping不通了，Windows系统显示请求超时，而Linux系统则发不出去包

![](https://img-blog.csdn.net/20180914165555411?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/2018091417004472?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

而当我们配置的是拒绝icmp协议的包通过

```
iptables -A FORWARD -p icmp --icmp-type 8 -j REJECT
```


![](https://img-blog.csdn.net/20180914165825225?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

拒绝的话，windows系统和Linux系统都是显示目标端口不可达。

![](https://img-blog.csdn.net/20180914165923213?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180914165938715?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **按网段禁止Ping**

配置防火墙，丢弃192.168.1.0/24网段的icmp请求包，允许10.0.0.0/24网段的icmp请求包

```
iptables -A FORWARD -s 192.168.1.0/24 -p icmp --icmp-type 8 -j DROP      
iptables -A FORWARD -s 10.0.0.0/24 -p icmp --icmp-type 8 -j ACCEPT
```


![](https://img-blog.csdn.net/20180914171624316?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，Win7已经不能ping了，而Rhel7可以ping

![](https://img-blog.csdn.net/20180914171910782?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180914171857676?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **禁止Telnet服务**

其他的和上面的一模一样，只是过滤规则的端口改了

```
iptables -A FORWARD -p tcp --dport 23 -j DROP
```


### **按网段禁止Telnet服务**

```
iptables -A FORWARD  -s  192.168.1.0/24  -p tcp --dport 23  -j DROP      
iptables -A FORWARD  -s  20.0.0.0/24  -p  tcp  --dport 24 -j ACCEPT
```
