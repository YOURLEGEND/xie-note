**目录**

[sudo](#t0)

[sudo -i](#t1)

[sudo的配置文件/etc/sudoers](#t2)

[su](#t3)

[su -](#t4)

* * *

sudo
----

**sudo** ：暂时切换到超级用户模式以执行超级[用户权限](https://so.csdn.net/so/search?q=%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90&spm=1001.2101.3001.7020)，提示输入密码时该密码为当前用户的密码，而不是超级账户的密码。缺点是每次执行超级用户权限都要在命令前加上 sudo ，优点是在当前终端再使用 sudo 不要再重复输入密码(只对于当前终端有效)。

![](https://img-blog.csdnimg.cn/20190818230051628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

sudo -i
-------

**sudo -i**：为了频繁的执行某些只有超级用户才能执行的权限，而不用每次输入密码，可以使用该命令。提示输入密码时该密码为当前账户的密码。没有时间限制。执行该命令后提示符变为“#”而不是“$”。想退回普通账户时可以执行“exit”或“logout” 。

![](https://img-blog.csdnimg.cn/20190818230855568.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

sudo的配置文件/etc/sudoers
---------------------

**注意：但是只有指定的一些用户才有执行 sudo 的权限，具体配置在 /etc/sudoers 文件中。**

![](https://img-blog.csdnimg.cn/20200224184449323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

由于hack用户不在 /etc/sudoers 文件中，故 hack 用户不能使用 sudo 命令。

![](https://img-blog.csdnimg.cn/20200224184847869.png)

那么，如何能让hack用户也能使用sudo命令呢？

**有两个方法：**

*   第一是将该用户加入 wheel 组中 (因为/etc/sudoers 中默认加入了wheel组中的用户可以使用sudo权限)，
*   第二是在 /etc/sudoers 文件中加入  hack   ALL=(ALL)    ALL

```
groupmems -a hack -g wheel   #将用户hack添加到whell组中
```


或在 /etc/sudoers 中加入下面这行

```
hack    ALL=(ALL)       ALL
```


![](https://img-blog.csdnimg.cn/2020022419454799.png)

![](https://img-blog.csdnimg.cn/20200224194123242.png)

![](https://img-blog.csdnimg.cn/20200224194301592.png)

**su**
------

**su** ： 切换到某某用户模式，提示输入密码时的密码为切换后账户的密码，用法为“su  账户名称”。如果后面不加账户时系统默认为 root 账户，密码也为超级账户的密码。没有时间限制。

![](https://img-blog.csdnimg.cn/20190818230651474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

su -
----

**su -** ：su - 和 su 的不同之处在于 su - 切换用户后的目录为切换后目录的家目录，而 su 则是原目录

![](https://img-blog.csdnimg.cn/20190818231950123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中