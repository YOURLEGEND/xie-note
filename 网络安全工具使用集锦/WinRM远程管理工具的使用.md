**目录**

[WinRM](#t0 "WinRM")

[WinRM的配置](#t1 "WinRM的配置")

[通过WinRM执行程序](#t2 "通过WinRM执行程序")

[利用WinRM远程连接主机](#t3 "利用WinRM远程连接主机")

[客户端连接](#t4 "客户端连接")

[使用Python远程连接WinRM](#t5 "使用Python远程连接WinRM")

[注意事项](#t6 "注意事项")

[WinRM其他命令](#t7 "WinRM其他命令")

* * *

WinRM
-----

    WinRM是Windows Remote Managementd（Windows远程管理）的简称。它基于Web服务管理(WebService-Management)标准，WinRM2.0默认端口5985（HTTP端口）或5986（HTTPS端口）。如果所有的机器都是在域环境下，则可以使用默认的5985端口，否则的话需要使用HTTPS传输(5986端口)。使用WinRM我们可以在对方有设置防火墙的情况下远程管理这台服务器，因为启动WinRM服务后，防火墙默认会放行5985端口。WinRM服务在Windows Server 2012以上服务器自动启动。在WindowsVista上，服务必须手动启动。WinRM的好处在于，这种远程连接不容易被察觉到，也不会占用远程连接数！

![](https://img-blog.csdnimg.cn/20200328210945685.png)

WinRM官方文档：[Windows Remote Management - Win32 apps | Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/winrm/portal "Windows Remote Management - Win32 apps | Microsoft Docs")

### WinRM的配置

```
#查看WinRM状态      
winrm enumerate winrm/config/listener       
#开启WinRM远程管理      
Enable-PSRemoting –force       
#设置WinRM自启动      
Set-Service WinRM -StartMode Automatic       1
#对WinRM服务进行快速配置，包括开启WinRM和开启防火墙异常检测,默认的5985端口      1
winrm quickconfig -q      1
#对WinRM服务进行快速配置，包括开启WinRM和开启防火墙异常检测，HTTPS传输，5986端口      1
winrm quickconfig -transport:https           1
#查看WinRM的配置      1
winrm get winrm/config       1
#查看WinRM的监听器      1
winrm e winrm/config/listener       2
#为WinRM服务配置认证      2
winrm set winrm/config/service/auth '@{Basic="true"}'       2
#修改WinRM默认端口      2
winrm set winrm/config/client/DefaultPorts '@{HTTPS="8888"}'       2
#为WinRM服务配置加密方式为允许非加密：      2
winrm set winrm/config/service '@{AllowUnencrypted="true"}'       3
#设置只允许指定IP远程连接WinRM      3
winrm set winrm/config/Client '@{TrustedHosts="192.168.10.*"}'       3
#执行命令      3
winrm invoke create wmicimv2/win32_process -SkipCAcheck -skipCNcheck '@{commandline="calc.exe"}'       3
#执行指定命令程序      3
winrm invoke create wmicimv2/win32_process -SkipCAcheck -skipCNcheck '@{commandline="c:\users\administrator\desktop\test.exe"}'
```


![](https://img-blog.csdnimg.cn/20200328210254805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

开启WinRM的过程，做了如下几件事：

![](https://img-blog.csdnimg.cn/2020032821230597.png)

快速配置WinRM

![](https://img-blog.csdnimg.cn/20200328211315118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200328211629899.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

设置只允许指定IP远程连接WinRM

![](https://img-blog.csdnimg.cn/20200328224244824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 通过WinRM执行程序

执行calc.exe程序

![](https://img-blog.csdnimg.cn/20200328214033230.png)

执行指定命令程序，我们这里执行木马

![](https://img-blog.csdnimg.cn/20200328223101977.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用WinRM远程连接主机
-------------

### 客户端连接

客户端连接的话，也需要启动WinRM，然后再执行以下命令进行连接。

**方法一：使用winrs连接**

在cmd窗口执行以下命令

```
winrs -r:http://192.168.10.20:5985 -u:administrator -p:root cmd
```


 ![](https://img-blog.csdnimg.cn/2020050110354316.png)

**方法二：使用Enter-PSSession连接**

```
Enter-PSSession -computer win2008.xie.com -Credential xie\administrator -Port 5985      
或      
New-PSSession -Name test -ComputerName win7.xie.com -Credential xie\administrator      
Enter-PSSession -Name test
```


![](https://img-blog.csdnimg.cn/2020032823241127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
查看WinRM远程会话      
Get-PSSession       
进入ID为2的WinRM会话中      
Enter-PSSession -id 2       
退出WinRM会话      
Exit-PSSession
```


![](https://img-blog.csdnimg.cn/20200328233342732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果是工作组环境运行，或客户端未加入域，则需要在客户端执行此命令：

```
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```


![](https://img-blog.csdnimg.cn/20200328234538487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用Python远程连接WinRM

首先，需要服务端WinRM配置如下，在cmd窗口执行以下命令：

```
#为winrm service 配置auth:      
winrm set winrm/config/service/auth @{Basic="true"}      
#为winrm service 配置加密方式为允许非加密：      
winrm set winrm/config/service @{AllowUnencrypted="true"}
```


![](https://img-blog.csdnimg.cn/2020043021484733.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

以下是python脚本 

```
import winrm      
while True:      
	cmd = input("$: ")      
	wintest = winrm.Session('http://192.168.10.20:5985/wsman',auth=('administrator','root'))      
	ret = wintest.run_cmd(cmd)      
	print(ret.std_out.decode("GBK"))       
	print(ret.std_err.decode())
```


![](https://img-blog.csdnimg.cn/20200328235948378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 注意事项

这里需要注意的是，通过WinRM远程连接也是受到LocalAccountTokenFilterPolicy的值影响的。在 Windows Vista 以后的操作系统中，LocalAccountTokenFilterPolicy 的默认值为0，这种情况下内置账户 administrator 进行远程连接时会直接得到具有管理员凭证的令牌，而其他账号包括管理员组内账号远程连接时会提示权限不足。而在域环境中，只要是域管理员都可以建立具备管理员权限的远程连接。

如果要允许本地管理员组的其他用户登录WinRM，需要修改注册表设置。

**修改 LocalAccountTokenFilterPolicy 为1**

```
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
```


WinRM其他命令
---------

```
winrm      
winrm help auth      
winrm help uris How to construct resource URIs.      
winrm help aliases Abbreviations for URIs.      
winrm help config Configuring WinRM client and service settings.      
winrm help certmapping Configuring client certificate access.      
winrm help remoting How to access remote machines.      
winrm help auth Providing credentials for remote access.      
winrm help input Providing input to create, set, and invoke.      1
winrm help switches Other switches such as formatting, options, etc.      1
winrm help proxy Providing proxy information.
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[PowerShell Remoting Cheatsheet](https://www.secpulse.com/archives/37479.html "PowerShell Remoting Cheatsheet")

                  [evil-winrm：Windows远程管理（WinRM）Shell终极版](https://www.freebuf.com/sectool/210479.html "evil-winrm：Windows远程管理（WinRM）Shell终极版")