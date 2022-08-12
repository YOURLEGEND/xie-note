**目录**

[SPN](#t0 "SPN")

[SPN的配置](#t1 "SPN的配置")

[使用SetSPN注册SPN](#t2 "使用SetSPN注册SPN")

[SPN的发现](#t3 "SPN的发现")

[使用SetSPN查询](#t4 "使用SetSPN查询")

[PowerShell-AD-Recon](#t5 "PowerShell-AD-Recon")

[GetUserSPNs.ps1](#t6 "GetUserSPNs.ps1")

[GetUserSPNs.vbs](#t7 "GetUserSPNs.vbs")

[PowerView.ps1](#t8 "PowerView.ps1")

[PowerShellery](#t9 "PowerShellery")

[RiskySPN中的Find-PotentiallyCrackableAccounts.ps1](#t10 "RiskySPN中的Find-PotentiallyCrackableAccounts.ps1")

* * *

> 查看原文请关注微信公众号：谢公子学安全

SPN
===

**SPN**(ServicePrincipal Names)服务主体名称，是服务实例(比如：HTTP、SMB、MySQL等服务)的唯一标识符。

Kerberos认证过程使用SPN将服务实例与服务登录账户相关联，如果想使用 Kerberos 协议来认证服务，那么必须正确配置SPN。如果在整个林或域中的计算机上安装多个服务实例，则每个实例都必须具有自己的 SPN。如果客户端可能使用多个名称进行身份验证，则给定服务实例可以具有多个SPN。SPN 始终包含运行服务实例的主机的名称，因此服务实例可以为其主机的每个名称或别名注册SPN。一个用户账户下可以有多个SPN，但一个SPN只能注册到一个账户。在内网中，SPN扫描通过查询向域控服务器执行服务发现。这对于红队而言，可以帮助他们识别正在运行重要服务的主机，如终端，交换机等。SPN的识别是kerberoasting攻击的第一步。

**下面通过一个例子来说明SPN的作用：**

      当某用户需要访问MySQL服务时，系统会以当前用户的身份向域控查询SPN为MySQL的记录。当找到该SPN记录后，用户会再次与KDC通信，将KDC发放的TGT作为身份凭据发送给KDC，并将需要访问的SPN发送给KDC。KDC中的TGS服务对TGT进行解密。确认无误后，由TGS将一张允许访问该SPN所对应的服务的ST服务票据和该SPN所对应的服务的地址发送给用户，用户使用该票据即可访问MySQL服务。

**SPN分为两种类型：**

1.  一种是注册在活动目录的机器帐户(Computers)下，当一个服务的权限为 Local System 或 Network Service，则SPN注册在机器帐户(Computers)下。域中的每个机器都会有注册两个SPN：**HOST/主机名**  和  **HOST/主机名.xie.com**
2.  另一种是注册在活动目录的域用户帐户(Users)下，当一个服务的权限为一个域用户，则SPN注册在域用户帐户(Users)下。

![](https://img-blog.csdnimg.cn/20200120153617815.png)

**这里以SQLServer服务注册为例：**

    SQLServer在每次启动的时候，都会去尝试用自己的启动账号注册SPN。但是在Windows域里，默认普通机器账号有权注册SPN，但是普通域用户账号是没有权注册SPN的。这就会导致这样一个现象，SQL Server如果使用“Local System account”来启动，Kerberos就能够成功，因为SQL Server这时可以在DC上注册SPN。如果用一个域用户来启动，Kerberos就不能成功，因为这时SPN注册不上去。

**解决办法：**

*   可以使用工具SetSPN -S来手动注册SPN。但是这不是一个最好的方法，毕竟手工注册不是长久之计。如果SPN下次丢了，又要再次手动注册。
*   所以比较好的方法，是让SQL Server当前启动域账号有注册SPN的权力。要在DC上为域账号赋予 “Read servicePrincipalName” 和 “Write serverPrincipalName” 的权限即可。

那么，如何在域控上为域账号赋予“Read servicePrincipalName” 和 “Write serverPrincipalName” 的权限呢？

在域控的cmd输入adsiedit.msc，打开ADSI编辑器，右键——>连接到——>确定

![](https://img-blog.csdnimg.cn/20200713180857895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

右键属性——>安全——>高级——>添加，对象名称输入 SELF

![](https://img-blog.csdnimg.cn/20200713180937761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200713181010831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020071318110478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200713181216527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SPN的配置
------

微软官方文档：[包含服务主体名称 (SPN) 的 Kerberos | Microsoft Docs](https://docs.microsoft.com/zh-cn/windows-server/networking/sdn/security/kerberos-with-spn "包含服务主体名称 (SPN) 的 Kerberos | Microsoft Docs")

在 SPN 的语法中存在四种元素，两个必须元素和两个额外元素，其中 <service class> 和 <host> 为必须元素：

```
SPN格式：<service class>/<host>:<port>/<service name>       
<service class>：标识服务类的字符串，可以理解为服务的名称，常见的有WWW、MySQL、SMTP、MSSQL等；必须元素      
<host>：服务所在主机名，host有两种形式，FQDN(win7.xie.com)和NetBIOS(win7)名；必须元素      
<port>：服务端口,如果服务运行在默认端口上,则端口号(port)可以省略；额外元素      
<service name>：服务名称,可以省略；额外元素       
一些服务的SPN示例：      
#Exchange服务      1
exchangeMDB/ex01.xie.com      1
#RDP服务      1
TERMSERV/te01.xie.com      1
#WSMan/WinRM/PSRemoting服务      1
WSMAN/ws01.xie.com
```


![](https://img-blog.csdnimg.cn/2020012015320023.png)

**使用SetSPN注册SPN**
-----------------

SetSPN是一个本地Windows二进制文件，可用于检索用户帐户和服务之间的映射。该实用程序可以添加，删除或查看SPN注册。

*   主机：win7.xie.com
*   域控：win2008.xie.com
*   当前用户：xie/test

注：只有机器账号或域管理员账号有权限注册SPN，普通域用户注册SPN会提示权限不够！

```
setspn -S MySQL/win7.xie.com:3307/MySQL win2008
```


![](https://img-blog.csdnimg.cn/20200309163459633.png)

**以test用户的身份进行SPN服务的注册**

```
setspn -S SQLServer/win7.xie.com:1433 test      
或      
setspn -U -A SQLServer/win7.xie.com:1434 test       
setspn -S SQLServer/win7.xie.com:1433 hack    也可以为域内其他用户创建SPN
```


 ![](https://img-blog.csdnimg.cn/20200120152753150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200713170929552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**以WIN7主机的身份在DC(win2008.xie.com)上进行SPN服务(SQLServer)的注册**

```
setspn -S SQLServer/win7.xie.com:1533/MSSQL win7      
setspn -S SQLServer/win7.xie.com:1533/MSSQL win2008  #在主机win7上为win2008注册SPN
```


这里由于之前用 test 用户注册过，所以会提示重复，我们可以将端口修改为其他端口，则不是重复的SPN了

![](https://img-blog.csdnimg.cn/20200120152936384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200713171024173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SPN的发现
------

由于每台服务器都需要注册用于Kerberos身份验证服务的SPN，因此这为在不进行大规模端口扫描的情况下收集有关内网域环境的信息提供了一个更加隐蔽的方法。

### 使用SetSPN查询

windows系统自带的setspn可以查询域内的SPN。

```
查看当前域内所有的SPN： setspn  -Q  */*      
查看指定域xie.com注册的SPN：setspn -T xie.com -Q */*      如果指定域不存在，则默认切换到查找本域的SPN      
查找本域内重复的SPN： setspn -X      
删除指定SPN：setspn -D MySQL/win7.xie.com:1433/MSSQL hack      
查找指定用户/主机名注册的SPN：setspn -L username/hostname
```


![](https://img-blog.csdnimg.cn/20200120145427131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200120145515707.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200120150259780.png)

![](https://img-blog.csdnimg.cn/20200120153101753.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **PowerShell-AD-Recon**

该工具包提供了一些探测指定SPN的脚本，例如Exchange，Microsoft SQLServer，Terminal等

![](https://img-blog.csdnimg.cn/20200120162132919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
#Discover-PSMSSQLServers.ps1的使用,扫描MSSQL服务      
Import-Module .\Discover-PSMSSQLServers.ps1;Discover-PSMSSQLServers       
#Discover-PSMSExchangeServers.ps1的使用，扫描Exchange服务      
Import-Module .\Discover-PSMSExchangeServers.ps1;Discover-PSMSExchangeServers       
#扫描域中所有的SPN信息      
Import-Module .\Discover-PSInterestingServices.ps1;Discover-PSInterestingServices
```


### **GetUserSPNs.ps1**

GetUserSPNs 是 Kerberoast 工具集中的一个 [powershell](https://so.csdn.net/so/search?q=powershell&spm=1001.2101.3001.7020) 脚本，用来查询域内**用户**注册的 SPN。

```
Import-Module .\GetUserSPNs.ps1
```


![](https://img-blog.csdnimg.cn/20200120155837935.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **GetUserSPNs.vbs**

GetUserSPNs 是 Kerberoast 工具集中的一个 vbs 脚本，用来查询域内用户注册的 SPN。

```
cscript .\GetUserSPNs.vbs
```


![](https://img-blog.csdnimg.cn/20200120160124362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **PowerView.ps1**

PowerView是 PowerSpolit 中 Recon目录下的一个powershell脚本**，**PowerView 相对于上面几种是根据不同用户的 objectsid 来返回，返回的信息更加详细。

```
Import-Module .\PowerView.ps1      
Get-NetUser -SPN
```


![](https://img-blog.csdnimg.cn/20200120163552785.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### PowerShellery

PowerShellery下有各种各样针对服务SPN探测的脚本。其中一些需要PowerShell v2.0的环境，还有一些则需要PowerShell v3.0环境。

```
#Powershellery/Stable-ish/Get-SPN/ 下Get-SPN.psm1脚本的使用,需要powershell3.0及以上版本才能使用      
Import-Module .\Get-SPN.psm1      
Get-SPN -type service -search "*"      
Get-SPN -type service -search "*" -List yes | Format-Table       
#Powershellery/Stable-ish/ADS/ 下Get-DomainSpn.psm1脚本的使用      
Import-Module .\Get-DomainSpn.psm1      
Get-DomainSpn
```


![](https://img-blog.csdnimg.cn/20200120165258583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **RiskySPN中的Find-PotentiallyCrackableAccounts.ps1**

该脚本可以帮助我们自动识别弱服务票据，主要作用是对属于用户的可用服务票据执行审计，并根据用户帐户和密码过期时限来查找最容易包含弱密码的票据。

```
Import-Module .\Find-PotentiallyCrackableAccounts.ps1;Find-PotentiallyCrackableAccounts -FullData -Verbose
```


![](https://img-blog.csdnimg.cn/20200309194322619.png)

该脚本将提供比klist和Mimikatz更详细的输出，包括组信息，密码有效期和破解窗口。

![](https://img-blog.csdnimg.cn/20200309194409579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用domain参数，将返回所有具有关联服务主体名称的用户帐户，也就是将返回所有SPN注册在域用户下的用户。

```
Import-Module .\Find-PotentiallyCrackableAccounts.ps1;Find-PotentiallyCrackableAccounts -Domain "xie.com"
```


![](https://img-blog.csdnimg.cn/20200309194747513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[SPN服务主体名称发现详解](https://www.freebuf.com/articles/system/174229.html "SPN服务主体名称发现详解")

                  [域渗透：SPN和kerberoast攻击](https://www.cnblogs.com/zpchcbd/p/11707776.html "域渗透：SPN和kerberoast攻击")

相关文章：[域渗透之Kerberoast攻击](https://xie1997.blog.csdn.net/article/details/104049291 "域渗透之Kerberoast攻击")