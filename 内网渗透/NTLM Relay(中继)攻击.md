**目录**

[NTLM Relay](#t0)

[获得Net-NTLM-Hash](#t1)

[使用NetBIOS名称欺骗和LLMNR欺骗获得Net-NTLMHash](#t2)

[使用WPAD劫持获得Net-NTLMHash](#t3)

[如何让受害者自动发送Net-NTLMHash？](#t4)

[Relay Net-NTLMHash](#t5)

[SMBRelay攻击](#t6)

[impacket 的下的smbrelayx.py](#t7)

[impacket 的下的ntlmrelayx.py](#t8)

* * *

NTLM Relay
==========

在讲NTLM Relay之前，先了解下NTLM认证，传送门：[Windows本地认证和NTLM认证](https://xie1997.blog.csdn.net/article/details/85941222)

NTLM Relay其实严格意义上并不能叫NTLM Relay，而是应该叫 Net-NTLM Relay。它是发生在NTLM认证的第三步，客户端发送使用服务端NTLM Hash加密的Challenge值，得到Net-NTLMHash。我们就是要重放这个Net-NTLMHash来进行攻击。

获得Net-NTLM-Hash
---------------

所以，进行NTLM Relay攻击的第一步我们首先要获得这个Net-NTLMHash值。那么如何能获得这个Net-NTLMHash值呢？思路是让受害者把Net-NTLMhash自己发送给攻击者，也就是说只要是使用SMB、HTTP、LDAP、MSSQL等协议来进行NTLM认证的程序，都可以尝试用来向攻击者发送Net-NTLMhash。

比如浏览器、word文档、pdf文档、explorer.exe等等。如果新发现一个这类应用程序，或者发现这些程序的一种调用方法，就会多出一种攻击手段。几种常见的获取方法如下：

*   网络协议的欺骗与劫持
*   钓鱼攻击
*   与其他漏洞结合

### 使用NetBIOS名称欺骗和LLMNR欺骗获得Net-NTLMHash

传送门：[NetBIOS名称欺骗和LLMNR欺骗](https://xie1997.blog.csdn.net/article/details/84637976)

### 使用WPAD劫持获得Net-NTLMHash

WPAD(Web Proxy Auto-Discovery Protocol) 是用来查找PAC文件的协议，其主要通过DHCP、DNS、LLMNR、NBNS协议来查找存放PAC文件的主机。一个典型的劫持方式是利用LLMNR/NBNS欺骗来让受害者从攻击者获取PAC文件，PAC文件指定攻击者就是代理服务器，然后攻击者就可以劫持受害者的HTTP流量，在其中插入任意HTML标签。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzE2OF81Y2QwZjE3MDYzYzI4LnBuZyFzbWFsbA?x-oss-process=image/format,png)

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzE4MV81Y2QwZjE3ZDNkYmNiLnBuZyFzbWFsbA?x-oss-process=image/format,png)

利用网络协议的欺骗与劫持的好处是不需要受害者主动交互来触发发送Net-NTLMhash的行为，受害者的任何正常行为都可以被欺骗，从而被攻击。缺点是这类工具回复所有的多播、广播数据包，容易被发现。

如何让受害者自动发送Net-NTLMHash？
-----------------------

前面介绍了获取受害者Net-NTLMhash的思路，那么如何让受害者自动发送Net-NTLMhash呢？这就需要看具体发送Net-NTLMhash的应用程序了。

这里重点分析下浏览器在什么情况下可以自动向攻击者发送Net-NTLMhash。从下图我们可以看出，Windows10上的IE和Edge，即使支持SMB协议，但是无法通过劫持WPAD的方式来触发，Chrome浏览器虽然可以进行WPAD劫持，但是不支持SMB协议。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzIyOV81Y2QwZjFhZDE0OWQ0LnBuZyFzbWFsbA?x-oss-process=image/format,png)

一个解决思路就是放弃最常用的NTLMSSP over SMB，使用NTMLSSP over HTTP，使用HTTP协议来发送NTLM认证消息，如下：

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzI3OV81Y2QwZjFkZjJlZGJmLnBuZyFzbWFsbA?x-oss-process=image/format,png)

那接下来的问题就是浏览器什么情况下会通过HTTP协议自动发送Net-NTLMhash呢 ？答案是 IntranetZone

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzI5Nl81Y2QwZjFmMDNlNGFjLnBuZyFzbWFsbA?x-oss-process=image/format,png)

演讲者通过对IE的两个API分析，发现其跟 policy 和 zone 有关：

如果 policy 是URLPOLICY\_CREDENTIALS\_SILENT\_LOGON\_OK，IE会自动发送hash。如果policy是URLPOLICY\_CREDENTIALS\_MUST\_PROMPT\_USER，那么只有zone的值为0、1、2的时候才会自动发送hash。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzM0MV81Y2QwZjIxZGNlMTI1LnBuZyFzbWFsbA?x-oss-process=image/format,png)

经过测试，在Windows10上，工作组环境下，只要URL是主机名而不是完全限定性域名的情况下，就认为是LocalInternet Zone内的，如下图：

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzM1Ml81Y2QwZjIyODgyYmU0LnBuZyFzbWFsbA?x-oss-process=image/format,png)

Relay Net-NTLMHash
------------------

Relay到同一台主机还是不同的主机？

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzM3MF81Y2QwZjIzYTJkOTczLnBuZyFzbWFsbA?x-oss-process=image/format,png)

首先说 relay 到不同的主机，如果是 relay 其他主机的SMB服务，在工作组的环境下，两台主机的用户名和密码需要相同；在域环境下，还可以 relay 域用户的 Net-NTLMhash。也可以 relay 到支持NTLM认证的EWS服务，典型的例子就是CVE-2018-8581，这里不再赘述。

再来说 relay 到同一台主机，如果relay同一台主机，由于Net-NTLMhash是从这台机器上发送过来的，肯定可以成功,，但实际上并没那么容易。在没打MS08068补丁的主机上，可以使用最原始的SMB协议进行Net-NTLMhash relay攻击，但是对于打了MS08-068补丁的主机，防止了同一主机从SMB协议向SMB协议的Net-NTLMhash relay。CVE-2019-1384(Ghost Potato)就是绕过了该补丁。

因为NTLM认证消息可以嵌入到其他协议进行传输，所以还可以使用其他协议进行 relay 攻击。也可以进行跨协议的Relay攻击，不过，微软的MS16-075补丁防止了同一主机上从HTTP协议向SMB协议的Net-NTLMhash relay。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzM4M181Y2QwZjI0N2NlNzc2LnBuZyFzbWFsbA?x-oss-process=image/format,png)

那么，还有办法relay攻击同一台主机吗？

演讲者发现Windows上的Java可以用来发送带有Net-NTLMhash的HTTP请求，可以relay给同一台主机。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzQwMF81Y2QwZjI1OGUwOTBiLnBuZyFzbWFsbA?x-oss-process=image/format,png)

并且分析了通过XXE、SSRF等漏洞，触发Java应用发送HTTP请求时，可以自动发送Net-NTLM hash的原因。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZS4zMDAxLm5ldC9pbWFnZXMvMjAxOTA1MDcvMTU1NzE5NzQxN181Y2QwZjI2OTgyYjFjLnBuZyFzbWFsbA?x-oss-process=image/format,png)

这是OpenJDK的问题，该问题在2011年1月10日的OpenJDK7中引入（合入代码，可见http://hg.openjdk.java.net/jdk7/jdk7/jdk/rev/cc989209cbf1），在2018年10月8日进行了修复

SMBRelay攻击
==========

这里演示的是SMB协议的Relay攻击。因为直接relay到smb服务器，是最直接最有效的方法。可以直接控制该服务器(包括但不限于在远程服务器上执行命令，上传exe到远程命令上执行，dump 服务器的用户hash等等等等)。

主要有两种场景

1：工作组环境

这个实用性比较差。在工作组环境里面，工作组中的机器之间相互没有信任关系，每台机器的账号密码Hash只是保存在自己的SAM文件中，这个时候Relay到别的机器，除非两台机器的账号密码一样(如果账号密码一样，我为啥不直接pth呢)，不然没有别的意义了，这个时候的攻击手段就是将机器reflect回机子本身。因此微软在ms08-068中对smb reflect到smb 做了限制。这个补丁在CVE-2019-1384(Ghost Potato)被绕过。将在下篇文章里面详细讲。

2：域环境

域环境底下域用户的账号密码Hash保存在域控的 ntds.dit里面。如下没有限制域用户登录到某台机子，那就可以将该域用户Relay到别人的机子，或者是拿到域控的请求，将域控Relay到普通的机子，比如域管运维所在的机子。(为啥不Relay到其他域控？因为域内就域控默认开启smb签名)

下面演示使用几款工具在域环境底下，从域控relay到普通机器执行命令

impacket 的下的smbrelayx.py
------------------------

首先，在攻击机上执行如下命令监听80和445端口，伪造http和smb服务

```
#SMBRelay攻击192.168.10.130主机，并执行whoami的命令      
./smbrelayx.py -h 192.168.10.130 -c whoami
```


![](https://img-blog.csdnimg.cn/2020032714444510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200327144350168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后只要使用其他方法诱导域管理员或域用户访问了攻击机搭建的伪造HTTP或SMB服务，并输入账号密码进行认证。

![](https://img-blog.csdnimg.cn/20200327151551600.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200327151529364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们的攻击机就可以 Relay 域用户的 Net-NTLM-Hash，来在域内其他机器上执行指定命令

![](https://img-blog.csdnimg.cn/20200327145439489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

impacket 的下的ntlmrelayx.py
-------------------------

首先，在攻击机上执行如下命令监听80和445端口，伪造http和smb服务

```
./ntlmrelayx.py -t smb://192.168.10.130 -c whoami -smb2support
```


然后只要使用其他方法诱导域管理员或普通域用户访问了攻击机搭建的伪造HTTP或SMB服务，并输入账号密码进行认证。

![](https://img-blog.csdnimg.cn/20200327151551600.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200327151529364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 我们的攻击机就可以Relay 域用户的Net-NTLM-Hash，来在域内其他机器上执行指定命令

![](https://img-blog.csdnimg.cn/20200327150832541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[跟着玄武大佬学NTLM relay攻防](https://www.freebuf.com/column/202842.html)

                  [Windows内网协议学习NTLM篇之Net-NTLM利用](https://www.anquanke.com/post/id/194069#h2-4)

相关文章：[渗透测试自动化：使用NTLM中继和Deathstar获取域管理员权限](https://www.freebuf.com/sectool/160884.html)

                  [原创干货 | 浅析SMB relay攻击手法](https://mp.weixin.qq.com/s/_qnt5NVEiud3OyQmGhkfUA)

                 [内网渗透测试：NTLM Relay攻击分析](https://blog.csdn.net/whatday/article/details/107698383)