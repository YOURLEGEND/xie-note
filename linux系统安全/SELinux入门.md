**目录**

[SELinux](#t0)

[SElinux的前世今生](#t1)

[SELinux的模式](#t2)

[安全属性](#t3)

* * *

SELinux
-------

SELinux(Security-Enhanced Linux)是一种基于 域-类型 模型（domain-type）的强制访问控制（MAC）安全系统，它由NSA编写并设计成内核模块包含到内核中，相应的某些安全相关的应用也被打了SELinux的补丁，最后还有一个相应的安全策略。任何程序对其资源享有完全的控制权。假设某个程序打算把含有潜在重要信息的文件扔到/tmp目录下，那么在DAC情况下没人能阻止他。SELinux提供了比传统的UNIX权限更好的访问控制。

### SElinux的前世今生

SELinux出现之前，Linux上的安全模型叫DAC(Discretionary Access Control 自主访问控制）。DAC的核心思想很简单，就是：进程理论上所拥有的权限与执行它的用户的权限相同。比如，以root用户启动firefox，那么firefox就有root用户的权限，在Linux系统上能干任何事情。

显然，DAC太过宽松了，所以各路高手想方设法都要在Android系统上搞到root权限。那么SELinux如何解决这个问题呢？原来，它在DAC之外，设计了一个新的安全模型，叫MAC（Mandatory Access Control  强制访问控制)。MAC的处世哲学非常简单：即任何进程想在SELinux系统中干任何事情，都必须先在安全策略配置文件中赋予权限。凡是没有出现在安全策略配置文件中的权限，进程就没有该权限。

关于DAC和MAC:

*   Linux系统先做DAC检查。如果没有通过DAC权限检查，则操作直接失败。通过DAC检查之后，再做MAC权限检查。
    
*   SELinux中也有用户的概念，但它和Linux中原有的user 不是同一个概念。比如，Linux中的超级用户 root 在SELinux中可能就是一个没权限，没地位，打打酱油的"路人甲"。当然，这一切都由SELinux安全策略的制定者来决定。
    

### SELinux的模式

*   enforcing：既阻止用户的违规行为，同时又对违规行为作日志记录
*   permissive：不对违规行为作阻止，只记录日志
*   disabled：SELinux不开启

获取当前的工作模式：  getenforce  
修改工作模式：

1.  Selinux的配置文件在  /etc/sysconfig/selinux  ,修改模式后需要重启才能生效。
2.  setenforce  0 / 1    当设置为0时，模式为 permissive；当设置为1 ，模式为 enforcing ； 不需要重启  (只限于 enforcing模式和permissive模式之间转换)
3.  也可以在系统刚启动引导的时候，按字母e进入 kernel 层对SElinux做控制：  selinux=0    开机后的模式就是 disabled enforcing=0       开机后的模式就是 permissive

![](https://img-blog.csdn.net/20180912112844842?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

也可以通过图形化工具管理，yum  install  policycoreutils-gui  安装插件，然后 system-config-selinux 启动图形化界面。

### 安全属性

Linux中有两种东西，一种死的（Inactive），一种活的（Active）。活的东西就是进程，而死的东西就是资源（文件、套接字等）。他们之间就是一种操作和被操作，或者说使用与被使用的关系。进程能发起动作，例如它能打开文件并操作它。而文件只能被进程操作。SElinux Policy语言就是将死的和活的东西都给打上"标签"，通过"标签"将系统内的资源（包括进程）分成不同的角色（比如：用户、客体），进而对整个系统资源(包括进程)进行合理安全的管控。

在SELinux世界里，每种东西都会被赋予一个安全属性，官方说法叫Security Context ，也可以理解为上面所提到的"标签"。通过每个资源的属性，对他们进行归类管理。Security Context（以后用SContext表示）是一个字符串，由四部分组成，被分为两类：进程SContext和资源SContext。根据SELinux规范，完整的SContext字符串为： user : role : type : range    range是可选项，表示安全级别。

*   user : 用户，SELinux中定义了三种用户：超级用户、系统用户、普通用户
*   role : role是角色之意，它是SELinux中一种比较高层次，更方便的权限管理思路，即Role Based Access Control(基于角色的访问控制，简称为RBAC)。 简单点说，一个u可以属于多个role，不同的role具有不同的权限。object\_r 代表的是文件或目录角色   
*   type : 代表该文件或进程所属的Domain。MAC的基础管理思路其实不是针对上面的RBAC，而是所谓的Type Enforcement Accesc Control(简称TEAD，一般用TE表示) ，Type就是Domain。这个Domain有什么权限，都需要通过allow语句来说明。
*   range : SELinux为了满足军用和教育行业而设计的Multi-LevelSecurity（MLS）机制。简单点说，MLS将系统的进程和文件进行了分级，不同级别的资源需要对应级别的进程才能访问。

查看文件或文件夹的SContext：  ls    Z  文件   或    ls  -dZ   文件夹  
查看进程的sContext：ps  -auxZ

![](https://img-blog.csdn.net/2018091214051860?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
修改SContext：我们一般修改的是type字段

*   chcon  -R   -t   public\_content\_t    /home/xie    chcon表示运行状态下的修改，修改完立即生效，restorecon可以将之前的修改还原。在修复模式等特殊环境下，chcon做的修改会被重置还原
*   semanage  fcontext  -a -t  httpd\_sys\_content\_t  '/srv/www(/.\*)?'    semanage表示系统层的修改，修改完不会立即生效，需要通过重启或restorecon触发更新，更改之后的标签类型，restorecon无法还原，只能通过semanage设置新的标签类型
*   restorecon -Rv  /home/xie    表示还原或触发系统默认标签类型       -R表示递归式还原，-v表示显示还原标签的变化信息

布尔值：布尔决定是否在运行时应用某些规则

*   获取布尔值： getsebool  -a   
*   修改布尔值： setsebool   -P   httpd\_enable\_cgi  on/off           -P表示永久生效，不加 -P时，重启后失效

获取端口的标签： semanage  port  -l  
SELinux日志机制：  
如果SElinux的监听服务开启，setroubleshootd（RHEL5/6）/auditd（RHEL7），SElinux相关的日志存放在/var/log/audit/audit.log文件中。如果SElinux的监听服务没有开启，则日志机制会被 rsyslog 代理监听，SElinux相关的日志就会被存放在/var/log/messages文件中