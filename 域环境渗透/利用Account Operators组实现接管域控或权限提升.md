**目录**

[利用基于资源的约束性委派进行权限提升](#t0 "利用基于资源的约束性委派进行权限提升")

[Write Dcsync Acl dump域内哈希](#t1 "Write Dcsync Acl dump域内哈希")

* * *

在域渗透的过程中，我们往往只会关注Domain admins组和Enterprise Admins组，而会忽略了其它组。今天，我们要讲的是Account Operators组。该组是内置的本地域组。该组的成员可以创建和管理该域中的用户和组并为其设置权限，也可以在本地登录[域控制器](https://so.csdn.net/so/search?q=%E5%9F%9F%E6%8E%A7%E5%88%B6%E5%99%A8&spm=1001.2101.3001.7020)。但是，不能更改属于Administrators或Domain Admins组的账号，也不能更改这些组。在默认情况下，该组中没有成员。也就是说，该组默认是域内管理用户和组的特殊权限组。

![](https://img-blog.csdnimg.cn/20210429181858398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在实际环境中，某些企业会有专门的管理用户账号的账号，这些账号就会分配在Account Operators组中。在渗透过程中，如果我们发现已经获得权限的用户在该组中的话，我们可以利用其特殊权限进行dump域内哈希或本地权限提升。

### 利用基于资源的约束性委派进行权限提升

如果域内没有安装Exchange服务器的话，我们可以利用基于资源的约束性委派攻击除域控外的域内其他所有机器，获取这些机器的本地最高权限。

查看Account Operators组内用户，发现hack用户在内。

![](https://img-blog.csdnimg.cn/20210430125627162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们获取到了某台机器的权限，当前登录用户为hack，但是hack并不在本地机器的管理员组中。

![](https://img-blog.csdnimg.cn/20210429183149433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是我们可以新建机器账号，然后配置机器账号到指定主机的基于资源的约束性委派

![](https://img-blog.csdnimg.cn/20210429183304940.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210429183239658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

最后利用新建的机器账号模拟administrator用户访问指定主机的cifs协议生成票据，导入票据后，就获取了目标机器的最高权限。

```
python3 getST.py -dc-ip 10.211.55.4 xie.com/test:root -spn cifs/win7.xie.com -impersonate administrator      
export KRB5CCNAME=administrator.ccache      
python3 psexec.py -k -no-pass win7.xie.com
```


![](https://img-blog.csdnimg.cn/20210429183654662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Write Dcsync Acl dump域内哈希

如果域内安装了Exchange服务器的话，我们可以将指定用户添加到Exchange Trusted Subsystem组中，由于Exchange Trusted Subsystem用户组又隶属于Exchange Windows Permissions。Exchange Windows Permissions这个组默认对域有WriteACL权限。因此我们可以尝试使用WriteACL赋予指定用户Dcsync的权限。

查看Account Operators组内用户，发现hack用户在内。

![](https://img-blog.csdnimg.cn/20210430125638982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们获取到了某台机器的权限，当前登录用户为hack，但是hack并不在本地机器的管理员组中。

![](https://img-blog.csdnimg.cn/20210429183149433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将hack用户自身加入到Exchange Trusted Subsystem组中

```
net group "Exchange Trusted Subsystem" hack /add /domain
```


![](https://img-blog.csdnimg.cn/20210429185105422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注意，这里需要将hack用户先在当前机器注销一下，重新登录。或者登录其他机器。

然后赋予hack用户自身dcsync权限

![](https://img-blog.csdnimg.cn/20210429191738296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用hack用户dump域内任意用户哈希，即可接管整个域。

```
python3 secretsdump.py xie.com/hack:P@ss123@10.211.55.4 -dc-ip 10.211.55.4 -just-dc-user administrator
```


![](https://img-blog.csdnimg.cn/20210429191914276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)