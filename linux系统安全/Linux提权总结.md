**目录**

[Linux内核](#t0)

[Linux提权](#t1)

[内核提权](#t2)

[SUID提权](#t3)

[Sudo提权](#t4)

[Linux配置错误提权](#t5)

[定时任务提权](#t6)

[密码复用提权](#t7)

[第三方服务提权](#t8)

* * *

讲Linux[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)之前，我们先看看与Linux有关的一些知识：

我们常说的Linux系统，指的是Linux内核与各种常用软件的[集合](https://so.csdn.net/so/search?q=%E9%9B%86%E5%90%88&spm=1001.2101.3001.7020)产品，全球大约有数百款的Linux系统版本，每个系统版本都有自己的特性和目标人群，大类有以下几种：

![](https://img-blog.csdnimg.cn/20190724225750105.png)

**Linux内核**
-----------

Linux系统内核指的是一个由 Linus Torvalds 负责维护，提供硬件抽象层、硬盘及文件系统控制及多任务功能的系统核心程序。  
   
Linux内核版本有两种：**稳定版** 和 **开发版** ，Linux内核版本号由3组数字组成：第一个组数字.第二组数字.第三组数字

*   第一个组数字：目前发布的内核主版本。
*   第二个组数字：偶数表示稳定版本；奇数表示开发中版本。
*   第三个组数字：错误修补的次数。

如下，使用 uname -r 可以看到linux系统的发行版本号。3.10.0就是内核版本号。3就是内核主版本，10表示是稳定版，0表示错误修补次数是0。 而 123.el7.x86\_64则是 redhat 的发行版本信息，123 代表编译123次，el7代表的是redhat7，x86\_64则是64位系统。

![](https://img-blog.csdnimg.cn/20190724231000270.png)

使用以下命令也可以看到系统内核及版本的一些信息：

```
uname -a                 #查看内核的具体信息      
cat /proc/version        #查看内核的具体信息      
cat /etc/centos-release  #查看centos发行版本      
cat /etc/redhat-release  #查看redhat发行版本
```


![](https://img-blog.csdnimg.cn/20200224161518232.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Linux提权
-------

当我们拿到了一台Linux服务器的低权限账号，于是，我们想要通过技术手段提权至 root 用户权限，以执行更多的操作。

**Linux提权的前提：**

*   拿到了一个低权限的账号
*   能上传和下载文件
*   机器上有python、java、perl等环境（非必须项）

### 内核提权

第一想到的肯定就是Linux的内核漏洞，脏牛提权：[Linux提权—脏牛漏洞(CVE-2016-5195)](https://blog.csdn.net/qq_36119192/article/details/97248374)

还有就是其他的各种内核漏洞提权了。说实话，内核漏洞提权很难成功，因为内核提权对内核的版本，还有目标机器的环境要求很高(需要安装有gcc等编译环境 )。所以使用内核提权成功率很低。并且内核提权需要查看exp的源代码，不然可能连编译都不会。

内核漏洞提权的步骤肯定是查看目标机器的内核版本等信息，查看命令上面有。

或者执行脚本查看该内核可能存在什么漏洞。

```
./Linux_Exploit_Suggester.pl
```


![](https://img-blog.csdnimg.cn/20200225190005613.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### SUID提权

利用Linux的SUID提权：[Linux下用SUID提权](https://blog.csdn.net/qq_36119192/article/details/84872644)

### Sudo提权

Sudo提权漏洞(CVE-2019-14287)：

*   [sudo提权漏洞(CVE-2019-14287)](https://blog.csdn.net/qq_36119192/article/details/104483937)
*   [Linux sudo权限提升漏洞(CVE-2021-3156)](https://xie1997.blog.csdn.net/article/details/113268374)

### Linux配置错误提权

还有就是利用Linux的配置文件错误，导致 /etc/passwd 文件可写入提权：[Linux提权之利用 /etc/passwd 文件](https://blog.csdn.net/qq_36119192/article/details/99871667)

对Linux配置进行检查的脚本有：[https://www.securitysift.com/download/linuxprivchecker.py](https://www.securitysift.com/download/linuxprivchecker.py)

                                                    [http://pentestmonkey.net/tools/audit/unix-privesc-check](http://pentestmonkey.net/tools/audit/unix-privesc-check)

### 定时任务提权

系统内可能会有一些定时执行的任务，一般这些任务由crontab来管理，具有所属用户的权限。非root权限的用户是不可以列出root 用户的计划任务的。但是 /etc/ 内系统的计划任务可以被列出。默认这些程序以root权限执行，如果有幸遇到一个把其中脚本配置成任意用户可写，我们就可以修改脚本进行提权了。

```
ls -l /etc/cron*
```


使用该命令，列出的文件，查看 /etc/cron.daily 、/etc/cron.hourly、/etc/cron.monthly、/etc/cron.weekly 这四个文件夹内的文件，查看是否允许其他用户修改。如果 允许任意用户修改，那么我们就可以往这些文件里面写入反弹shell的脚本提权了。

![](https://img-blog.csdnimg.cn/20200225185211960.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 密码复用提权

我们如果在主机上找到了其他应用或数据库的密码，那么很有可能root用户也用该密码。那么就可以尝试一下 su root 来提权了。

### 第三方服务提权

我们还可以查看主机上其他的第三方服务，利用第三方服务的漏洞可以拿到主机的 root 权限。比如如果主机的mysql或tomcat是用root权限启动的，而我们又通过漏洞拿到了mysql或tomcat的权限，就相当于获得了root的权限。

###  

参考文章：[Linux提权：从入门到放弃](https://www.freebuf.com/articles/system/129549.html)

                  [Linux 提权的各种姿势总结](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247491851&idx=1&sn=067bf2536befb99e9ffab38a51519de2&chksm=ec1dd123db6a58355e9d2ae528468e4d02e74a3bff8340dd0662024edd1fc5bb9cd7e7a3e314&scene=0&xtrack=1&key=731bb20196f2553334f413210a39658f0deb9adece0957c2cc59cdc3bd2487ccbfe3ac9c14d9091c121aeeca069a2340e0d4681f74ce0f552eba912855896ec14d78bc61d7a770849747b9375910e485&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=NzJWdPfBFd8NILfmisxL3bwPds7flbP2N%2BTh8Aien8mQ17OVkOU%2F0hiYhjgFhDBL)

                  [提权总结以及各种利用姿势](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651079239&idx=1&sn=5b153e33af1ed562dff6a79643ba3020&chksm=bd1fd0cc8a6859daf549d51d37824152dc91a0c15f9b0566b73342bec101916256c833ce5b50&scene=0&xtrack=1&key=6e39c5c522d24d96aac620040e3bdbf561177c4887409cc4ea5c25bda2e8c80cdd82a6c61c60642aaf0219d981a1144d4fe2747f38ce6c8857186444a6941dbd48e1eb0e78203fdd96082c44875d6ffb&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62070158&lang=zh_CN&pass_ticket=g3kRTmjolyk7lxmhGmX6Na4%2FUW2vKudQpJNqRVxyy%2F9BNtFjWxCBrKz2SzcF1BMN)

                  [https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-sockets](https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-sockets)

相关文章：[Linux本地提权漏洞复现与检测思路](https://mp.weixin.qq.com/s/lj3NjQlOeLmoXty5bsA_SA)