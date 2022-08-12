**目录**

[Scapy](#t0)

[scapy的安装和使用](#t1) 

[发包](#t2)

[发包和收包](#t3) 

[抓包](#t4) 

[将抓取到的数据包保存](#t5)

[查看抓取到的数据包](#t6) 

[格式化输出](#t7)

[过滤抓包](#t8)

* * *

Scapy
=====

[scapy](https://so.csdn.net/so/search?q=scapy&spm=1001.2101.3001.7020)是python中一个可用于网络嗅探的非常强大的第三方库，可以用它来做 packet 嗅探和伪造 packet。 scapy已经在内部实现了大量的网络协议。如DNS、ARP、IP、TCP、UDP等等，可以用它来编写非常灵活实用的工具。

换言之，Scapy 是一个强大的操纵报文的交互程序。它可以伪造或者解析多种协议的报文，还具有发送、捕获、匹配请求和响应这些报文以及更多的功能。Scapy 可以轻松地做到像扫描(scanning)、路由跟踪(tracerouting)、探测(probing)、单元测试(unit tests)、攻击(attacks)和发现网络(network discorvery)这样的传统任务。它可以代替 hping 、arpspoof 、[arp](https://so.csdn.net/so/search?q=arp&spm=1001.2101.3001.7020)\-sk、arping,p0f 甚至是部分的Namp、tcpdump 和 tshark 的功能。

最简单的一个发包

```
from scapy.all import *      
data='hello,word!'      
pkt=IP(src='10.96.10.208',dst='10.96.10.209')/TCP(sport=12345,dport=12345)/data      
send(pkt,inter=1,count=5)  #每隔一秒发包，发5次
```


![](https://img-blog.csdnimg.cn/20181105225801781.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

scapy的安装和使用 
------------

scapy默认是不安装的，安装命令：pip  install   scapy 

安装完后： 

*   ls()  命令可以查看所有支持的协议
*   ls(IP) 命令列出ip协议头部字段格式，只要想查看哪个协议的参数，括号里就填哪个协议
*   IP().show()　列出ip包的信息
*   lsc() 命令列出scapy的所有命令 
*   conf 命令列出scapy 的配置参数

发包的包格式： 层的协议(参数)/上一层的协议(参数) /要发送的数据    ，比如如下

```
IP(src='10.96.10.208',dst='10.96.10.209')/TCP(sport=12345,dport=12345)/data
```


发包
--

发送三层包：send(pkt, inter=0, loop=0, count=1, iface=N)   
发送二层包：sendp(pkt, inter=0, loop=0, count=1, iface=N)   
使用tcpreplay在第二层以更快的速度发送数据包 ：sendpfast(pkt, pps=N, mbps=N, loop=0, iface=N) 

> def send(x, inter=0, loop=0, count=None, verbose=None, realtime=None, return\_packets=False, socket=None，\*args,\*\*kargs)

> def sendp(x, inter=0, loop=0, iface=None, iface\_hint=None, count=None, verbose=None, realtime=None,  
>           return\_packets=False, socket=None，\*args,\*\*kargs)

> def sendpfast(x, pps=None, mbps=None, realtime=None, loop=0, file\_cache=False, iface=None，\*args,\*\*kargs)

```
from scapy.all import *      
send(IP(dst="202.99.96.68")/UDP(dport=53))      
sendp(Ether()/IP(dst="202.99.96.68")/UDP(dport=53))
```


发包和收包 
------

和上面发包不同的是，发包仅仅是发送包，而发包和收包是对发包的回复信息进行收集。

*   三层发包和收包：sr(pkt, filter=N, iface=N)
*   二层发包和收包：srp(pkt, filter=N, iface=N) 
*   三层发包但是仅仅接受第一个回复：sr1(pkt, inter=0, loop=0, count=1, iface=N),
*   二层发包但是仅仅接受第一个回复：srp1(pkt, filter=N, iface=N) 
*   在环回口发包并且打印出所有的回复：srloop(pkt, timeout=N, count=N), srploop(…) 

> def sr(x, promisc=None, filter=None, iface=None, nofilter=0，\*args,\*\*kargs)

> def srp(x, promisc=None, iface=None, iface\_hint=None, filter=None, nofilter=0, type=ETH\_P\_ALL，\*args,\*\*kargs)

> def sr1(x, promisc=None, filter=None, iface=None, nofilter=0，\*args,\*\*kargs)

> def srp1(\*args,\*\*kargs) 

以上所有的发包和收包都是接收到两个参数，第一个参数是回复的包，第二个参数是没回复的包 

举例，我们利用arp协议获得指定ip的mac地址

```
>>ans,unans=srp(Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst="10.96.10.197"))      
>>print(ans)      
>>print(unans)      
<Results: TCP:0 UDP:0 ICMP:0 Other:1>      
<Unanswered: TCP:0 UDP:0 ICMP:0 Other:0>       
可知，ans是回复我们的包，如果我们想获得ans回复的数据，我们可以查看ans的第一个包      
>>print(ans[0])      
(<Ether  dst=ff:ff:ff:ff:ff:ff type=0x806 |<ARP  pdst=10.96.10.197 |>>, <Ether  dst=50:7b:9d:12:b2:ca src=e4:35:c8:7a:58:a6 type=0x806 |<ARP  hwtype=0x1 ptype=0x800 hwlen=6 plen=4 op=is-at hwsrc=e4:35:c8:7a:58:a6 psrc=10.96.10.197 hwdst=50:7b:9d:12:b2:ca pdst=10.96.10.208 |<Padding  load='\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00' |>>>)       1
ans[0]包中是一个元组，第一部分是我们发的信息，第二部分是回复的信息。很明显，第二部分才是我们要的内容      1
>>for snd,rcv in ans:      1
>>   list_mac=rcv.sprintf("%Ether.src% - %ARP.psrc%")     #按照指定格式打印出我们的包      1
>>   print(list_mac)      1
e4:35:c8:7a:58:a6 - 10.96.10.197
```


抓包 
---

scapy[抓包](https://so.csdn.net/so/search?q=%E6%8A%93%E5%8C%85&spm=1001.2101.3001.7020)使用 sniff() 函数，这个函数有很多参数

> def sniff(count=0, store=1, offline=None, prn=None,filter=None, L2socket=None, timeout=None, opened\_socket=None, stop\_filter=None, iface=None，\*args,\*\*kargs)

*   count：抓包的数量，0表示无限制；
*   store：保存抓取的数据包或者丢弃，1保存，0丢弃
*   offline：从 pcap 文件读取数据包，而不进行嗅探，默认为None
*   prn：为每一个数据包定义一个函数，如果返回了什么，则显示。例如：prn = lambda x: x.summary()； （  packct.summar()函数返回的是对包的统计性信息 ）
*   filter：过滤规则，使用wireshark里面的过滤语法
*   L2socket：使用给定的 L2socket
*   timeout：在给定的时间后停止嗅探，默认为 None
*   opened\_socket：对指定的对象使用 .recv() 进行读取；
*   stop\_filter：定义一个函数，决定在抓到指定数据包后停止抓包，如：stop\_filter = lambda x: x.haslayer(TCP)；
*   iface：指定抓包的接口 

### **将抓取到的数据包保存**

```
from scapy.all import *      
package=sniff(iface='eth0',count=10)  #扫描eth0网卡的数据包，总数为10个      
wrpcap("test.pcap",package)  #将抓取到的包保存为test.pcap文件       
如果我们以后想查看这个包的话，可以这样使用      
package = sniff(offline='test.pcap')  或 package= rdpcap('test.pcap')
```


### **查看抓取到的数据包** 

```
>>from scapy.all import *      
>>package=sniff(iface='eth0',count=10)  #扫描eth0网卡的数据包，总数为10个      
>>print(package)      
<Sniffed: TCP:0 UDP:10 ICMP:0 Other:0>
```


从上面可以看到，我们抓取到了十个[UDP](https://so.csdn.net/so/search?q=UDP&spm=1001.2101.3001.7020)的数据包，然后我们可以查看第一个数据包：package\[0\]是查看第一个数据包的数据，package\[0\].show()是查看第一个数据包的详细信息，scapy是按照按照 TCP/IP 四层参考模型显示详细包信息的，即：链路层 \[Ethernet\]、网络层\[IP\]、传输层\[TCP/UDP\]、应用层\[RAW\] 。我们还可以通过协议来查看指定的包：

package\[UDP\]\[0\].show() ，因为我们这里只有UDP的数据包，所以就没有这样使用。，而我们也可以直接只获取指定层的数据，如： pcap\[UDP\]\[1\]\[Ether\].dst   这个包里面是等于ff:ff:ff:ff:ff:ff

```
from scapy.all import *      
package=sniff(iface='eth0',count=10)  #扫描eth0网卡的数据包，总数为10个      
print(package)      
print(package[0])   #查看第一个数据包的数据      
print(package[0].show())  #查看第一个数据包的详情       
######################################################################      
<Sniffed: TCP:0 UDP:9 ICMP:0 Other:1>       
b"\xff\xff\xff\xff\xff\xff\xc8[v\xec5\xed\x08\x00E\x00\x01#8G\x00\x00@\x11\x17=\n`\n\x88\n`\n\xff\xd6\x83\xd6\x83\x01\x0fN\xa0\x00qu-PC\x00\x00H\xb6\x1d\x07\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xe0\x1dF\x02\x00\x00\x00\x00\xd0\xb5\x1d\x07\x00\x00\x00\x003'\x00\x00\x00\x00\x00\x00\xd0\x1dF\x02\x00\x00\x00\x00\xc02\xc5\x05\x00\x00\x00\x00|j\x85`\x00\x00\x00\x00p\xa4/a\x00\x00\x00\x00\xf9\xb9\x1d\x07\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00`\xfe\xcc\x05\x00\x00\x00\x00D\xb6\x1d\x07\x00\x00\x00\x00`\xb6\x1d\x07\x00\x00\x00\x00H\xaa {efdced0c-1ada-40e0-a13e-2968030599d4}\x00\x00\x00\x00\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00@\xb6\x1d\x07\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00n\x00\xc5\xac"       1
###[ Ethernet ]###       1
  dst       = ff:ff:ff:ff:ff:ff      1
  src       = c8:5b:76:ec:35:ed      1
  type      = 0x800      1
###[ IP ]###       1
     version   = 4      1
     ihl       = 5      1
     tos       = 0x0      1
     len       = 291      2
     id        = 14407      2
     flags     =       2
     frag      = 0      2
     ttl       = 64      2
     proto     = udp      2
     chksum    = 0x173d      2
     src       = 10.96.10.136      2
     dst       = 10.96.10.255      2
     \options   \      2
###[ UDP ]###       3
        sport     = 54915      3
        dport     = 54915      3
        len       = 271      3
        chksum    = 0x4ea0      3
###[ Raw ]###       3
           load      = "\x00qu-PC\x00\x00H\xb6\x1d\x07\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xe0\x1dF\x02\x00\x00\x00\x00\xd0\xb5\x1d\x07\x00\x00\x00\x003'\x00\x00\x00\x00\x00\x00\xd0\x1dF\x02\x00\x00\x00\x00\xc02\xc5\x05\x00\x00\x00\x00|j\x85`\x00\x00\x00\x00p\xa4/a\x00\x00\x00\x00\xf9\xb9\x1d\x07\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00`\xfe\xcc\x05\x00\x00\x00\x00D\xb6\x1d\x07\x00\x00\x00\x00`\xb6\x1d\x07\x00\x00\x00\x00H\xaa {efdced0c-1ada-40e0-a13e-2968030599d4}\x00\x00\x00\x00\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00@\xb6\x1d\x07\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00n\x00\xc5\xac"
```


### 格式化输出

如果我们要对抓取到的数据包进行[格式化输出](https://so.csdn.net/so/search?q=%E6%A0%BC%E5%BC%8F%E5%8C%96%E8%BE%93%E5%87%BA&spm=1001.2101.3001.7020)，我们可以使用 packet.`sprintf()` 函数进行格式化输出 

sprintf()读数据格式：IP：%IP.src%  代表读取的是IP字段的源地址

比如要读取IP包的源地址和目的地址： IP:%IP.src% -> %IP.dst%

要读取UDP中的源端口和目的端口：  UDP:%UDP.sport% -> %UDP.sport%

![](https://img-blog.csdnimg.cn/20181107201053282.png)

### 过滤抓包

如果我们想抓指定类型的数据包，就需要使用 filter 进行过滤，而 filter 使用的是 Berkeley Packet Filter (BPF)语法，也就是我们在 wireshark 中可以使用的过滤语法

比如说我们只抓取 icmp 的包，并且按照 源ip-> 目的ip 的格式打印出来 。我在一直ping百度，下面是抓包的

```
sniff(filter="icmp",count=5,prn=lambda x : x.sprintf("{IP:%IP.src%-> %IP.dst%}"))
```


![](https://img-blog.csdnimg.cn/20181107201611444.png)

相关文章：[Python脚本扫描给定网段的MAC地址表(scapy或 python-nmap)](https://blog.csdn.net/qq_36119192/article/details/83719688)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-147)[网络爬虫](https://edu.csdn.net/skill/python/python-3-147)[urllib](https://edu.csdn.net/skill/python/python-3-147)84711 人正在系统学习中