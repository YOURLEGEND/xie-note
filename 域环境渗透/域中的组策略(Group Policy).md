**目录**

[组策略](#t0 "组策略")

[组策略的功能](#t1 "组策略的功能")

[组策略对象GPO](#t2 "组策略对象GPO")

[组策略容器GPC](#t3 "组策略容器GPC")

[组策略模板GPT](#t4 "组策略模板GPT")

[策略设置与首选项设置](#t5 "策略设置与首选项设置")

[组策略的应用时机](#t6 "组策略的应用时机")

* * *

组策略
---

      [组策略](https://so.csdn.net/so/search?q=%E7%BB%84%E7%AD%96%E7%95%A5&spm=1001.2101.3001.7020)是Windows环境下用户管理对象的一种手段。组策略分为**本地组策略**和**域环境中的组策略**。本地组策略适合于管理独立的未加入域的工作组的机器。而域环境中的组策略则是用于管理域环境中的对象；本文主要讲解域环境中的组策略。域环境中通过配置组策略可以对域中的用户、用户组、计算机进行不同维度的管理；如安全配置、[注册表](https://so.csdn.net/so/search?q=%E6%B3%A8%E5%86%8C%E8%A1%A8&spm=1001.2101.3001.7020)配置、软件安装配置、开关机与登入登出管理等。配置的组策略通过关联到站点(site)、域(domain)、组织单位(OU)上来在不同层级上应用不同的策略配置。

组策略内包含 **计算机配置** 和 **用户配置** 两部分：

*   计算机配置：当计算机开机时，系统会根据计算机配置的组策略内容来设置计算机的环境。例如在域xie.com配置了组策略，则这个组策略中的计算机设置就会被应用到这个域内的所有计算机
*   用户配置：当用户登录时，系统会根据用户配置的组策略内容来设置用户的工作环境。例如在组织单位技术部配置了组策略，则这个组策略中的用户配置就会被应用到这个组织单位内的所有用户

### 组策略的功能

以下是组策略的主要功能：

*   账户策略的设置：例如设置用户账户的密码长度、密码使用期限、账户锁定策略等
*   本地策略的设置：例如审核策略的设置、用户权限的分配、安全性的设置等
*   脚本的设置：例如登录与注销、启动与关机脚本的设置
*   用户工作环境的设置：例如隐藏用户桌面上所有的图标，删除开始菜单中的运行、搜索、关机等选项、在开始菜单中添加注销选项、删除浏览器的部分选项、强制通过指定的代理服务器上网等
*   软件的安装与删除：用户登录或计算机启动时，自动为用户安装应用软件、自动修复应用软件或自动删除应用软件
*   限制软件的执行：通过各种不同的软件限制策略来限制域用户只能运行指定的软件
*   文件夹的重定向：例如改变文件、开始菜单等文件夹的存储位置
*   限制访问可移动存储设备：例如限制将文件写入U盘、以免企业内机密文件被拷走
*   其它的系统设置：例如让所有的计算机都自动信任指定的CA(Certificate Authority)、限制安装设备驱动程序(device driver)等

组策略对象GPO
--------

组策略是通过**组策略对象(Group Policy Object，简称GPO)**来设置的，而你只需要将GPO链接到指定的站点、域或组织单位，此GPO内的设置值就会影响到该站点、域或组织单位内的所有用户和计算机。组策略对象GPO由 **组策略容器(Group Policy Containers，GPC)** 与 **组策略模板(Group Policy Templet，GPT)** 两部分组成，它们分别被存储在不同的位置：

*   组策略容器(Group Policy Containers，GPC)：GPC存储在活动目录数据库内，它记载着此GPO的属性与版本等数据。域成员计算机可以通过属性来得知GPT的存储位置，而域控制器可利用版本来判断其所拥有的GPO是否为最新版本，以便作为是否需要从其它域控制器复制最新GPO的依据
*   组策略模板(Group Policy Templet，GPT)：GPT是用来存储GPO的设置值与相关文件，它是一个文件夹，而且被建立在域控制器的 %systemroot%\\SYSVOL\\sysvol\\域名\\Policies 文件夹内。系统利用GPO的GUID来当做GPT的文件夹名称。

组策略模板GPT是具体的配置模板，包含实际的配置文件；组策略容器GPC是组策略模板GPT的一种容器，可以将其视作组织管理具体配置的容器。此外，客户端扩展程序（CSEs）是存在于客户端上用于执行下发的组策略的代理程序。

![](https://img-blog.csdnimg.cn/20210205112707104.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**默认GPO** 

当域控建立时，默认会创建两个组策略，分别是：

*   Default Domain Policy：此GPO默认已经被链接到域，因此其设置会被应用到整个域内的所有用户与计算机
*   Default Domain Contoller Policy：此GPO默认已经被链接到组织单位Domain Controllers，因此其设置值会被应用到Domain Controllers内的所有用户与计算机(Domain Controllers内默认只有域控的计算机账户)

![](https://img-blog.csdnimg.cn/20201013093630169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：不要对默认的GPO进行修改

我们可以右键，保存报告，将组策略的内容导出为html文件。

![](https://img-blog.csdnimg.cn/20210218160840990.png)

![](https://img-blog.csdnimg.cn/20210218161519799.png)

### 组策略容器GPC

活动目录以容器的概念来组织和管理组策略，组策略容器存储了每一个组策略详细的基本信息，如：

*   策略名称
*   标识组策略的GUID
*   组策略链接到的层级（即作用的对象）
*   策略模板的具体路径
*   策略应用的筛选过滤等

客户端可以从组策略容器中获取到关于该组策略的所有元信息以及具体配置路径。

组策略容器位于LDAP数据库中的 “CN=Policies, CN=System, DC=xie, DC=com” ，该节点下是以GUID命名的各个组策略对象，如下是默认的两个组策略对象。

可以通过 开始——>管理工具——>Active Directory管理中心 来查看GPC

![](https://img-blog.csdnimg.cn/20210205152130983.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

为了更直观，以下是我通过AD Explorer查看的GPC 

![](https://img-blog.csdnimg.cn/20201013101124523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

组策略容器中，有几个重要的属性配置：

*   displayname：组策略的名称；
*   gPCFileSysPath：组策略模板（GPT）所在的具体路径，即客户端查找具体的配置信息的物理路径，位于域控的SYSVOL共享中；
*   gPCMachineExtensionNames：客户端执行该组策略所需的客户端扩展程序；

![](https://img-blog.csdnimg.cn/20201013101806203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当某个对象应用了某个指定的组策略时，该对象的 gPLink 属性将包含指向该组策略容器的完整DN，gPLink属性值形式如下：

```
# 分号后的0或者1表示该条组策略应用时是否强制，0表示非强制，1表示强制      
[LDAP://CN={GPO_GUID}, CN=Policies, CN=System, DC=xie, DC=com;0/1]
```


如下，Domain Controllers组织单元应用了Default Domain Contoller Policy组策略，所以该组织单元的gPLink属性指向了该组策略对象的DN。

![](https://img-blog.csdnimg.cn/20201013101904138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 组策略模板GPT

组策略模板是组策略具体的策略配置信息，其位于域控的 C:\\Windows\\SYSVOL\\sysvol\\DomainName\\Policies 共享目录下的各个GUID文件夹内。（通过net share可以看到该共享）。

![](https://img-blog.csdnimg.cn/20201013102251318.png)

我们在域内任意一台主机上，以普通域用户身份可以查看该默认sysvol共享

![](https://img-blog.csdnimg.cn/20201013103043711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201013103204972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

GPT在SYSVOL共享中以容器的形式组织目录结构，以GUID标识为目录名的各个组策略配置目录包含以下内容：

*   Macheine目录：包含针对计算机的策略配置；
*   User目录：包含针对用户的策略配置；
*   GPT.ini文件：该组策略对象的一些配置信息（如版本信息、策略名称）；

![](https://img-blog.csdnimg.cn/20201013102723719.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Machine目录和User目录存放着具体的策略配置信息文件。

根据设置的不同组策略配置拥有不同的目录结构，如Scripts目录包含开关机和登入登出的执行脚本、Applications目录包含关于软件的配置、Preferences目录包含首选项配置。

![](https://img-blog.csdnimg.cn/20201013110236257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)(图片来源于网络)

策略设置与首选项设置
----------

组策略内的设置可再分为 **策略设置** 与 **首选项设置** 两种(本地组策略无首选项设置)。

*   策略设置是强制性设置，客户端应用这些设置后就无法更改(有些设置虽然客户端可以自行变更设置值,不过下次应用策略时，仍然会被改为策略内的设置值)；
*   而首选项设置是非强制性的，客户端可以自行更改设置值，因为首选项设置适合用来当做默认值；

若要过滤策略设置的话，必须针对整个GPO来过滤，例如某个GPO已经被应用到技术部，但是我们可以通过过滤设置来让其不要应用到技术部的张三，也就是整个GPO内的所有设置项目都不会被应用到张三，而首选项设置可以针对单一设置项目来过滤。

如果在策略设置与首选项设置内有相同的设置项目，而且都已经做了设置，但是其设置值却不相同的话，则以策略设置优先。

要应用首选项设置的客户端需要安装支持首选项设置的Client-Side Extension(CSE)，而Windows7及其以后的系统默认已包含CSE，其他系统需要手动去微软官网下载。

要应用首选项设置的客户端还需要安装XMLLite。Windows Xp及其以后的机器默认已包含XMLLite。

组策略的应用时机
--------

当修改了站点、域或组织单位的GPO设置值后，这些设置值并不是立刻就对其中的用户和计算机有效，而是必须等GPO设置值被应用到用户或计算机后才有效。GPO设置值内的计算机配置与用户配置的应用时机并不相同。

**计算机配置的应用时机**

计算机会在下面场景中应用GPO的计算机配置值：

1：计算机开机时会自动应用

2：若计算机已经开机，则会每隔一段时间自动应用：

*   域控：默认是每隔5分钟自动应用一次
*   普通域主机：默认是每隔90分钟到120分钟自动应用一次

3：不论策略设置是否发生变化，都会每隔16小时自动应用一次安全设置策略。

4：手动应用：到主机上命令执行框执行：gpupdate /target:computer /force ，该命令只会影响组策略的计算机配置

**用户配置的应用时机**

域用户会在下面场景中应用GPO 的用户配置值：

1：用户登录时会自动应用

2：若用户已经登录的话，则默认会每隔90到120分钟自动应用一次。且不论策略设置值是否发生变化，都会每隔16小时自动应用一次安全设置策略。

3：手动应用：到主机上命令执行框执行：gpupdate /target:user /force ，该命令只会影响组策略的用户配置

注：

*   执行gpupdate /force 会同时应用组策略的计算机和用户的配置
*   部分策略设置需要计算机重新启动或用户登录才有效，例如软件安装策略与文件夹重定向策略

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[域渗透中的ACL访问控制和组策略利用方法](https://www.freebuf.com/news/231037.html "域渗透中的ACL访问控制和组策略利用方法")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10890 人正在系统学习中