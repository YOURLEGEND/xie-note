**目录**

[Windows访问控制模型](#t0 "Windows访问控制模型")

[ACE](#t1 "ACE")

[ACL的判断流程](#t2 "ACL的判断流程")

[文件ACL的查看和修改](#t3 "文件ACL的查看和修改")

[图形化查看ACL](#t4 "图形化查看ACL")

[icacls命令行查看和修改ACL](#t5 "icacls命令行查看和修改ACL")

[Powershell命令查看ACL](#t6 "Powershell命令查看ACL ") 

[域中的ACL](#t7 "域中的ACL")

 [adfind查询过滤ACL](#%C2%A0adfind%E6%9F%A5%E8%AF%A2%E8%BF%87%E6%BB%A4ACL " adfind查询过滤ACL")

[枚举 ACL配置](#t8 "枚举 ACL配置")

* * *

Windows访问控制模型
-------------

简单来说，访问控制是指某 主体(subject) 允许或不被允许对某 实体(object) 执行读取、写入、删除、更改等操作。在Windows中，主体subject通常是进程；实体object通常指安全对象(securable object)，可能是：

*   文件
*   目录
*   管道
*   服务
*   进程
*   Active Directory对象
*   注册表
*   打印机
*   共享等

访问控制即对访问行为是否具有合法权限进行判定并执行相应的策略，而访问行为实际上就是主体subject访问实体object的过程。Windows操作系统会为访问行为的主体(subject)创建**访问令牌（Access Token）**，且当访问行为的实体(object)被创建时操作系统会为其创建一个**安全描述符（Security Descriptor）**，ACL对权限的访问控制正是通过 **访问令牌** 和 **安全描述符** 来完成的。

Windows访问控制模型由如下两个部分组成：

**访问令牌（Access Token）**：其中包含有关登录用户的信息。当用户登录时，系统会验证用户的帐户名称和密码。如果登录成功，系统将创建一个访问令牌。代表此用户执行的每个进程都将具有此访问令牌的副本。访问令牌包含安全标识符SID(Security Identifiers) ，用于标识用户的帐户和用户所属的所有组帐户。令牌还包含用户或用户组所持有的特权列表。当进程试图访问安全对象或执行需要特权的系统管理任务时，系统使用此访问令牌来标识关联用户。传送门：[令牌(Token)](https://xie1997.blog.csdn.net/article/details/103965659#%E4%BB%A4%E7%89%8C%28Token%29 "令牌(Token)")

**安全描述符（Security Descriptor）**：包含保护安全对象的安全信息。当创建安全对象时，系统会为其分配一个安全描述符，安全描述符包含了该对象的属主对该对象所配置的一些安全属性和策略，安全描述符由4部分组成：

*   SID(Security Identifiers)：标识该对象拥有的SID
*   DACL(Discretionary Access Control List)：DACL定义对象的访问控制策略（允许或拒绝访问），DACL的ACE定义了哪些用户(组)对该对象具有怎样的访问权限，当请求访问该对象时，系统检查请求方的SID与对象DACL中的ACE配置策略，根据找到的ACE配置策略授予访问或拒绝访问。如果该对象没有设置DACL，系统默认允许所有访问操作，如果对象配置了DACL但是没有配置ACE条目，系统将拒绝所以访问操作；如果系统配置了DACL和ACE，系统将按顺序检查ACE，直到找到一个或多个允许所有请求访问权限的ACE，或者直到任何请求的访问权限被拒绝为止。关于DACL如何控制对对象的访问更多详细信息：[How AccessCheck Works](https://docs.microsoft.com/zh-cn/windows/win32/secauthz/how-dacls-control-access-to-an-object "How AccessCheck Works") 。关于如何创建DACL：[Creating a DACL](https://docs.microsoft.com/en-us/windows/win32/secbp/creating-a-dacl "Creating a DACL")
*   SACL(System Access Control List)：SACL定义对象的访问行为记录策略，SACL的ACE定义了对哪些用户(组)的哪些访问行为进行日志记录，例如对指定用户的访问成功、失败行为进行审计记录日志。更多的关于SACL：[SACL Access Right](https://docs.microsoft.com/zh-cn/windows/win32/secauthz/sacl-access-right "SACL Access Right")  和 [Audit Generation](https://docs.microsoft.com/zh-cn/windows/win32/secauthz/audit-generation "Audit Generation")
*   Flag：其他标志位信息

使用函数操作安全描述符、SID和ACL的内容，而不是直接访问它们。这有助于确保这些结构保持语法准确性，并防止未来对安全系统的增强破坏现有代码。

ACL微软官方文档：[Access Control Lists - Win32 apps | Microsoft Docs](https://docs.microsoft.com/en-us/windows/desktop/SecAuthZ/access-control-lists "Access Control Lists - Win32 apps | Microsoft Docs")

ACE
---

ACL访问控制列表由一系列访问控制实体（ACE，Access Control Entries）组成，每个ACE可以看作是配置的一条访问策略。每个ACE指定了一组访问权限，并包含一个SID，该ID标识了允许、拒绝或审计这些权利的委托人。委托人可以是用户帐户、群组帐户或_登录会话_。

![](https://img-blog.csdnimg.cn/20201012111203245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以把一条ACE归纳为四个方面。

1.  谁对你有权限
2.  有什么权限
3.  是允许权限还是拒绝权限
4.  这个权限能不能被继承

第一点谁对你有权限和第三点是允许还是拒绝权限这个比较好理解。

我们现在来看看第二点有什么权限，这可以分为3种权限：

*   通用权限：就是对这个条目的通用权限，通用读，通用写等。
*   对某个属性的权限：一个条目包含若干个属性，通用权限是对整个条目的权限。域内的ACL同时也支持某个属性的权限。
*   扩展权限：前面说的都是读写执行权限，但是域内的安全对象相对较为复杂，读写执行权限是不够用的，域内的ACL也支持扩展权限，比如强制更改密码。扩展权限都存储在`CN=Extended-Rights,CN=Configuration,DC=xie,DC=com`里面,具体值在rightsGuid这个属性里面

![](https://img-blog.csdnimg.cn/20210217132121705.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

至于第四点这个权限能不能被继承也很好理解，就是这个ACE能不能被这个目录下的文件或目录所继承。

ACL的判断流程
--------

ACL的解析流程如下：

*   明确定义的Deny ACE
*   明确定义的Allow ACE
*   继承的Deny ACE
*   继承的Allow ACE

当每条ACE都没匹配上的时候，是拒绝访问的。值得注意的是，这里有两种情况：

*   有DACL，但是ACE条目的数量为0，这种情况是不允许任何用户访问的
*   没有DACL的情况，这种情况是允许任何用户访问的

我们接下来举个例子来说明：

```
主体subjectA：      
	sid=100      
	groupsid=110      
	groupsid=120      
主体subjectB：      
	sid=200      
	groupsid=210      
	groupsid=220      
主体subjectC：      1
	sid=300      1
	groupsid=310      1
	groupsid=320       1
安全对象实体ObjectD：      1
	ACE：拒绝sid=200的对象访问      1
    ACE：允许sid=100和sid=210的对象访问
```


当主体subjectA访问安全对象实体ObjectD时，首先匹配第一条拒绝sid=200的访问，没匹配上，然后匹配第二条允许sid=100和sid=210的对象访问，匹配上了，所以允许主体A访问。

当主体subjectB访问安全对象实体ObjectD时，首先匹配第一条拒绝sid=200的访问，匹配上，拒绝主体A对D的访问。即使第二条ACE允许了sid=210的对象访问匹配上了，但是因为拒绝的ACE优先级大于允许的ACE，所以最后还是拒绝主体B访问。

当主体subjectC访问安全对象实体时，第一条ACE和第二条ACE都没匹配上，所以拒绝主体C访问。

文件ACL的查看和修改
-----------

官方文档：[Getting Information from an ACL](https://docs.microsoft.com/zh-cn/windows/win32/secauthz/getting-information-from-an-acl "Getting Information from an ACL")

为了确保ACL在语义上是正确的，尽量使用适当的函数来创建和操作ACL。官方文档：[Creating or Modifying an ACL](https://docs.microsoft.com/zh-cn/windows/win32/secauthz/creating-or-modifying-an-acl "Creating or Modifying an ACL")

### **图形化查看ACL**

右键 任意文件或文件夹属性——>安全——>高级——>权限，就看到该文件或文件夹的DACL。再双击任意一条DACL，则可以看到该条DACL的ACE。

![](https://img-blog.csdnimg.cn/20201012141545210.png)

### **icacls命令行查看和修改ACL**

使用 icacls 命令即可查看ACL。

![](https://img-blog.csdnimg.cn/20201012141954182.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看指定文件ACL**

```
icacls C:\Windows\System32\config\sam
```


![](https://img-blog.csdnimg.cn/20201012142126268.png)

**备份指定文件或文件夹的ACL**

我们这里有一个文件夹ACL\_Test，该文件夹下有一个文件test.txt。我们现在备份该ACL\_Test文件夹的ACL，其会备份当前目录下所有的文件和文件夹的ACL

```
icacls C:\Users\xie\Desktop\ACL_Test /save acl_bank /t
```


![](https://img-blog.csdnimg.cn/20201012143153880.png)

**添加用户hack对指定文件/文件夹(包括当前目录及其子目录中的文件)的完全访问权限**

```
icacls C:\Windows\System32\config /grant hack:(OI)(CI)(F) /t       
#(OI)代表对象继承 (CI)代表容器继承 (F)代表完全访问
```


**移除用户hack对指定文件/文件夹(包括当前目录及其子目录中的文件)的完全访问权限** 

```
icacls C:\Windows\System32\config /remove hack /t
```


### Powershell命令查看ACL 

**查看指定文件的ACL**

```
Get-Acl -Path 'C:\Windows\System32\config\sam'| Format-Table -wrap
```


![](https://img-blog.csdnimg.cn/20201012162956173.png)

**添加用户hack对指定文件的完全访问权限**

```
function Add-ACL{      
    [CmdletBinding()]                 
    Param (      
        [Parameter(Mandatory = $True)]      
        [String]      
        [ValidateNotNullOrEmpty()]      
        $Path      
    )       1
    $acl = Get-Acl -Path $Path      1
    $person = [System.Security.Principal.NTAccount]"hack"      1
    $access = [System.Security.AccessControl.FileSystemRights]"FullControl"      1
    $inheritance = [System.Security.AccessControl.InheritanceFlags]"ObjectInherit,ContainerInherit"      1
    $propagation = [System.Security.AccessControl.PropagationFlags]"None"      1
    $type = [System.Security.AccessControl.AccessControlType]"Allow"      1
    $rule = New-Object System.Security.AccessControl.FileSystemAccessRule( `      1
    $person,$access,$inheritance,$propagation,$type)      1
    $acl.AddAccessRule($rule)      1
    Set-Acl $Path $acl      2
}      2
Add-ACL -Path 'C:\Windows\System32\config\sam'
```


**移除用户hack对指定文件的完全访问权限** 

```
function Remove-ACL{      
    [CmdletBinding()]                 
    Param (      
        [Parameter(Mandatory = $True)]      
        [String]      
        [ValidateNotNullOrEmpty()]      
        $Path      
    )       1
    $acl = Get-Acl -Path $Path      1
    $person = [System.Security.Principal.NTAccount]"hack"      1
    $access = [System.Security.AccessControl.FileSystemRights]"FullControl"      1
    $inheritance = [System.Security.AccessControl.InheritanceFlags]"ObjectInherit,ContainerInherit"      1
    $propagation = [System.Security.AccessControl.PropagationFlags]"None"      1
    $type = [System.Security.AccessControl.AccessControlType]"Allow"      1
    $rule = New-Object System.Security.AccessControl.FileSystemAccessRule( `      1
    $person,$access,$inheritance,$propagation,$type)      1
    $acl.RemoveAccessRule($rule)      1
    Set-Acl $Path $acl      2
}      2
Remove-ACL -Path 'C:\Windows\System32\config\sam'
```


**添加用户hack对指定文件夹(包括当前目录及其子目录中的文件)的完全访问权限** 

```
function Add-ACL{      
    [CmdletBinding()]                 
    Param (      
        [Parameter(Mandatory = $True)]      
        [String]      
        [ValidateNotNullOrEmpty()]      
        $Path      
    )       1
    $acl = Get-Acl -Path $Path      1
    $person = [System.Security.Principal.NTAccount]"hack"      1
    $access = [System.Security.AccessControl.FileSystemRights]"FullControl"      1
    $inheritance = [System.Security.AccessControl.InheritanceFlags]"None"      1
    $propagation = [System.Security.AccessControl.PropagationFlags]"None"      1
    $type = [System.Security.AccessControl.AccessControlType]"Allow"      1
    $rule = New-Object System.Security.AccessControl.FileSystemAccessRule( `      1
    $person,$access,$inheritance,$propagation,$type)      1
    $acl.AddAccessRule($rule)      1
    Set-Acl $Path $acl      2
}      2
Add-ACL -Path 'C:\Windows\System32\config\'      2
$fileList = Get-ChildItem 'C:\Windows\SYSVOL\sysvol\test.com' -recurse      2
Foreach($file in $fileList)      2
{      2
    $file.fullname      2
    Add-ACL -Path $file.fullname      2
}
```


**移除用户hack对指定文件夹(包括当前目录及其子目录中的文件)的完全访问权限**

```
function Remove-ACL{      
    [CmdletBinding()]                 
    Param (      
        [Parameter(Mandatory = $True)]      
        [String]      
        [ValidateNotNullOrEmpty()]      
        $Path      
    )       1
    $acl = Get-Acl -Path $Path      1
    $person = [System.Security.Principal.NTAccount]"hack"      1
    $access = [System.Security.AccessControl.FileSystemRights]"FullControl"      1
    $inheritance = [System.Security.AccessControl.InheritanceFlags]"None"      1
    $propagation = [System.Security.AccessControl.PropagationFlags]"None"      1
    $type = [System.Security.AccessControl.AccessControlType]"Allow"      1
    $rule = New-Object System.Security.AccessControl.FileSystemAccessRule( `      1
    $person,$access,$inheritance,$propagation,$type)      1
    $acl.RemoveAccessRule($rule)      1
    Set-Acl $Path $acl      2
}      2
Remove-ACL -Path 'C:\Windows\System32\config\'      2
$fileList = Get-ChildItem 'C:\Windows\SYSVOL\sysvol\test.com' -recurse      2
Foreach($file in $fileList)      2
{      2
    Remove-ACL -Path $file.fullname      2
}
```


域中的ACL
------

ACL还提供对Active Directory目录服务对象的访问控制。 活动目录服务接口(ADSI，Active Directory Service Interfaces)包括创建和修改这些ACL内容的例子。 官方文档：[Controlling object access in Active Directory Domain Services](https://docs.microsoft.com/en-us/windows/win32/ad/controlling-access-to-objects-in-active-directory-domain-services "Controlling object access in Active Directory Domain Services")

Powershell调用ADSI的参考资料：[Using PowerShell to assign permissions on Active Directory objects](https://social.technet.microsoft.com/Forums/windowsserver/en-US/df3bfd33-c070-4a9c-be98-c4da6e591a0a/forum-faq-using-powershell-to-assign-permissions-on-active-directory-objects?forum=winserverpowershell "Using PowerShell to assign permissions on Active Directory objects")

Empire中的powerview.ps1脚本已经集成了ACL的相关功能,以普通域用户即可执行以下命令

```
import-module powerview.ps1       
#获得当前域内所有对象      
Get-DomainObject -Domain xie.com       
#获得当前域内所有对象的ACL      
Get-DomainObjectAcl -Domain xie.com       
#获得指定用户的ACL      1
Get-DomainUser hack       1
#添加用户hack对指定对象(guid)的完全访问权限      1
Add-DomainObjectAcl -TargetIdentity '483e9973-2d45-4e2f-b034-f272a26950e0' -PrincipalIdentity hack -Rights All       1
#移除用户hack对指定对象(guid)的完全访问权限      1
Remove-DomainObjectAcl -TargetIdentity '483e9973-2d45-4e2f-b034-f272a26950e0' -PrincipalIdentity hack -Rights All       1
#添加指定用户hack对域具有dcsync权限      2
Add-DomainObjectAcl -TargetIdentity 'DC=xie,DC=com' -PrincipalIde hack -Rights DCSync -Verbose
```


![](https://img-blog.csdnimg.cn/20201012165913390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201012170010964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201012170104988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

###  adfind查询过滤ACL

adfind查询过滤有两种方式，一种是直接-b指定对象，另一种是使用 `-sddlfilter 参数过滤对象。`

`使用-b参数的话，`命令如下，使用+更容易阅读，最高三个+。

`使用-sddlfilter 参数过滤对象的话，格式如下:`

```
-sddlfilter ;;;;;
```


后面跟的参数对应的是ace条目相应的参数，值得注意的是，过滤的格式跟输出的格式要保持一致：

*   如果`-rawsddl` ，最后一个参数是sid，这个时候用`-sddlfilter`进行过滤的话，最后一个参数就要用sid的形式。
*   如果是`-sddl+++`，最后一个参数是已经解析后的账号名，这个时候用`-sddlfilter`进行过滤的话，最后一个参数就要用账号名的形式。

adfind查询过滤命令：

```
#查找指定对象的ACL      
AdFind.exe -b "CN=win7,CN=Computers,DC=xie,DC=com" nTSecurityDescriptor  -sddl      
AdFind.exe -b "CN=win7,CN=Computers,DC=xie,DC=com" nTSecurityDescriptor  -sddl+++       
#查找某个对象在域内的ACL权限      
AdFind.exe -s subtree -b "DC=xie,DC=com" nTSecurityDescriptor  -sddl+++   -sddlfilter ;;;;;"xie\win2008$" -recmute
```


![](https://img-blog.csdnimg.cn/20210216213826544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210217122352196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 **查找域内具备dcsync 权限的用户**

关于dcsync权限，传送门：[域渗透之Dcsync](https://xie1997.blog.csdn.net/article/details/108988491 "域渗透之Dcsync")

域内对象只需要具备以下这两个权限，就拥有dcsync的权限。

```
#复制目录更改权限      
'DS-Replication-Get-Changes'     = 1131f6aa-9c07-11d1-f79f-00c04fc2dcd2      
#复制目录更改所有项权限      
'DS-Replication-Get-Changes-All' = 1131f6ad-9c07-11d1-f79f-00c04fc2dcd2
```


![](https://img-blog.csdnimg.cn/20210217123413953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
#查询CN=Extended-Rights,CN=Configuration,DC=xie,DC=com下rightsGuid=1131f6aa-9c07-11d1-f79f-00c04fc2dcd2的对象      
AdFind.exe -b "CN=Extended-Rights,CN=Configuration,DC=xie,DC=com" -f "rightsGuid=1131f6aa-9c07-11d1-f79f-00c04fc2dcd2" name       
#查询CN=Extended-Rights,CN=Configuration,DC=xie,DC=com下rightsGuid=1131f6ad-9c07-11d1-f79f-00c04fc2dcd2的对象      
AdFind.exe -b "CN=Extended-Rights,CN=Configuration,DC=xie,DC=com" -f "rightsGuid=1131f6ad-9c07-11d1-f79f-00c04fc2dcd2" name
```


![](https://img-blog.csdnimg.cn/20210217123252905.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后DS-Replication-Get-Changes和DS-Replication-Get-Changes-All的displayName分别为：Replicating Directory Changes 和 Replicating Directory Changes All

![](https://img-blog.csdnimg.cn/20210308151155253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210308151224465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
#查询displayName=Replicating Directory Changes对域xie.com的ACL      
adfind.exe -s subtree -b "DC=xie,DC=com" nTSecurityDescriptor -sddl+++ -sddlfilter ;;;"Replicating Directory Changes";; -recmute       
#查询displayName=Replicating Directory Changes All对域xie.com的ACL      
adfind.exe -s subtree -b "DC=xie,DC=com" nTSecurityDescriptor -sddl+++ -sddlfilter ;;;"Replicating Directory Changes All";; -recmute
```


![](https://img-blog.csdnimg.cn/20210308151556167.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下是查询域内具备Dcsync权限的对象 

```
AdFind.exe -s subtree -b "DC=xie,DC=com" nTSecurityDescriptor -sddl -sddlfilter ;;;"1131f6aa-9c07-11d1-f79f-00c04fc2dcd2";; -recmute -resolvesids      
AdFind.exe -s subtree -b "DC=xie,DC=com" nTSecurityDescriptor -sddl -sddlfilter ;;;"1131f6ad-9c07-11d1-f79f-00c04fc2dcd2";; -recmute -resolvesids
```


从图中我们可以看到，默认情况下，企业只读域控拥有 复制目录更改权限。而域控拥有复制目录更改所有项权限

![](https://img-blog.csdnimg.cn/20210217125539919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 枚举 ACL配置

Powerview集成的 Invoke-ACLScanner 可以枚举检查活动目录中可能存在风险的ACL配置，Invoke-ACLScanner 会查询每一个对象的ACL配置，如果某个对象的某些权限被分配给SID大于或等于1000的用户（或用户组等其他对象），则该条权限配置将被认为是有风险的（即IdentityReference>=1000）。

```
Import-Module .\PowerView.ps1;Invoke-ACLScanner -ResolveGUIDs
```


![](https://img-blog.csdnimg.cn/20200714140500302.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[SDDL安全描述符定义语言](https://xie1997.blog.csdn.net/article/details/113833478 "SDDL安全描述符定义语言")

参考文章：[域权限上 - windows protocol](https://daiker.gitbook.io/windows-protocol/ldap-pian/11 "域权限上 - windows  protocol")

                  [访问控制列表 - Win32 apps | Microsoft Docs](https://docs.microsoft.com/zh-cn/windows/win32/secauthz/access-control-lists "访问控制列表 - Win32 apps | Microsoft Docs")

                 [渗透技巧——Windows下的Access Control List](https://3gstudent.github.io/3gstudent.github.io/%E6%B8%97%E9%80%8F%E6%8A%80%E5%B7%A7-Windows%E4%B8%8B%E7%9A%84Access-Control-List/ "渗透技巧——Windows下的Access Control List")

                 [域渗透中的ACL访问控制和组策略利用方法](https://www.freebuf.com/news/231037.html "域渗透中的ACL访问控制和组策略利用方法")