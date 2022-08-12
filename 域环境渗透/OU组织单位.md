**目录**

[OU组织单位](#t0 "OU组织单位")

[OU跟容器的区别](#t1 "OU跟容器的区别")

[OU跟组的区别](#t2 "OU跟组的区别")

[OU委派](#t3 "OU委派 ") 

[OU查询](#t4 "OU查询")

[创建组织单元和域用户账号](#t5 "创建组织单元和域用户账号")

* * *

OU组织单位
------

组织单位(Organization Unit,OU)是一个容器对象，将域中的对象组织成逻辑组，帮助网络管理员简化管理组。组织单位包含下列类型的对象：用户，计算机，工作组，打印机，安全策略，其他组织单位等。可以在组织单位基础上部署[组策略](https://so.csdn.net/so/search?q=%E7%BB%84%E7%AD%96%E7%95%A5&spm=1001.2101.3001.7020)，统一管理组织单位中的域对象。 在企业域环境里面，我们经常看到按照部分划分的一个个OU。

![](https://img-blog.csdnimg.cn/20210208214532239.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### OU跟容器的区别

组织单位（OU）是专用容器，与常规容器的区别在于管理员可以将组策略应用于OU，然后系统将其下推到OU中的所有计算机。您不能将组策略应用于容器。需要注意的是CN=`Computers`是一个普通容器，而OU=`Domain Controllers`是一个OU，因此可以可以将组策略应该于`Domain Controllers`，而不可以将组策略应用于`Computers`。

![](https://img-blog.csdnimg.cn/20210208214832931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2021020821491021.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### OU跟组的区别

组织单位跟组是两个完全不同的概念。很多人经常会把这两个弄混。组是权限的集合。OU是管理对象的集合。举个前面举过的例子，运维需要对公司的网络进行管理，需要一些特殊的管理权限，我们就可以设置个组，对组配置权限。然后将运维拉近组里面，组里面的运维就拥有了该权限。比如我们需要对财务部里面的用户进行统一管理，那我们可以设置个OU，然后把财务部的用户拉近这个OU，这样就可以进行集中管理，比如说下发组策略。说通俗点，组是管理的集合，OU是被管理的集合。

### OU委派 

考虑这样一种需求，如果我们想允许某个用户把其他用户拉入OU，而不赋予这个用户域管权限，我们可以在这个OU给这个用户委派 添加成员的权限。组织单位的委派其实就是赋予某个域内用户对OU的某些管理权限。这些权限体现在ACL里面。

右键 OU——>委派控制

![](https://img-blog.csdnimg.cn/20210208215150862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### OU查询

所有的OU都是`organizationalUnit`类的实例，因此我们可以用`(objectClass=organizationalUnit)`或者`(objectCategory=organizationalUnit)`来过滤OU。

```
adfind.exe -s subtree -b dc=xie,dc=com -f "(objectClass=organizationalUnit)" -dn      
或      
adfind.exe -s subtree -b dc=xie,dc=com -f "(objectCategory=organizationalUnit)" -dn
```


![](https://img-blog.csdnimg.cn/20210208215626479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如要要查询OU里面的账户，可以指定BaseDN为OU就行

```
adfind.exe -s subtree -b "OU=技术部,DC=xie,DC=com" -dn
```


![](https://img-blog.csdnimg.cn/20210208220047491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 创建组织单元和域用户账号

开始——>管理工具——>Active Directory用户和计算机，右键域名——>新建——>组织单位

![](https://img-blog.csdnimg.cn/20210127231649148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

填入要创建的组织单位的名字，然后确定即可。

![](https://img-blog.csdnimg.cn/20210127231735461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

往组织单位中添加用户，右键组织单位名——>新建——>用户

![](https://img-blog.csdnimg.cn/20210127231800321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

填入用户的一些信息

![](https://img-blog.csdnimg.cn/20210127231928749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

填入用户的密码

![](https://img-blog.csdnimg.cn/20210127231958897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击完成即可

![](https://img-blog.csdnimg.cn/20210127232014135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到已经在技术部组织单位中新建了用户张三

![](https://img-blog.csdnimg.cn/20210127232034183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[组和OU介绍 - windows protocol](https://daiker.gitbook.io/windows-protocol/ldap-pian/9#1-zu-jie-shao "组和OU介绍 - windows  protocol")