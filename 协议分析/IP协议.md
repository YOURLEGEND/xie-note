**目录**

[IP](#t0)

[IP数据包的封装](#t1)

[IP欺骗](#t2)

[IP欺骗的防范](#t3)

* * *

IP
--

**IP协议**（Internet Protocol，互联网协议），是 TCP/IP 协议栈中最核心的协议之一，通过IP地址，保证了联网设备的唯一性

### IP数据包的封装

![](https://img-blog.csdnimg.cn/20181030082840409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181030082851444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181030082904211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

IP 数据报的首部长度和数据长度都是可变长的，但总是4 字节的整数倍。对于IPv4，4 位版本字段是4。4 位首部长度的数值是以4 字节为单位的，最小值为5，也就是说首部长度最小是4x5=20 字节，也就是不带任何选项的IP 首部，4 位能表示的最大值是15，也就是说首部长度最大是4x15=60 字节  
标志符：在IP数据报头部有一个叫“标志”的字段，用3位二进制数表示：  
不分片DF（Do not Fragment）标志如果被置1，则数据报在传输过程中不能被分片，如网络连通性测试命令ping就可以用-F参数设置为在                数据传输时不分片，但这样当数据不能通过MTU较小的网络时，将产生数据不可达的错误。  
片未完MF（More Fragment）标志如果被置1，说明该数据报不是分片后的最后一个数据报，如果被置为0，则说明是最后一个数据片段

### IP欺骗

IP地址欺骗是指行动产生的IP数据包为伪造的源IP地址，以便冒充其他系统或发件人的身份。这是一种黑客的攻击形式，黑客使用一台计算机上网,而借用另外一台机器的IP地址,从而冒充另外一台机器与服务器打交道。

**场景一：**

常用于DDoS攻击（分布式拒绝服务攻击），在向目标系统发起的恶意攻击请求中，随机生成大批假冒源IP，如果目标防御较为薄弱，对收到的恶意请求也无法分析攻击源的真实性，从而达到攻击者隐藏自身的目的。  
这类场景里一种很有意思的特殊情景来自于“反射”式DDoS攻击，它的特点来自于利用目标系统某种服务的协议缺陷，发起针对目标系统输入、输出的不对称性——向目标发起吞吐量相对较小的某种恶意请求，随后目标系统因其协议缺陷返回大量的响应，阻塞网络带宽、占用主机系统资源。这时如果攻击者的请求使用真实源地址的话，势必要被巨大的响应所吞没，伤及自身。这样，攻击者采取IP欺骗措施就势在必行了。

**场景二：**

原本A主机信任B主机，也就是B可以畅通无阻地获取A的数据资源。而恶意主机C为了能同样获取到A的数据，就需要伪装成B去和A通信。这样C需要做两件事：

*   第一：让B把嘴堵上，不再向A发送请求，比如向B主机发起DoS攻击，占用B的连接使其无法正常发出网络包
*   第二：伪装成B的IP和A交互。

### IP欺骗的防范

一方面需要目标设备采取更强有力的认证措施，不仅仅根据源IP就信任来访者，更多的需要强口令等认证手段；另一方面采用健壮的交互协议以提高伪装源IP的门槛。