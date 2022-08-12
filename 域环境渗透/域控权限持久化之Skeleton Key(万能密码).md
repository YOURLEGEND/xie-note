**目录**

[mimikatz注入Skeleton Key(万能密码)](#t0 "mimikatz注入Skeleton Key(万能密码)")

[Skeleton Key攻击的防御措施](#t1 "Skeleton Key攻击的防御措施")

* * *

使用Skeleton Key(万能密码)，可以对域内权限进行持久化操作。

mimikatz注入Skeleton Key(万能密码)
----------------------------

在域控上，使用mimikatz注入Skeleton Key，注入成功后。会在域内的所有账号中添加万能密码：mimikatz 。

![](https://img-blog.csdnimg.cn/2020021819583755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

接下来，我们就可以以域内任何用户，密码：mimikatz 登录。

![](https://img-blog.csdnimg.cn/20200218200050690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

登录成功后，我们列出域控的C盘。

![](https://img-blog.csdnimg.cn/20200218200128614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Skeleton Key攻击的防御措施
-------------------

2014年，微软在Windows操作系统中增加了LSA保护策略，以防止lsass.exe进程被恶意注入，从而防止mimikatz在非允许的情况下提升到Debug权限。通用的Skeleton Key的防御措施列举如下：

*   域管理员用户设置强口令，确保恶意代码不会在域控制器中执行
*   在所有域用户中启用双因子认证
*   启动应用程序白名单(Applocker)，以限制mimikatz在域控上运行

在日常网络维护中注意以下方面，也可以有效防范Skeleton Key

*   向域控制器注入Skeleton Key的方法，只能在64位操作系统中使用，包括Windows Server2012R2、Windows Server2012、Windows Server2008、Windows Server2008R2、Windows Server 2003R2、Windows Server 2003
*   只有具有域管理员权限的用户可以将Skeleton Key注入域控制器的lsass.exe进程
*   Skeleton Key被注入后，用户使用现有的密码仍然可以登录系统
*   因为Skeleton Key是被注入lsass.exe进程的，所以它只存在于内存中。如果域控制器重启，注入的Skeleton Key将会失效。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)