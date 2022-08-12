**目录**

[环境安装准备](#t0 "环境安装准备")

[安装Exchange Server2016](#t1 "安装Exchange Server2016")

[Exchange添加用户](#t2 "Exchange添加用户")

* * *

环境安装准备
------

1：以管理员身份运行Windows Powershell，安装必需的 Windows组件：

```
Install-WindowsFeature NET-Framework-45-Features, Server-Media-Foundation, RPC-over-HTTP-proxy, RSAT-Clustering, RSAT-Clustering-CmdInterface, RSAT-Clustering-Mgmt, RSAT-Clustering-PowerShell, WAS-Process-Model, Web-Asp-Net45, Web-Basic-Auth, Web-Client-Auth, Web-Digest-Auth, Web-Dir-Browsing, Web-Dyn-Compression, Web-Http-Errors, Web-Http-Logging, Web-Http-Redirect, Web-Http-Tracing, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Lgcy-Mgmt-Console, Web-Metabase, Web-Mgmt-Console, Web-Mgmt-Service, Web-Net-Ext45, Web-Request-Monitor, Web-Server, Web-Stat-Compression, Web-Static-Content, Web-Windows-Auth, Web-WMI, Windows-Identity-Foundation, RSAT-ADDS
```


![](https://img-blog.csdnimg.cn/20200708235159720.png)

 2：安装.NET Framework 4.7.2或更高版本（微软官网）：[Download .NET Framework | Free official downloads](https://dotnet.microsoft.com/download/dotnet-framework "Download .NET Framework | Free official downloads")

3：安装Visual C++ Redistributable Package for Visual Studio 2013：

4：安装Microsoft统一通信托管API 4.0 核心运行时（64 位）：[Download Unified Communications Managed API 4.0 Runtime from Official Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=34992 "Download Unified Communications Managed API 4.0 Runtime from Official Microsoft Download Center")​​​​​​​

 安装Exchange Server2016
----------------------

先去下载Exchange Server2016，地址：

```
ed2k://|file|mu_exchange_server_2016_x64_dvd_7047456.iso|1810948096|53E8C34083D3290A6A4AE8D82BA2C57F|/
```


下载了iso文件后，用压缩软件解压，双击里面的 EXCHANGE2016-X64

![](https://img-blog.csdnimg.cn/20200708233237290.png)

 然后它会自动将所有安装文件释放出来，双击setup.exe

![](https://img-blog.csdnimg.cn/20200708233426975.png)

 勾选现在不检查更新

![](https://img-blog.csdnimg.cn/20200708233556848.png)

 下一步

![](https://img-blog.csdnimg.cn/20200709112927298.png)

 勾选我接受许可协议中的条款(A)

![](https://img-blog.csdnimg.cn/2020070911302197.png)

 勾选使用推荐设置(U)

![](https://img-blog.csdnimg.cn/20200709113047719.png)

 勾选邮箱角色(M)

![](https://img-blog.csdnimg.cn/20200709113127829.png)

 安装空间和位置默认即可，下一步

![](https://img-blog.csdnimg.cn/20200709113138500.png)

 Exchange组织默认即可，下一步

![](https://img-blog.csdnimg.cn/20200709113152530.png)

 勾选是，下一步

![](https://img-blog.csdnimg.cn/2020070911320851.png)

 检查先决条件完成，然后点击安装

![](https://img-blog.csdnimg.cn/2020071015142933.png)

 看到如下说明安装完成了，这里勾选在完成Exchange安装后启动Exchange管理中心

![](https://img-blog.csdnimg.cn/20200710155327854.png)

 点击完成后，浏览器会自动跳到 Exchange 管理中心，如果没有自动跳转，我们可以手动输入URL：https://localhost/ecp  ，对Exchange 2016 进行相应管理。这里的账号密码填域管理员的账号密码即可登录

![](https://img-blog.csdnimg.cn/20200710155706127.png)

选择时区

![](https://img-blog.csdnimg.cn/20200712170148240.png)

Exchange添加用户
------------

访问 https://localhost/ecp 登陆后，如下添加新用户，选择已有的域账号即可。

![](https://img-blog.csdnimg.cn/20200712170335311.png)

![](https://img-blog.csdnimg.cn/20200712170508161.png)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)