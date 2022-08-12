**目录**

[账户管理、认证授权](#t0 "账户管理、认证授权")

[管理账户](#t1 "管理账户")

[管理口令](#t2 "管理口令")

[授权](#t3 "授权")

[审核策略](#t4 "审核策略")

[IP协议安全配置](#t5 "IP协议安全配置")

[设备其他配置操作](#t6 "设备其他配置操作")

* * *

账户管理、认证授权
=========

管理账户
----

说明：

*   对于管理员账号，要求更改缺省账户名称，并且禁用 guest (来宾) 账号；
*   按照用户分配账户，根据系统要求，设定不同的账户和账户组，管理员用户，数据库用户，审计用户，来宾用户等
*   删除或锁定与设备运行、维护等工作无关的账户

操作：

开始-->管理工具-->本地安全策略-->安全选项，最底下重命名系统管理员账户

开始-->管理工具-->计算机管理-->本地用户和组-->用户，然后要操作哪个用户就右击，然后属性

![](https://img-blog.csdnimg.cn/20181026173519386.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181026083909225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

管理口令
----

说明：

*   设置密码复杂度，比如说最短8个字符，大小字母、小写字母、字符和数字四个里面选3个
*   设置密码最长留存期，比如说3个月
*   账户锁定策略，比如说输错密码多少次锁定账户

操作：开始-->管理工具-->本地安全策略-->账户策略

![](https://img-blog.csdnimg.cn/20181026173724502.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

授权
--

说明：

*   在本地安全设置中从远端系统强制关机只指派给Administrators组
*   在本地安全设置中取得文件或其他对象的所有权只指派给 Administrators组
*   在本地安全设置中配置只有指定授权用户允许本地登录此计算机
*   在组策略中只允许授权账号从网络访问(包括网络共享等，但不包括终端服务)此计算机

操作：开始-->管理工具-->本地安全策略-->本地策略-->用户权限分配

![](https://img-blog.csdnimg.cn/2018102609011133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

审核策略
====

说明：

1.  设置应配置日志功能，对用户登录进行记录，记录内容包括用户登录使用的账户，登录是否成功，登录时间，以及远程登录时所使用的ip地址
2.  启用组策略中对windows系统的审核策略更改，成功和失败都要审核
3.  启用组策略中对windows系统的审核对象访问，成功和失败都要审核
4.  启用组策略中对windwos系统的审核目录访问，成功和失败都要审核
5.  操作系统审核特权使用策略安全基线要求项
6.  操作系统审核系统事件策略安全基线要求项
7.  操作系统审核账户管理策略安全基线要求项
8.  操作系统审核过程追踪策略安全基线要求项
9.  操作系统日志容量安全基线要求项

操作：开始-->管理工具-->本地安全策略-->本地策略-->审核策略

![](https://img-blog.csdnimg.cn/20181026174119608.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

IP协议安全配置
========

说明：

启动SYN攻击保护  
指定触发SYN洪水攻击保护所必须超过的TCP连接请求数阀值为5  
指定处于SYN\_RCVD状态的TCP连接数的阀值为500  
指定处于至少已发送一次重传的SYN\_RCVD状态中的TCP连接数的阀值为400

操作：开始-->运行-->regedit 查看注册表

设备其他配置操作
========

说明：

*   在非域环境中，关闭默认共享
*   查看每个共享文件夹的共享权限，只允许授权的账户拥有权限共享此文件夹
*   列出所需要服务的列表(包括所需的系统服务)，不在此列表中的服务需关闭
*   列出系统启动时自动加载的进程和服务列表，不在此列表的需关闭
*   关闭远桌面，如需开启，修改端口
*   关闭Windows自动播放功能
*   对于远程登录的账号，设置不活动断连时间15分钟

相关文章：[Windows服务器主机加固分享](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651078089&idx=4&sn=bdf8aba21d41d6d0b03cd3d0df98c3ec&chksm=bd1faf428a682654658371fa586e4cd6f6428079d2fbf3231b63e1727694258727da683bce8d&scene=0&xtrack=1&key=95522d0f0e67d29cf758e6bea54954f4a2d9d024ffd27e2820398bc8a256417a9130f4d57981446a89d682acbc7dc661f7dd21de5ba933f02c6656714b5ea2fcb253bdc7af9514c60ee2406585faea26&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=irTZVyrE8xZpBUIJ5x3YZIVIIl%2Bc7G9LNBLHZyp7zJHangzzaA61Hwqw2925WxMV "Windows服务器主机加固分享")

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)