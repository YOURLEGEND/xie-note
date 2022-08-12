**目录**

[LLMNR和NetBios](#t0 "LLMNR和NetBios")

[攻击原理](#t1 "攻击原理")

[Responder](#t2 "Responder")

[攻击过程](#t3 "攻击过程")

* * *

LLMNR和NetBios
-------------

**首先我们来看看Windows系统的名称解析顺序如下：**

*   本地hosts文件（%windir%\\System32\\drivers\\etc\\hosts）
*   DNS缓存/DNS服务器
*   链路本地多播名称解析（LLMNR）和NetBIOS名称服务（NBT-NS）

**什么是LLMNR和NetBIOS名称服务器广播？**

当DNS名称服务器请求失败时，Microsoft Windows系统就会通过链路本地多播名称解析（LLMNR）和Net-[BIOS](https://so.csdn.net/so/search?q=BIOS&spm=1001.2101.3001.7020)名称服务（NBT-NS）试图在本地进行名称解析。

**LLMNR和Netbios NS广播存在的问题**

当DNS名称无法解析的时候，客户端就会将未经认证的UDP广播到网络中，询问它是否为本地系统的名称。 事实上，该过程是未被认证的，并会广播到整个网络，从而允许网络上的任何机器响应并声称是目标机器。

**什么是LLMNR /Netbios NS欺骗？**

通过侦听LLMNR和NetBIOS广播，攻击者可以伪装成受害者（客户端）要访问的目标机器，从而让受害者乖乖交出相应的登陆凭证。在接受连接后，攻击者可以使用 Responder 或 Metasploit 等工具将请求转发到执行身份验证过程的服务（如SMB）。 在服务身份验证过程中，受害者会向服务器发送用于身份认证的Net-NTLMv2哈希值，这个哈希值会被攻击者接收并保存。之后就可以使用像Hashcat 或John Ripper（TJR）这样的工具在线破解，或直接用于 pass-the-hash 攻击。

在Windows中，LLMNR和Netbios NS是默认启用的，并且了解这种攻击的人很少，所以我们可以在内部渗透测试中利用该攻击来收集凭据。为此，我们可以在使用其他攻击手段进行测试的过程中，可以让Responder一直监听着。 

**Netbios(Network Basic Input Output System)：**网络基本输入输出系统，NetBIOS协议是由IBM公司开发，主要用于20-200台计算机的局域网。Netbios是一种应用程序接口(API)，系统可以利用WINS服务、广播及Lmhost文件等多种模式**将NetBIOS名解析为相应IP地址**，实现信息通讯作用是为了给局域网提供网络以及其他特殊功能，几乎所有的局域网都是在NetBIOS协议的基础上工作的。在Windows操作系统中，默认情况下在安装 TCP/IP 协议后会自动安装NetBIOS。

查看局域网内其他主机的 NetBIOS Name ： nbtscan  --r  10.96.10.0/24

![](https://img-blog.csdnimg.cn/20181130122340806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**LLMNR(Link-LocalMulticast NameResolution****)：**链路本地多播名称解析（LLMNR），是一个基于协议的域名系统（DNS）数据包的格式。当主机访问另外一台主机时，如果只知道对方的主机名，则会向局域网内广播请求，询问该主机名对应的ip地址，然后收到该请求的主机首先会判断自己的主机名是否是这个，如果是的话，则会回复一个ip地址，如果主机名不符合，则丢弃。LLMNR协议就类似于ARP协议。

**NetBIOS** 和 **Link-LocalMulticast NameResolution（LLMNR）**是Microsoft针对工作组和域设计的名称解析协议，主要用于局域网中的名称解析。当DNS解析失败时，Windows系统会使用 NetBIOS 和 LLMNR 搜索名称。这些协议只为本地连接设计。

NetBIOS 和 LLMNR在WindowsVista以后的系统中均实现，二者的主要区别在于：

*   NetBIOS基于广播，而LLMNR基于多播；
*   NetBIOS在WindowsNT以后的所有操作系统上均可用，而只有WindowsVista和更高版本才支持LLMNR；
*   LLMNR还支持IPv6，而NetBIOS不支持，因此，在启用了IPv6，但对IPv6管理不如IPv4那样细致的复杂网络中，就可能发生更广泛的攻击。

### **攻击原理**

NetBIOS和LLMNR协议对于没有DNS的工作站系统来说很有帮助，但也对攻击者大开方便之门。当人们输入不存在、包含错误或者DNS中没有的主机名时，就会使用这些协议在网络中搜索主机，这些协议的本质决定了本地网络上的任何主机都可以回答请求。这意味着作为攻击者，我们能够代替网络上任何不存在的主机回答请求，并诱导搜索内容的主机连接到我们。

更严重的是，当我们使用 Metasploit 和 Responder 等工具时，可以要求验证受害者主机的身份，而如果我们被认为是这些主机所在的本地网络中的一部分时，他们就会把自己进行哈希后的Windows凭据发给我们

### Responder

Responder是由 LaurentGaffie 发布的一款功能强大且简单易用的内网渗透工具，将 NetBIOS名称服务（NBNS）、LLMNR和DNS欺骗集成到一个工具中。Kali中默认安装了 Responder

Responder的特性包括内置SMB认证服务器、MSSQL认证服务器、HTTP认证服务器、HTTPS认证服务器、LDAP认证服务器，DNS服务器、WPAD代理服务器，内建FTP、POP3、IMAP、SMTP服务器用于收集明文的凭据。

Responder会将所有抓取到的hash打印到标准输出接口上，同时会将抓取到的数据存储到 /usr/share/responder/logs/ 文件夹下。

所有的活动都会记录到Responder-Session.log,分析模式下的日志保存到Analyze-Session.log,毒化模式下的日志保存到Poisoners-Session.log.同时所有抓到的hash都会存储到我们在Responder.conf中配置的sqlite数据库中.

### **攻击过程**

1.  用户发送不正确的SMB共享地址 \\\\ SNARE01
2.  DNS服务器响应 \\\\SNARE01 - NOT FOUND
3.  客户端进行 LLMNR / NBT-NS广播
4.  攻击者告诉客户端它是 SNARE01，并接受被害者发送用于认证的Net-NTLMHash值了，保存于本地
5.  响应者将错误发送回客户端，因此最终用户如果不是精于此道的话，通常不会引起警觉

**攻击端输入：**

```
responder -I eth0 -f      #-I指定使用的网卡，-f允许攻击者查看受害者的主机指纹。       
开启毒化，针对LLMNR、NBT-NS（NetBIOS名称服务）、DNS/MDNS的毒化攻击已经开启。
```


![](https://img-blog.csdnimg.cn/20210529163126618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**靶机端输入**

只要随便输入不能解析的名称

![](https://img-blog.csdnimg.cn/20210529163103913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后会弹出输入账户密码认证框

![](https://img-blog.csdnimg.cn/2021052916320050.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，攻击端已经收到了靶机的 Net-NTLMHash 的值了，该值被存放到了 /usr/share/responder/logs/ 目录下。于是我们可以使用 Hashcat 爆破该NTLM哈希值，也可以利用该 Net-NTLMHash 进行NTLM-Relay攻击。传送门：[使用Hashcat破解Net-NTLMHash](https://xie1997.blog.csdn.net/article/details/100105816#%E4%BD%BF%E7%94%A8Hashcat%E7%A0%B4%E8%A7%A3NTLMv2 "使用Hashcat破解Net-NTLMHash")  、 [NTLM Relay攻击](https://xie1997.blog.csdn.net/article/details/105135184 "NTLM Relay攻击")

![](https://img-blog.csdnimg.cn/20210529163103576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)