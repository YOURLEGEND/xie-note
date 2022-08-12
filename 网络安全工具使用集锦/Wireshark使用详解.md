**目录**

[WireShark](#t0 "WireShark")

[Wireshark的简单使用](#t1 "Wireshark的简单使用")

[数据包的过滤](#t2 "数据包的过滤")

[数据流追踪](#t3 "数据流追踪 ") 

[专家信息说明](#t4 "专家信息说明")

[数据包的统计分析](#t5 "数据包的统计分析 ") 

[导出对象——>HTTP](#t6 "导出对象——>HTTP")

[数据包分析过程中的一些小技巧](#t7 "数据包分析过程中的一些小技巧 ") 

* * *

WireShark
---------

[WireShark](https://so.csdn.net/so/search?q=WireShark&spm=1001.2101.3001.7020)只要是学计算机的人应该都听过，也用过。一个非常好用的免费的抓包工具。Wireshark使用WinPcap作为接口，直接与网卡进行数据报文交换。

### Wireshark的简单使用

![](https://img-blog.csdnimg.cn/20181201105845888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

双击选择了网卡之后，就开始[抓包](https://so.csdn.net/so/search?q=%E6%8A%93%E5%8C%85&spm=1001.2101.3001.7020)了

![](https://img-blog.csdnimg.cn/20181201110012452.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

停止抓包后，我们可以选择保存抓取到的数据包。文件——> 另存为——>选择一个存储路径，然后就保存为后缀为 .pcap 格式的文件了，可以双击直接用wireshark打开。

### 数据包的过滤

数据包过滤是wireshark一个很实用的功能了，通常我们抓包会抓取到网卡通过的所有数据包。很多数据包对于我们来说是没用的，所以我们就需要对其进行过滤。数据包的过滤可以分为 **抓取时过滤** 和 **抓取后的过滤  。**这两种过滤的语法不同！

**抓取时过滤** 

捕获——>捕获过滤器，这是wireshark默认的一些捕获过滤器，我们可以参照他的语法，自己在左下角自己添加或者删除捕获过滤器

![](https://img-blog.csdnimg.cn/20181201110627374.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后如果我们想抓取时对数据包过滤，**捕获——>选择**，然后选择我们要抓取数据包的网卡，在下面选择我们的过滤器。绿色的话表示语法没有问题，设置好了之后点击开始就可以抓取数据包了

![](https://img-blog.csdnimg.cn/20181201110823471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) **抓取后的过滤** 

我们一般都是抓取完数据包后进行过滤的，在上方输入我们的过滤语法

```
过滤地址      
ip.addr==192.168.10.10  或  ip.addr eq 192.168.10.10  #过滤地址      
ip.src==192.168.10.10     #过滤源地址      
ip.dst==192.168.10.10     #过滤目的地址       
过滤协议，直接输入协议名      
icmp       
http       1
过滤协议和端口      1
tcp.port==80      1
tcp.srcport==80      1
tcp.dstport==80       1
过滤http协议的请求方式      1
http.request.method=="GET"      1
http.request.method=="POST"      1
http.request.uri contains admin   #url中包含admin的      1
http.request.code==404    #http请求状态码的       2
连接符      2
&&        2
||      2
and      2
or       2
通过连接符可以把上面的命令连接在一起，比如：      2
ip.src==192.168.10.10 and http.request.method=="POST"
```


![](https://img-blog.csdnimg.cn/20181201111249220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 数据流追踪 

我们的一个完整的数据流一般都是由很多个包组成的，所以，当我们想查看某条数据包对于的数据流的话，我们就可以：**右键——>追踪流**，然后就会有TCP流、UDP流、SSL流、HTTP流。当你这个数据包是属于哪种流，就可以选择对应的流

![](https://img-blog.csdnimg.cn/20181201125715305.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当我们选择了追踪流时，会弹出该流的完整数据流。还有这个数据流中包含的数据包。顶部的过滤器就是该流的过滤规则

### ![](https://img-blog.csdnimg.cn/20181201125823685.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

###  专家信息说明

功能：可以对数据包中特定的状态进行警告说明。

*   错误(errors)
*   警告(Warnings)
*   标记(notes)
*   对话(chats)

路径：**分析——>专家信息**

![](https://img-blog.csdnimg.cn/20181201130307419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 数据包的统计分析 

分析一栏中，可以对抓取的数据包进行进一步的分析，比如抓取的数据包的属性、已解析的地址、协议分级等等。我就不一一列出来了

![](https://img-blog.csdnimg.cn/20181201131106601.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**已解析的地址** 

功能：统计通信流量中已经解析了的地址

路径：**统计——>已解析的地址**

**![](https://img-blog.csdnimg.cn/20181201131314816.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

**协议分级**

功能：统计通信流量中不同协议占用的百分比

路径：**统计->协议分级**

**![](https://img-blog.csdnimg.cn/20181201130648266.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

**统计摘要说明**

功能：可以对抓取的数据包进行全局统计，统计出包的一些信息

路径：**统计->捕获文件属性** 

**![](https://img-blog.csdnimg.cn/20181201130512810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)**

### 导出对象——>HTTP

这个功能点非常有用，他可以查看并且导出HTTP流对象，这对于数据包分析非常有用！

![](https://img-blog.csdnimg.cn/20191028230443929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191028230522431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 数据包分析过程中的一些小技巧 

*   大量404请求——>目录扫描
*   大量 select....from 关键字请求——>SQL注入
*   连续一个ip的多端口请求或多个ip的几个相同端口请求——>端口扫描
*   黑客通过爆破账户和密码，则是post请求   http.request.method==POST
*   黑客修改文件，  ip.addr==219.239.105.18 and http.request.uri matches "edit|upload|modify"  
*   如果是用菜刀连接的包，则是post请求  ip.addr==219.239.105.18  and  http.request.method==POST

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)