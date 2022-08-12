**目录**

[Windows错误配置提权](#t0)

[系统服务权限配置错误提权](#t1)

[PowerUp.ps1实战利用](#t2)

[MSF下实战利用](#t3)

[注册表键AlwaysInstallElevated提权](#t4)

[PowerUp实战利用](#t5)

[可信任服务路径漏洞提权](#t6)

[MSF下实战利用](#MSF%E4%B8%8B%E5%AE%9E%E6%88%98%E5%88%A9%E7%94%A8)

[自动安装配置文件提权](#t7)

[MSF下实战利用](#t8) 

[计划任务提权](#t9)

* * *

Windows错误配置提权
=============

当攻击者使用其他[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)方法无法提权时，就会利用系统中的配置错误来提权。Windows操作系统中的常见配置错误包括管理员凭据配置错误、服务配置错误、故意削弱的安全措施、用户权限过高等。

系统服务权限配置错误提权
------------

Windows系统服务文件在操作系统启动时加载和执行，并在后台调用可执行文件。因此，如果一个低权限的用户对此类系统服务调用的可执行文件拥有写权限，就可以将该文件替换成任意可执行文件，并随着系统服务的启动获得系统权限。Windows服务是以System权限运行的，因此，其文件夹，文件和注册表键值都是受强访问控制机制保护的。但是，在某些情况下，操作系统中仍然存在一些没有得到有效保护的服务。

系统服务权限配置错误(可写目录漏洞)有如下两种可能：

*   服务未运行：攻击者会使用任意服务替换原来的服务，然后重启服务
*   服务正在运行且无法被终止：这种情况复合绝大多数的漏洞利用场景，攻击者通常会利用DLL劫持技术并尝试重启服务来提权

### PowerUp.ps1实战利用

PowerUp.ps1脚本是PowerSploit目录 Privsec 下的一个脚本，功能非常强大。拥有很多用来寻找目标主机Windows服务配置错误来进行提权的模块。当我们无法通过 windows 内核漏洞进行提权的话，这个时候我们就可以利用该脚本来寻找目标主机上Windows服务配置错误来进行提权，或者利用常见的系统服务，通过其继承的系统权限来完成提权。

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
*   Test-ServiceDaclPermission 模块提权 (该模块会检查所有可用的服务，并尝试对这些打开的服务进行修改，如果可修改，则存在此漏洞)。Windows系统服务文件在操作系统启动时会加载执行，并且在后台调用可执行文件。比如在每次重启系统时，Java升级程序都会检测出Oracle网站是否有新版Java程序。而类似Java程序之类的系统服务程序，在加载时往往都是运行在系统权限上的。所以如果一个低权限的用户对于此类系统服务调用的可执行文件具有可写的权限，那么就可以将其替换成我们的恶意可执行文件，从而随着系统启动服务器获得系统权限。

**检查是否存在配置错误漏洞(Invoke-Allchecks模块)**

```
powershell -exec bypass -c import-module .\PowerUp.ps1;Invoke-Allchecks -verbose
```


运行该脚本，该脚本会自动检查PowerUp.ps1下所有的模块，并在存在漏洞利用的模块下的AbuseFunction中直接给出利用方法。从图中可以看出，VlunService服务存在漏洞，可以被利用。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203225331367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是，我们执行下面的命令进行提权

```
write-servicebinary -servicename vulnservice -username "offensive\alice" -password Password!  #该命令会在本目录下生成一个service.exe文件，该文件启动时会将用户offensive\alice提权到管理员组内。需要注意的是，这里的username必须是域内已经存在的用户，而password的话，则随便填，可以是这个域用户的正确密码，也可以随便指定密码，这对该域用户的密码无影响      
cp .\service.exe "c:\program files\vuln service\vulnservice.exe"  #复制本路径下的service.exe文件到指定的程序
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203225542671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后重启系统，重启后会发现 offensive\\alice 在管理员组内了。(当然要想办法让机器重启)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019120322572328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### MSF下实战利用

在MSF中，对应的利用模块是 service\_permissions。选择 AGGRESSIVE 选项，可以利用目标机器上每一个由缺陷的服务。该选项被禁用时，该模块在第一次提权成功后就会停止工作。

service\_permissions 模块会使用两种办法来获得system权限：

*   如果当前的已经是管理员权限，则该模块会尝试创建并运行一个新的服务
*   如果当前是低权限，则该模块会判断哪些服务的文件或文件夹的权限有问题，并允许对其进行劫持。在创建服务或者劫持已经存在的服务时，该模块会创建一个可执行程序，其文件名和安装路径都是随机的。

注册表键AlwaysInstallElevated提权
---------------------------

Windows Installer是Windows操作系统的组件之一，专门用来管理和配置软件服务。Windows Installer除了是一个安装程序，还用于管理软件的安装、管理软件组件的添加和删除，监视文件的还原、通过回滚进行灾难恢复等。Windows Installer分为客户端安装服务(Msiexec.exe)和MSI文件两部分，它们是一起工作的Windows Installer通过Msiexec.exe安装MSI文件包含的程序。MSI是Windows Installer的数据表，它实际上是一个数据库，包含安装和卸载软件时需要使用大量的指令和数据。Msiexec.exe用于安装MSI文件，一般在运行Microsoft Update安装更新或安装一些软件的时候使用，占用内存较多。简单的说，双击MSI文件就会运行Msiexec.exe。

注册表键AlwaysInstallElevated是一个策略设置项。Windows允许低权限用户已System权限运行安装文件。如果启用此策略设置项，那么任何权限的用户都能以System权限来安装恶意的MSI(MicrosoftWindows Installer)文件。

**AlwaysInstallElevated提权原因**

该漏洞产生的原因是用户开启了Windows Installer特权安装功能。

### **PowerUp实战利用**

可信任服务路径漏洞提权
-----------

可信任服务路径(包含空格且没有引号的路径)漏洞利用了Windows文件路径解析的特性，并涉及服务路径的文件、文件夹权限(存在缺陷的服务程序利用了属于可执行文件的文件/文件夹的权限)。如果一个服务调用的可执行文件没有正确地处理所引用的完整路径名，这个漏洞就会被攻击者用来上传任意可执行文件。也就是说，如果一个服务的可执行文件的路径没有被双引号引起来且包含空格，那么这个服务就是有漏洞的。

该漏洞存在如下两种可能性：

*   如果路径与服务有关，就任意创建一个服务或者编译Service模板。
*   如果路径与可执行文件有关，就任意创建一个可执行文件。

**可信任服务路径漏洞产生的原因**

因为Windows服务通常都是以System权限运行的，所以系统在解析服务所对应的文件路径中的空格时，也会以系统权限运行。例如，有一个文件路径"C:\\Program Files\\Some Folder\\Service.exe"，对于该路径中的每一个空格，Windows都会尝试寻找并执行与空格前面的名字相匹配的程序。操作系统会对文件路径中空格的所有可能情况进行尝试，直至找到一个能够匹配的程序。在本例中，Windows会依次尝试确定和执行下列程序：

*   C:\\Program.exe
*   C:\\Program Files\\Some.exe
*   C:\\Program Files\\Some Folder\\Service.exe

因此，如果一个被“适当”命名的可执行程序被上传到受影响的目录中，服务一旦重启，该程序就会以System权限运行。

### MSF下实战利用

自动安装配置文件提权
----------

网络管理员在内网中给多台机器配置同一个环境时，通常不会逐台配置，而会使用脚本化批量部署的方法。在这一过程中，会使用安装配置文件。这些文件中包含所有的安装配置信息，其中的一些还可能包含本地管理员账号和密码等信息。这些文件列举如下(可以对整个系统进行检查)：

*   C:\\sysprep.inf
*   C:\\sysprep\\sysprep.xml
*   C:\\Windows\\system32\\sysprep.inf
*   C:\\Windows\\system32\\sysprep\\sysprep.xml
*   C:\\unattend.xml
*   C:\\Windows\\Panther\\Unattend.xml
*   C:\\Windows\\Panther\\Unattended.xml
*   C:\\Windows\\Panther\\Unattend\\Unattend.xml
*   C:\\Windows\\Panther\\Unattend\\Unattended.xml
*   C:\\Windows\\system32\\Sysprep\\Unattend.xml
*   C:\\Windows\\system32\\Sysprep\\Panther\\Unattend.xml

也可以执行如下命令，搜索Unattend.xml文件

```
dir /b /s c:\Unattend.xml
```


打开Unattend.xml，查看其中是否有明文密码或者经常base64编码的密码。

### MSF下实战利用 

计划任务提权
------

可以使用如下命令查看计算机的计划任务

```
schtasks /query /fo LIST /v
```


相关文章：[metasploit、powershell之Windows错误系统配置漏洞实战提权](https://mp.weixin.qq.com/s?__biz=MzU1NjgzOTAyMg==&mid=2247485548&idx=1&sn=47778d34ecc9932e489764e19b52d95c&chksm=fc3fb16dcb48387b9f2c8762bc0baa74a126a367aa5425f67dafd54fab3938dbeff009f8d138&scene=0&xtrack=1&key=f1120516e6597816a6e9626bec579a9c62c048c6f75fd840840790258ca9ccdcb3a1a4472ffb441de6bd84c2e85e0318e17750fad2e4d3f2b02d9a3fb3abd0e7a5f71996c56b7301016b8e20106ae414&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=6208006f&lang=zh_CN&exportkey=Axx8yJlx1p8h1JnBqarNRZU%3D&pass_ticket=4fz6iStV%2BygAPKE4hHfKAmlAmui%2FtCJF8RZVmcB9FDg50oYPAd71frZMW%2FV%2BjnKY)

未完待续。。