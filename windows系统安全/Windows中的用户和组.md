**目录**

[用户帐户](#t0 "用户帐户")

[Windows 默认账户](#t1 "Windows 默认账户")

[Windows 内置用户账户](#t2 "Windows 内置用户账户")

[查看、创建和删除账户](#t3 "查看、创建和删除账户")

[组账户](#t4 "组账户")

[内置组账户](#t5 "内置组账户")

[组的查看、创建和删除](#t6 "组的查看、创建和删除")

* * *

用户帐户
----

用户帐户是对计算机用户身份的标识，本地用户帐户、密码存在本地计算机上，只对本机有效，存储在本地安全帐户数据库 SAM 中，文件路径：C:\\Windows\\System32\\config\\SAM ，对应的进程：lsass.exe  。通过本地用户和组，可以为用户和组分配权利和权限，从而限制用户和组执行某些操作的能力。

*   不同的用户身份拥有不同的权限
*   每个用户包含一个名称和一个密码
*   用户帐户拥有唯一的安全标识符(Security Identifier，SID)

![](https://img-blog.csdn.net/20181015112903831?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当我们去进程管理里面杀死 lsass.exe 进程时，windows会提示遇到错误，然后关机。

![](https://img-blog.csdnimg.cn/20181129160357670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看用户的SID**

*   whoami  /user     查看系统当前用户的SID
*   wmic  useraccount  get  name,sid      查看所有用户的SID

![](https://img-blog.csdn.net/20181018151145134?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Windows 默认账户

用于特殊用途，一般不需更修改其权限

*   与使用者关联的用户帐户 Administrator（管理员用户）
*   默认的管理员用户 Guest（来宾用户） 默认是禁用的

![](https://img-blog.csdn.net/20181015153204439?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Windows 内置用户账户

**权限：**System >  Administrator > User > Guest

与windows组件关联的用户账户

*   System (本地系统)：为windows的核心组件访问文件等资源提供权限
*   Local Service (本地服务)：预设的拥有最小权限的本地账户
*   Network Service (网络服务)：具有运行网络服务权限的计算机账户

![](https://img-blog.csdn.net/20181015122202502?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 查看、创建和删除账户

使用命令查看、创建和删除账户

*   net  user　　   查看系统账户
*   net  user   xie      123   /add        创建新用户xie，密码设置为 123
*   net  user   hack$  123  /add        创建隐藏用户hack，密码为123
*   net  user  xie        查看账户xie的属性
*   net  user  xie  x456.    将用户写的密码修改为x456.
*   net  user  xie  /del    删除用户xie
*   net  localgroup   administrators  xie    /add      将普通用户xie提权到管理员
*   net  localgroup   administrators  hack$  /add   将隐藏用户hack提权到管理员

![](https://img-blog.csdn.net/20181015153601898?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

组账户
---

*   组是一些用户的集合
*   组内的用户自动具备为组所设置的权限

![](https://img-blog.csdn.net/20181015113018909?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 内置组账户

**需要人为添加成员的内置组**

*   Administrators
*   Guests
*   Power Users
*   Users（标准用户）
*   Remote Desktop Users

administators 组内的用户，都具备系统管理员的权限，它们拥有对这台计算机最大的控制权限，可以执行整台计算机的管理任务。内置的系统管理员账号 Administrator 就是本地组的成员，而且无法将它从该组删除。如果这台计算机已加入域，则域的Domain Admins 会自动地加入到该计算机的Administrators组内。也就是说，域上的系统管理员在这台计算机上也具备着系统管理员的权限

Guests 组是提供给没有用户帐户但是需要访问本地计算机内资源的用户使用，该组的成员无法永久地改变其桌面的工作环境。该组最常见的默认成员为用户帐号Guest。

Power Users 组内的用户具备比Users组更多的权利，但是比Administrators组拥有的权利更少一些，例如，可以：创建、删除、更改本地用户帐户；创建、删除、管理本地计算机内的共享文件夹与共享打印机；自定义系统设置，例如更改计算机时间、关闭计算机等。但是不可以更改Administrators，无法夺取文件的所有权、无法备份与还原文件、无法安装删除与删除设备驱动程序、无法管理安全与审核日志。

Users 组内的成员只拥有一些基本的权利，例如运行应用程序，但是他们不能修改操作系统的设置、不能更改其它用户的数据、不能关闭服务器级的计算机。所有添加的本地用户帐户者自动属于Users组。如果这台计算机已经加入域，则域的Domain Users会自动地被加入到该计算机的Users组中。

Remote Desktop Users 组内的成员拥有远程桌面登录的权限。默认Administrators组内的成员都拥有远程桌面的权限。

**动态包含成员的内置组**

其成员由Windows程序“自动添加” ，Windows会根据用户的状态来决定用户所属的组 ，组内的成员也随之动态变化，无法修改

*   Interactive： 动态包含在本地登录的用户
*   Authenticated Users：任何一个利用有效的用户帐户连接的用户都属于这个组。建议在设置权限时，尽量针对Authenticated Users组进行设置，而不要针对Everone进行设置。
*   Everyone ：任何一个用户都属于这个组。注意，如果Guest帐号被启用时，则给Everone这个组指派权限时必须小心，因为当一个没有帐户的用户连接计算机时，他被允许自动利用Guest帐户连接，但是因为Guest也是属于Everone组，所以他将具备Everyone所拥有的权限。

### 组的查看、创建和删除

使用命令查看、创建和删除组

*   net  localgroup    查看系统的组
*   net  localgroup   marketGroup   /add         新建一个marketGroup的组
*   net  localgroup   marketGroup   xie  /add   将用户xie加入marketGroup组中
*   net  localgroup   markGroup     查看markGroup组内的成员
*   net  localgroup   marketGroup   xie  /del    将用户xi从marketGroup组中移除
*   net  localgroup   marketGroup   /del           删除marketGroup组
*   net  localgroup  "remote desktop users" hack /add     将用户hack加入远程桌面组
*   net  localgroup  "remote desktop users" hack /del      将用户hack从远程桌面组删除

![](https://img-blog.csdn.net/20181015154643269?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[Windows中的认证体系](https://blog.csdn.net/qq_36119192/article/details/85941222 "Windows中的认证体系")