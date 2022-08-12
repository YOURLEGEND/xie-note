**whoami**：查看当前用户

![](https://img-blog.csdnimg.cn/20190701160337665.png)

**who**：查看当前登录系统的所有用户

*   tty指的是主机的图形化界面的面板
*   pts/x指的是远程ssh连接的窗口

![](https://img-blog.csdnimg.cn/20190712214705910.png)

**who -b**:主机的上一次启动时间

![](https://img-blog.csdnimg.cn/20190712214833357.png)

**w**：显示已经登陆系统的用户列表，并显示用户正在执行的指令。

![](https://img-blog.csdnimg.cn/20190701163835176.png)

**users**：显示当前登录系统的所有用户的用户列表。

![](https://img-blog.csdnimg.cn/20190701164043812.png)

**last**：查看最近登录成功的用户及信息，该命令是读取的是 /var/log/wtmp 文件

第1列是登录成功的用户名，第2列是pts终端，第3列是登录的ip，第4列是时间日期(包括登录时间到退出时间，still logged in代表现在该用户还登录着)

![](https://img-blog.csdnimg.cn/20190701162551309.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

攻击者在侵入目标主机后，一般都会将 /var/log/wtmp 文件删掉，这样last就看不到任何东西了。黑客删除只能将整个文件删除，而不能只是说删除某一条或者某几条记录。

![](https://img-blog.csdnimg.cn/20190712215026251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**lastb**：查看最近登录失败的用户及信息，该命令是读取的是 /var/log/btmp 文件

第1列是登录失败的用户名，第2列的[ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020):notty说明登录失败，第3列是登录的ip，第4列是时间日期

如下，说明有黑客在尝试爆破你的ssh账号密码。黑客如果通过爆破你的SSH账号进入你主机的话，一般会将 /var/log/btmp 文件删掉，这样就看不到登录失败的记录了。黑客删除只能将整个文件删除，而不能只是说删除某一条或者某几条记录。

![](https://img-blog.csdnimg.cn/20190701163349200.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**lastlog**：显示系统中所有用户最近一次登录信息

![](https://img-blog.csdnimg.cn/20190701163938772.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**与系统登录相关的日志**

**/var/log/secure**

除了/var/log/secure以外，还有三个存储着之前的数据。如果后面的数据慢慢多了，就会删除最早的这些。

![](https://img-blog.csdnimg.cn/20190712215839598.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

/var/log/secure 一般用来记录安全相关的信息，记录最多的是哪些用户登录服务器的相关日志，如果该文件很大，说明有人在破解你的 root 密码

这个是  /var/log/secure 里的登录日志 ，第一行说明root用户切换登录xie用户成功。第二三行说明xie用户想登录bob用户然后认证失败了，也就是密码错误。第四行说明xie用户退出登录了。

![](https://img-blog.csdn.net/20180911190332711?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
grep 'Fail' /var/log/secure | awk '{print $11}' | sort |uniq -c | sort -k1 -n -r | head -5     #查看登录失败的那一行，然后打印出第11列(从后数)， 然后排序，然后去除重复，然后按第一列排序 ，然后查看前五个
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中