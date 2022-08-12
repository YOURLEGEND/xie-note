**目录**

[WebShell提权](#t0)

[数据库提权](#t1)

[MySQL](#t2)

[SQLServer](#t3)

[普通用户——>管理员用户](#t4)

* * *

在Windows系统中，权限大概分为四中，分别是：

*   User：普通用户权限
*   Administrator：管理员权限，可以利用Windows机制提升为System权限
*   System：系统权限
*   TrustedInstaller：Windows中最高权限。有些文件，连System权限也无法修改，只能TrustedInstaller权限修改。

我们获得了一个Windows系统的普通[用户权限](https://so.csdn.net/so/search?q=%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90&spm=1001.2101.3001.7020)，由于低权限用户的很多操作将受到限制。所以，我们会想法设法地将低权限用户提权到高权限用户。例如，从  **Webshell(apache)、数据库权限——>系统普通用户权限——>Administrator权限——>System权限**。反正，我们的最终目的就是拿到该主机的最高权限。

WebShell提权
----------

比如说我们获得了某个网站的WebShell(小马或大马)，该网站是以普通用户启动的，通过WebShell我们可以执行一些普通的系统命令，但是执行不了创建用户等高权限的系统命令。所以我们现在需要利用获得的WebShell[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)，以达到创建用户等高权限命令。[Windows PR提权](https://blog.csdn.net/qq_36119192/article/details/84562454)

数据库提权
-----

比如说我们发现了某个网站存在SQL注入，亦或者是我们发现了某个主机的数据库的账号密码，我们直接远程连接上了该数据库。我们现在想利用该数据库来执行系统命令。这里需要注意的是，执行系统命令的权限取决于数据库启动用户的权限。

### MySQL

*   **UDF提权：**[MySQL UDF提权执行系统命令](https://blog.csdn.net/qq_36119192/article/details/84863268) ，利用MySQL的自定义函数功能，创建一个自定义的执行系统命令的函数，通过调用该函数来执行系统命令
*   **启动项提权：**传送门——> [MySQL启动项提权](https://www.cnblogs.com/wh4am1/p/6613759.html)，利用MySQL，将后门写入开机启动项。同时因为是开机自启动，再写入之后，需要重启目标服务器，才可以运行，不常用。**从系统普通用户权限——>系统管理员权限。**
*   **mof提权：**传送门——>[mysql之mof提权详解](https://www.cnblogs.com/h4ck0ne/p/5154629.html)，只适用于windows系统，一般低版本系统才可以用，比如xp，server2003，比较麻烦，不常用。**从系统普通用户权限——>系统管理员权限。**

### SQLServer

*   **xp\_cmdshell提权：**[SA权限开启xp\_cmdshell获取主机权限](https://blog.csdn.net/qq_36119192/article/details/88679754#SA%E6%9D%83%E9%99%90%E8%8E%B7%E5%8F%96%E4%B8%BB%E6%9C%BA%E6%9D%83%E9%99%90)，利用SQLServer中的xp\_cmdshell组件执行系统命令
*   **sp\_oacreate提权：**[SA权限使用sp\_oacreate执行系统命令](https://blog.csdn.net/qq_36119192/article/details/88679754#SA%E4%BD%BF%E7%94%A8sp_oacreate%E6%89%A7%E8%A1%8C%E7%B3%BB%E7%BB%9F%E5%91%BD%E4%BB%A4)，利用SQLServer中的sp\_oacreate组件执行系统命令，该组件执行系统命令时无回显
*   **Log备份提权：**[DB\_owner权限LOG备份Getshell](https://blog.csdn.net/qq_36119192/article/details/88679754#DB_owner%E6%9D%83%E9%99%90LOG%E5%A4%87%E4%BB%BDGetshell)
*   **差异备份提权：**[DB\_owner权限差异备份Getshell](https://blog.csdn.net/qq_36119192/article/details/88679754#DB_owner%E6%9D%83%E9%99%90%E5%B7%AE%E5%BC%82%E5%A4%87%E4%BB%BDGetshell)

普通用户——>管理员用户
------------

1.  内核溢出漏洞提权：[Windows内核溢出漏洞提权](https://blog.csdn.net/qq_36119192/article/details/104280692)
2.  错误的系统配置提权：[Windows错误配置提权](https://blog.csdn.net/qq_36119192/article/details/104282669)
3.  组策略首选项提权：[Windows组策略首选项提权](https://blog.csdn.net/qq_36119192/article/details/104344105)
4.  令牌窃取提权：[内网渗透之AccessToken窃取与利用](https://xie1997.blog.csdn.net/article/details/103965659)
5.  绕过UAC提权：[绕过UAC提权](https://blog.csdn.net/qq_36119192/article/details/104292591)

相关文章：[windows提权总结](https://lengjibo.github.io/windows%E6%8F%90%E6%9D%83%E6%80%BB%E7%BB%93/)

                  [提权总结以及各种利用姿势](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651079239&idx=1&sn=5b153e33af1ed562dff6a79643ba3020&chksm=bd1fd0cc8a6859daf549d51d37824152dc91a0c15f9b0566b73342bec101916256c833ce5b50&scene=0&xtrack=1&key=6e39c5c522d24d96aac620040e3bdbf561177c4887409cc4ea5c25bda2e8c80cdd82a6c61c60642aaf0219d981a1144d4fe2747f38ce6c8857186444a6941dbd48e1eb0e78203fdd96082c44875d6ffb&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62070158&lang=zh_CN&pass_ticket=g3kRTmjolyk7lxmhGmX6Na4%2FUW2vKudQpJNqRVxyy%2F9BNtFjWxCBrKz2SzcF1BMN)