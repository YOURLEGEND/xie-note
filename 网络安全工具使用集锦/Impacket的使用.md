**目录**

[Impacket](#t0 "Impacket")

[Impacket的安装](#t1 "Impacket的安装")

[Impacket中包含以下协议](#t2 "Impacket中包含以下协议")

[Impacket中的脚本](#t3 "Impacket中的脚本")

[远程执行](#t4 "远程执行")

[Windows Secrets](#t5 "Windows Secrets")

[服务器工具/ MiTM攻击](#t6 "服务器工具/ MiTM攻击")

[WMI](#t7 "WMI")

[已知的漏洞](#t8 "已知的漏洞 ") 

[SMB / MSRPC](#t9 "SMB / MSRPC")

[MSSQL / TDS](#t10 "MSSQL / TDS")

[文件格式](#t11 "文件格式")

[其他](#t12 "其他")

* * *

Impacket
--------

Impacket是用于处理[网络协议](https://so.csdn.net/so/search?q=%E7%BD%91%E7%BB%9C%E5%8D%8F%E8%AE%AE&spm=1001.2101.3001.7020)的Python类的集合，用于对SMB1-3或IPv4 / IPv6 上的TCP、UDP、ICMP、IGMP，ARP，IPv4，IPv6，SMB，MSRPC，NTLM，Kerberos，WMI，LDAP等协议进行低级编程访问。数据包可以从头开始构建，也可以从原始数据中解析，而面向对象的API使处理协议的深层次结构变得简单。

项目地址：[https://github.com/SecureAuthCorp/impacket](https://github.com/SecureAuthCorp/impacket "https://github.com/SecureAuthCorp/impacket")

关于工具的说明：[Impacket – SecureAuth](https://www.secureauth.com/labs/open-source-tools/impacket "Impacket – SecureAuth")

### Impacket的安装

```
git clone https://github.com/CoreSecurity/impacket.git      
cd impacket/      
python setup.py install
```


![](https://img-blog.csdnimg.cn/20200314222800711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

安装完成后，进入examples目录，查看有哪些脚本

```
cd impacket/examples
```


![](https://img-blog.csdnimg.cn/20200314223741275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Impacket中包含以下协议

*   以太网，Linux “Cooked” 数据包捕获
*   IP，TCP，UDP，ICMP，IGMP，ARP
*   支持IPv4和IPv6
*   NMB和SMB1，SMB2和SMB3（高级实现）
*   MSRPC版本5，通过不同的传输协议：TCP，SMB / TCP，SMB/NetBIOS 和 HTTP
*   使用 密码/哈希/票据/密钥 进行简单的 NTLM 和 Kerberos 身份验证
*   部分或完全实现以下MSRPC接口：EPM，DTYPES，LSAD，LSAT，NRPC，RRP，SAMR，SRVS，WKST，SCMR，BKRP，DHCPM，EVEN6，MGMT，SASEC，TSCH，DCOM，WMI
*   部分TDS（MSSQL）和LDAP协议实现。

Impacket中的脚本
------------

### 远程执行

*   [psexec.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/psexec.py "psexec.py")：类似psexec的功能示例，使用remcomsvc（https://github.com/kavika13/remcom）
*   [smbexec.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/smbexec.py "smbexec.py")：与使用remcomsvc的psexec类似的方法。这里描述了该技术。我们的实现更进一步，实例化本地smbserver以接收命令的输出。这在目标计算机没有可写共享可用的情况下很有用。
*   [atexec.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/atexec.py "atexec.py")：此示例通过Task Scheduler服务在目标计算机上执行命令，并返回已执行命令的输出。
*   [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/wmiexec.py "wmiexec.py")：通过Windows Management Instrumentation使用的半交互式shell，它不需要在目标服务器上安装任何服务/代理，以管理员身份运行，非常隐蔽。
*   [dcomexec.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/dcomexec.py "dcomexec.py")：类似于wmiexec.py的半交互式shell，但使用不同的DCOM端点。目前支持MMC20.Application，ShellWindows和ShellBrowserWindow对象。

*   [GetTGT.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/getTGT.py "GetTGT.py")：指定密码，哈希或aesKey，此脚本将请求TGT并将其保存为ccache
*   [GetST.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/getST.py "GetST.py")：指定ccache中的密码，哈希，aesKey或TGT，此脚本将请求服务票证并将其保存为ccache。如果该帐户具有约束委派（具有协议转换）权限，您将能够使用-impersonate参数代表另一个用户请求该票证。
*   [GetPac.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/getPac.py "GetPac.py")：此脚本将获得指定目标用户的PAC（权限属性证书）结构，该结构仅具有正常的经过身份验证的用户凭据。它通过混合使用\[MS-SFU\]的S4USelf +用户到用户Kerberos身份验证组合来实现的。
*   [GetUserSPNs.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/GetUserSPNs.py "GetUserSPNs.py")：此示例将尝试查找和获取与普通用户帐户关联的服务主体名称。
*   [GetNPUsers.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/GetNPUsers.py "GetNPUsers.py")：此示例将尝试为那些设置了属性“不需要Kerberos预身份验证”的用户获取TGT（UF\_DONT\_REQUIRE\_PREAUTH).输出与JTR兼容 
*   [ticketer.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/ticketer.py "ticketer.py")：此脚本将从头开始或基于模板（根据KDC的合法请求）创建金/银票据，允许您在PAC\_LOGON\_INFO结构中自定义设置的一些参数，特别是组、外接程序、持续时间等。 
*   [raiseChild.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/raiseChild.py "raiseChild.py")：此脚本通过（ab）使用Golden Tickets和ExtraSids的基础来实现子域到林权限的升级。

### Windows Secrets

*   [secretsdump.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/secretsdump.py "secretsdump.py：")执行各种技术从远程机器转储Secrets，而不在那里执行任何代理。对于SAM和LSA Secrets（包括缓存的凭据），然后将hives保存在目标系统（％SYSTEMROOT％\\ Temp目录）中，并从中读取其余数据。对于DIT文件，我们使用dl\_drsgetncchanges（）方法转储NTLM哈希值、纯文本凭据（如果可用）和Kerberos密钥。它还可以通过使用smbexec/wmiexec方法执行的vssadmin来转储NTDS.dit.如果脚本不可用，脚本将启动其运行所需的服务（例如，远程注册表，即使它已被禁用）。运行完成后，将恢复到原始状态。
*   [mimikatz.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/mimikatz.py "mimikatz.py：")用于控制@gentilkiwi开发的远程mimikatz RPC服务器的迷你shell

### 服务器工具/ MiTM攻击

*   [ntlmrelayx.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/ntlmrelayx.py "ntlmrelayx.py：")此脚本执行NTLM中继攻击，设置SMB和HTTP服务器并将凭据中继到许多不同的协议（SMB，HTTP，MSSQL，LDAP，IMAP，POP3等）。该脚本可以与预定义的攻击一起使用，这些攻击可以在中继连接时触发（例如，通过LDAP创建用户），也可以在SOCKS模式下执行。在此模式下，对于每个中继的连接，稍后可以通过SOCKS代理多次使用它
*   [karmaSMB.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/karmaSMB.py "karmaSMB.py：")无论指定的SMB共享和路径名如何，都会响应特定文件内容的SMB服务器
*   [smbserver.py:SMB](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/smbserver.py "smbserver.py:SMB")服务器的Python实现，允许快速设置共享和用户帐户。

### WMI

*   [wmiquery.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/wmiquery.py "wmiquery.py：")它允许发出WQL查询并在目标系统上获取WMI对象的描述（例如，从win32\_account中选择名称）
*   [wmipersist.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/wmipersist.py "wmipersist.py")：此脚本创建、删除WMI事件使用者、筛选器，并在两者之间建立链接，以基于指定的wql筛选器或计时器执行Visual Basic

### 已知的漏洞 

*   [goldenPac.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/goldenPac.py "goldenPac.py：") 利用MS14-068。保存Golden Ticket并在目标位置启动PSExec会话

*   [sambaPipe.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/sambaPipe.py "sambaPipe.py：")该脚本将利用CVE-2017-7494，通过-so参数上传和执行用户指定的共享库。 
*   [smbrelayx.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/smbrelayx.py "smbrelayx.py：")利用SMB中继攻击漏洞CVE-2015-0005。如果目标系统正在执行签名并且提供了计算机帐户，则模块将尝试通过NETLOGON收集SMB会话密钥。  利用SMB中继攻击漏洞CVE-2015-0005

### SMB / MSRPC

*   [smbclient.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/smbclient.py "smbclient.py：")一个通用的SMB客户端，可以允许您列出共享和文件名，重命名，上传和下载文件，以及创建和删除目录，所有这些都是使用用户名和密码或用户名和哈希组合。这是一个很好的例子，可以了解到如何在实际中使用impacket.smb
*   [getArch.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/getArch.py "getArch.py：")此脚本将与目标主机连接，并使用文档化的msrpc功能收集由（ab）安装的操作系统体系结构类型。 
*   [rpcdump.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/rpcdump.py "rpcdump.py：")此脚本将转储目标上注册的RPC端点和字符串绑定列表。它还将尝试将它们与已知端点列表进行匹配。
*   [ifmap.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/ifmap.py "ifmap.py")：此脚本将绑定到目标的管理接口，以获取接口ID列表。它将在另一个界面UUID列表上使用这个列表，尝试绑定到每个接口并报告接口是否已列出或正在侦听
*   [opdump.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/opdump.py "opdump.py：")这将绑定到给定的hostname:port和msrpc接口。然后，它尝试依次调用前256个操作号中的每一个，并报告每个调用的结果。 
*   [samrdump.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/samrdump.py "samrdump.py：")从MSRPC套件与安全帐户管理器远程接口通信的应用程序中。它列出了通过此服务导出的系统用户帐户、可用资源共享和其他敏感信息  
     
*   [services.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/services.py "services.py：")此脚本可用于通过\[MS-SCMR\] MSRPC接口操作Windows服务。它支持启动，停止，删除，状态，配置，列表，创建和更改。 
*   [netview.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/netview.py "netview.py：")获取在远程主机上打开的会话列表，并跟踪这些会话在找到的主机上循环，并跟踪从远程服务器登录/退出的用户 
*   [reg.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/reg.py "reg.py：")通过\[ms-rrp\]msrpc接口远程注册表操作工具。其想法是提供与reg.exe Windows实用程序类似的功能。=
*   [lookupsid.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/lookupsid.py "lookupsid.py：")通过\[MS-LSAT\] MSRPC接口的Windows SID暴力破解程序示例，旨在查找远程用户和组

### MSSQL / TDS

*   [mssqlinstance.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/mssqlinstance.py "mssqlinstance.py：")从目标主机中检索MSSQL实例名称。 
*   [mssqlclient.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/mssqlclient.py "mssqlclient.py：")MSSQL客户端，支持SQL和Windows身份验证（哈希）。它还支持TLS。

### 文件格式

*   [esentutl.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/esentutl.py "esentutl.py：")Extensibe存储引擎格式实现。它允许转储ESE数据库的目录，页面和表（例如NTDS.dit） 
*   [ntfs-read.py](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/ntfs-read.py "ntfs-read.py")：NTFS格式实现。此脚本提供了一个用于浏览和提取NTFS卷的功能小的反弹shell，包括隐藏/锁定的内容
*   [registry-read.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/registry-read.py "registry-read.py：")Windows注册表文件格式实现。它允许解析脱机注册表配置单元

### 其他

*   [GetADUsers.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/GetADUsers.py "GetADUsers.py：")此脚本将收集有关域用户及其相应电子邮件地址的数据。它还将包括有关上次登录和上次密码设置属性的一些额外信息。  
*   [mqtt\_check.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/mqtt_check.py "mqtt_check.py：")简单的MQTT示例，旨在使用不同的登录选项。可以很容易地转换成帐户/密码暴力工具。 
*   [rdp\_check.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/rdp_check.py "rdp_check.py：")\[MS-RDPBCGR \]和\[MS-CREDSSP\]部分实现只是为了达到CredSSP身份验证。此示例测试帐户在目标主机上是否有效。
*   [sniff.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/sniff.py "sniff.py：")简单的数据包嗅探器，使用pcapy库来监听在指定接口上传输的包。
*   [sniffer.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/sniffer.py "sniffer.py：")简单的数据包嗅探器，它使用原始套接字来侦听与指定协议相对应的传输中的数据包。
*   [ping.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/ping.py "ping.py：")简单的ICMP ping，它使用ICMP echo和echo-reply数据包来检查主机的状态。如果远程主机已启动，则应使用echo-reply数据包响应echo探针。 
*   [ping6.py：](https://github.com/SecureAuthCorp/impacket/blob/impacket_0_9_19/examples/ping6.py "ping6.py：")简单的IPv6 ICMP ping，它使用ICMP echo和echo-reply数据包来检查主机的状态。 

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[Impacket网络协议工具包介绍](https://www.freebuf.com/sectool/175208.html "Impacket网络协议工具包介绍")