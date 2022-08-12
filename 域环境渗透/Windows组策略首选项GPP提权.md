**目录**

[SYSVOL](#t0 "SYSVOL")

[组策略首选项提权分析](#t1 "组策略首选项提权分析")

[1：创建组策略，批量修改域中机器的本地管理员密码](#t2 "1：创建组策略，批量修改域中机器的本地管理员密码")

[2：获取组策略的凭据](#t3 "2：获取组策略的凭据")

[（1）使用PowerShell获取cpassword](#t4 "（1）使用PowerShell获取cpassword")

[（2）使用MSF获取cpassword](#t5 "（2）使用MSF获取cpassword")

[针对组策略首选项提权的防御措施](#t6 "针对组策略首选项提权的防御措施")

* * *

SYSVOL
======

SYSVOL是活动目录里面的一个用于存储域公共文件服务器副本的共享文件夹，在域中的所有[域控制器](https://so.csdn.net/so/search?q=%E5%9F%9F%E6%8E%A7%E5%88%B6%E5%99%A8&spm=1001.2101.3001.7020)之间进行复制。SYSVOL文件夹是在安装活动目录时自动创建的，主要用来存放登录脚本、组策略数据及其他域控制器需要的域信息等。SYSVOL在所有经过身份验证的域用户或者域信任用户具有读权限的活动目录的域范围内共享。整个SYSVOL目录在所有的域控制器中是自动同步和共享的，所有的域策略均存放在 C:\\Windows\\SYSVOL\\DOMAIN\\Policies\\ 目录中。

![](https://img-blog.csdnimg.cn/20200217170807243.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200217183214988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在一般的域环境中，所有机器都是脚本化批量部署的，数据量通常很大。为了方便地对所有的机器进行操作，网络管理员往往会使用域策略进行统一的配置和管理。大多数组织在创建域环境后，会要求加入域的计算机使用域用户密码进行登录验证。为了保证本地管理员密码的安全性，这些组织的网络管理员往往会修改本地管理员密码。

尽管如此，安全问题依旧存在。通过[组策略](https://so.csdn.net/so/search?q=%E7%BB%84%E7%AD%96%E7%95%A5&spm=1001.2101.3001.7020)统一修改的密码，虽然强度有所提高，但所有机器的本地管理员密码是相同的。攻击者获得了一台机器的本地管理员密码，就相当于获得了整个域中所有机器的本地管理员密码。

 常见的组策略首选项(Group Policy Preferences,GPP)列举如下：

*   映射驱动器(Drives.xml)
*   创建本地用户
*   数据源(DataSources.xml)
*   打印机配置(Printers.xml)
*   创建、更新服务(Services.xml)
*   计划任务(ScheduledTasks.xml)

组策略首选项提权分析
==========

**1：创建组策略，批量修改域中机器的本地管理员密码**
----------------------------

在域控上执行：开始——>运行——>输入 gpmc.msc ——>选择xie.com——>右键组策略对象——>新建，我这里新建一个test组策略

![](https://img-blog.csdnimg.cn/20200217174253450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

右键刚刚新建的组策略，编辑

![](https://img-blog.csdnimg.cn/20200217174407453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

会弹出一个组策略管理编辑器，然后 计算机配置——>首选项——>控制面板设置——>本地用户和组

![](https://img-blog.csdnimg.cn/20200217174935367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

右键本地用户和组——>新建——>本地用户

我们将域中每个计算机的本地administrator用户更名为 admin，并且设置新的密码 root@123456 

![](https://img-blog.csdnimg.cn/20200217174745864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后将Domain Computers添加到组策略应用的组中

![](https://img-blog.csdnimg.cn/2020021717593699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

强制更新组策略 gpupdate /force

![](https://img-blog.csdnimg.cn/20210111232059985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200217175627586.png)

**2：获取组策略的凭据**
--------------

管理员在域中新建一个组策略后，操作系统会自动在SYSVO共享目录中生成一个XML文件，该文件中保存了该组策略更新后的密码。该密码使用AES-256加密算法，安全性还是比较高的。但是，2012年微软在官方网站上公布了该密码的私钥，导致保存在XML文件中的密码的安全性大大降低。任何域用户和域信任的用户均可对该共享目录进行访问，这就意味着，任何用户都可以访问保存在XML文件中的密码并将其解密，从而控制域中所有使用该账号、密码的本地管理员计算机。在SYSVOL中搜索，可以找到Groups.xml文件。找到其中的cpassword字段，该字段是用AES-256算法加密的，root@123456 加密后的密文为  Yw6hqEEKPZtyKUXpWd5UhxEZ13DAe64s9w8U7Sq55uw 。

```
C:\Windows\SYSVOL\domain\Policies\{ABDAFB3B-920B-4A1A-9B47-B0D8721244D4}\Machine\Preferences\Groups\Group.xml
```


![](https://img-blog.csdnimg.cn/20200217180201732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200217180238830.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **（1）使用PowerShell获取cpassword**

PowerSploit中的Get-GPPPassword.ps1脚本可以获取组策略中的密码。注意，我们只需要在域内任何一台以域用户权限登录的机器上均可查询到。

```
Import-Module .\Get-GPPPassword.ps1;Get-GPPPassword
```


![](https://img-blog.csdnimg.cn/20200217182908213.png)

### **（2）使用MSF获取cpassword**

MSF中  post/windows/gather/credentials/gpp  模块可以获取组策略中的密码。注意，我们只需要获取域内任何一台以域用户权限登录的机器的权限即可。

```
use post/windows/gather/credentials/gpp      
set session 1      
run
```


![](https://img-blog.csdnimg.cn/20200217183245448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

针对组策略首选项提权的防御措施
===============

在用于管理组策略的计算机上安装 KB2962486补丁，防止新的凭据被放置在组策略首选项中。微软在2014年修复了组策略首选项提权漏洞，使用的方法就是不再将密码保存在组策略首选项中。

此外，针对Everyone访问权限进行设置，具体如下：

*   设置共享文件夹SYSVOL的访问权限
*   将包含组策略密码的 XML 文件从 SYSVOL 目录中删除
*   不要把密码放在所有域用户都有权访问的文件中
*   如果需要更改域中机器的本地管理员密码，建议使用LAPS

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

官方：[MS14-025：组策略首选项中的漏洞可能允许特权提升：2014 年 5 月 13 日](https://support.microsoft.com/zh-cn/topic/ms14-025-%E7%BB%84%E7%AD%96%E7%95%A5%E9%A6%96%E9%80%89%E9%A1%B9%E4%B8%AD%E7%9A%84%E6%BC%8F%E6%B4%9E%E5%8F%AF%E8%83%BD%E5%85%81%E8%AE%B8%E7%89%B9%E6%9D%83%E6%8F%90%E5%8D%87-2014-%E5%B9%B4-5-%E6%9C%88-13-%E6%97%A5-60734e15-af79-26ca-ea53-8cd617073c30 "MS14-025：组策略首选项中的漏洞可能允许特权提升：2014 年 5 月 13 日")