在linux下，可以使用 [tcpdump](https://so.csdn.net/so/search?q=tcpdump&spm=1001.2101.3001.7020) 命令来抓取数据包。

![](https://img-blog.csdnimg.cn/20200503172436300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**主要用法如下：**

过滤网卡

```
tcpdump -i eth0    #抓取所有经过网卡eth0数据包      
tcpdump -i lo     #抓取环回口的数据包
```


![](https://img-blog.csdnimg.cn/20200503172529274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

![](https://img-blog.csdnimg.cn/20200503172558587.png)

过滤主机/IP

```
tcpdump host 192.168.10.10        #抓取所有IP为192.168.10.10的数据包      
tcpdump src host 192.168.10.10    #抓取所有源IP为192.168.10.10的数据包      
tcpdump dst host 192.168.10.10    #抓取所有目的IP为192.168.10.10的数据包
```


![](https://img-blog.csdnimg.cn/20200503172657321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

过滤端口： 

```
tcpdump port 1234      #抓取所有端口为1234的网络数据       
tcpdump src port 1234  #抓取所有源端口为1234的网络数据       
tcpdump dst port 1234  #抓取所有目的端口为1234的网络数据
```


![](https://img-blog.csdnimg.cn/20200503172756327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

过滤特定协议： 

```
tcpdump udp   #抓取UDP协议的数据包      
tcpdump icmp   #抓取ICMP协议的数据包
```


![](https://img-blog.csdnimg.cn/20200503172909578.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

抓取特定类型的数据包： 

```
tcpdump -i eth1 'tcp[tcpflags] = tcp-syn'   #抓取所有经过网卡1的SYN类型数据包
```


结合

```
tcpdump -i eth1 udp dst port 53  #抓取经过网卡eth1的所有DNS数据包（默认端口）
```


逻辑语句过滤： 

```
tcpdump -i eth1 ‘((tcp) and ((dst net 172.16) and (not dst host 192.168.1.200)))’  #抓取所有经过网卡1，目的网络是172.16，但目的主机不是192.168.1.200的TCP数据
```


[抓包](https://so.csdn.net/so/search?q=%E6%8A%93%E5%8C%85&spm=1001.2101.3001.7020)存取： 

```
tcpdump -i eth1 host 172.16.7.206 and port 80 -w /tmp/xxx.cap  #抓取所有经过网卡1，目的主机为172.16.7.206的端口80的网络数据并存储
```


相关文章：[WEB安全渗透测试基础知识（二十二）](https://mp.weixin.qq.com/s/ZK4-kdpFhOc05_nhasvu1g)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-cf0d7a4a97c34c3ba01732646fcb0617)[Linux实用命令](https://edu.csdn.net/skill/gml/gml-cf0d7a4a97c34c3ba01732646fcb0617)[tcpdump命令](https://edu.csdn.net/skill/gml/gml-cf0d7a4a97c34c3ba01732646fcb0617)10949 人正在系统学习中