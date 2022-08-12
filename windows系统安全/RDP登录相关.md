**目录**

[查询和修改RDP状态](#t0 "查询和修改RDP状态")

[RDP开放端口](#t1 "RDP开放端口")

[手动修改RDP开放端口](#t2 "手动修改RDP开放端口")

[RDP历史登录凭据](#t3 "RDP历史登录凭据")

[事件ID(Event ID)&事件类型(Event Type)](#t4 "事件ID(Event ID)&事件类型(Event Type)")

[RDP登录成功历史日志](#t5 "RDP登录成功历史日志")

[RDP登录失败历史日志](#t6 "RDP登录失败历史日志")

* * *

在红蓝对抗中，不管是红队还是蓝队，都需要对[RDP](https://so.csdn.net/so/search?q=RDP&spm=1001.2101.3001.7020)远程桌面的知识具有很深的了解。

### 查询和修改RDP状态

首先肯定就需要判断RDP是否开启了。我们通过注册表“HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Terminal Server”的**fDenyTSConnections**值判断RDP是否开启。

![æ³¨åè¡¨ç¼è¾å¨ï¼å¶ä¸­æ¾ç¤ºäº fDenyTSConnections æ¡ç®](https://img-blog.csdnimg.cn/img_convert/8a5a339fefa100036ad8cca2a6632c8a.png)

*   如果 **fDenyTSConnections** 项的值为 **0**，则表明已启用 RDP。
*   如果 **fDenyTSConnections** 项的值为 **1**，则表明已禁用 RDP。

我们可以使用CheckRdpStatus.ps1脚本查询RDP的状态

```
Import-Module .\CheckRdpStatus.ps1      
CheckRdpStatus
```


![](https://img-blog.csdnimg.cn/20201214232141327.png)

我们可以使用命令开启或关闭RDP状态，但是以下命令会被杀毒软件拦截

```
开启RDP服务：      
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f       
关闭RDP服务      
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000001 /f
```


或者也可以使用RegfDenyTSConnections.ps1脚本开启或关闭RDP状态，通过powershell脚本开启或关闭RDP，不会被杀毒软件拦截

```
开启RDP服务：      
Import-Module RegfDenyTSConnections.ps1      
RegfDenyTSConnections 0       
关闭RDP服务：      
Import-Module RegfDenyTSConnections.ps1      
RegfDenyTSConnections 1
```


### RDP开放端口

RDP默认端口为3389，但是，在很多情况下，运维人员会将RDP端口修改为其他端口。那么，我们就需要知道RDP修改后的真正端口了。

可以通过执行以下命令查询

```
tasklist /svc | findstr TermService   #查找RDP进程的PID      
netstat -ano  | findstr 上一步查询到的PID  #过滤上一步的PID，查找出对应的端口
```


![](https://img-blog.csdnimg.cn/20201214215340166.png)

也可以使用RegRdpPort.ps1脚本查询

```
Import-Module .\RegRdpPort.ps1      
RegRdpPort
```


![](https://img-blog.csdnimg.cn/20201214215514328.png)

### 手动修改RDP开放端口

RDP端口号由以下注册表决定

```
HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\PortNumber
```


手动编辑该值，如下，十进制修改为3489。

然后重启计算机

### RDP历史登录凭据

传送门：[获取RDP登录凭据](https://xie1997.blog.csdn.net/article/details/107068706 "获取RDP登录凭据")

### 事件ID(Event ID)&事件类型(Event Type)

**事件 ID（Event ID）**

| Event ID(2000/XP/2003) | Event ID(Vista/7/8/2008/2012) | 描述 |
| --- | --- | --- |
| 528 | 4624 | 成功登录 |
| 529 | 4625 | 失败登录 |
| 680 | 4776 | 成功/失败的账户认证 |
| 624 | 4720 | 创建用户 |
| 636 | 4732 | 添加用户到启用安全性的本地组中 |
| 632 | 4728 | 添加用户到启用安全性的全局组中 |
| 2934 | 7030 | 服务创建错误 |
| 2944 | 7040 | IPSEC服务服务的启动类型已从禁用更改为自动启动 |
| 2949 | 7045 | 服务创建 |

  
  
**事件类型(EventType)**

| 登录类型 | 登录类型 | 描述 |
| --- | --- | --- |
| 2 | Interactive | 用户登录到本机 |
| 3 | Network | 用户或计算手机从网络登录到本机，如果网络共享，或使用 net use 访问网络共享，net view 查看网络共享 |
| 4 | Batch | 批处理登录类型，无需用户干预 |
| 5 | Service | 服务控制管理器登录 |
| 7 | Unlock | 用户解锁主机 |
| 8 | NetworkCleartext | 用户从网络登录到此计算机，用户密码用非哈希的形式传递 |
| 9 | NewCredentials | 进程或线程克隆了其当前令牌，但为出站连接指定了新凭据 |
| 10 | Remotelnteractive | 使用终端服务或远程桌面连接登录 |
| 11 | Cachedlnteractive | 用户使用本地存储在计算机上的凭据登录到计算机（域控制器可能无法验证凭据），如主机不能连接域控，以前使用域账户登录过这台主机，再登录就会产生这样日志 |
| 12 | CachedRemotelnteractive | 与 Remotelnteractive 相同，内部用于审计目的 |
| 13 | CachedUnlock | 登录尝试解锁 |

### RDP登录成功历史日志

这里登录成功是指其他机器登录该机器成功登录的日志。

管理工具——>事件查看器

![](https://img-blog.csdnimg.cn/20201214220357632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Windows日志——>安全 ，右边查看事件ID为4624的是登录成功的日志

![](https://img-blog.csdnimg.cn/20201214221621743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201214221525660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们可以执行命令查询

```
wevtutil qe security /q:"*[EventData[Data[@Name='LogonType']='10'] and System[(EventID=4624)]]" /f:text /rd:true /c:10
```


![](https://img-blog.csdnimg.cn/20201214221807198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

也可以使用EventLogSuccess.ps1脚本查询

```
Import-Module .\EventLogSuccess.ps1      
EventLogSuccess
```


 ![](https://img-blog.csdnimg.cn/20201214221905568.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### RDP登录失败历史日志

这里登录失败是指其他机器尝试RDP登录该机器，但是登录失败的日志。

事件ID为4625的是登录失败的日志

![](https://img-blog.csdnimg.cn/20201214221525660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们可以使用EventLogFailed.ps1脚本进行查询。当同一时刻，有很多登录失败的日志，说明存在爆破RDP的情况。

```
Import-Module .\EventLogFailed.ps1      
EventLogFailed
```


![](https://img-blog.csdnimg.cn/20201214222433437.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 相关文章：[LogParse-Windows系统日志分析](https://blog.csdn.net/weixin_30663471/article/details/98886535 "LogParse-Windows系统日志分析")

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)