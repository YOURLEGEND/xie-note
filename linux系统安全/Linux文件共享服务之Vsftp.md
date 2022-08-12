**目录**

[FTP](#t0)

[Vsftp服务的搭建](#t1)

[ftp、sftp、vsftp、vsftpd的区别](#t2)

* * *

### FTP

FTP的工作原理：  
FTP会话时包含了两个通道，一个叫控制通道，一个叫数据通道。控制通道是和[FTP服务器](https://so.csdn.net/so/search?q=FTP%E6%9C%8D%E5%8A%A1%E5%99%A8&spm=1001.2101.3001.7020)进行沟通的通道，连接FTP、发送FTP指定都是通过控制通道来完成的。数据通道是和FTP服务器进行文件传输或者列表的通道。FTP协议中，控制连接都是由客户端发起的，而数据连接有两种工作方式：PORT(主动)方式和PASV(被动)方式 (主动和被动是对于客户端来说的)

*   主动模式：服务器开放 20 和 21 号端口
*   被动模式：服务器开放 21 号端口以及一个随机端口，其中21号端口固定用来做控制链接 

简单概述为：主动模式是传送数据时是 服务器 连接到客户端的端口；而被动模式则是客户端连接到服务器的端口。主动模式需要客户端必须开放端口给服务器，很多客户端都是在防火墙内部，开放端口给FTP服务器比较困难；而被动模式则只需要服务器开放端口给客户端就行了。一般被动模式是用的比较多

FTP用户类型：

*   匿名用户：anonymous 或者 ftp
*   本地用户：服务器本身的用户家目录为共享目录
*   虚拟用户：使用独立账户密码数据文件的用户

常见的FTP服务程序：  
Vsftpd、IIS 、Server-U、wu-ftp、proftp

**Vsftp (Very Security ftp)** ：非常安全的FTP应用，基于FTP服务开发的应用  
主要配置文件：

*   /etc/vsftpd/vsftpd.conf ：主配置文件
*   /etc/vsftpd/ftpusers ：黑名单
*   /etc/vsftpd/user\_list：黑白名单

### **Vsftp服务的搭建**

服务器端 (Rhel7      ip: 192.168.10.17)  
客户端    (Centos 6.5 ip: 192.168.110.24)

1.  服务器端关闭Selinux ：setenforce  0  ； 配置防火墙： firewall-cmd   --add-service=ftp
2.  服务器端安装 vsftpd 程序：yum  -y  install  vsftpd
3.  修改共享目录属主属组：  chown  ftp:ftp   /var/ftp/pub  -R
4.  创建本地登录用户并且设置密码：useradd  -s  /sbin/nologin   test  ;  passswd    test
5.  修改配置文件  /etc/vsftpd/vsftpd.conf ,启动 vsftp 服务 systemctl  start  vsftpd
6.  客户端关闭Selinux和防火墙：setenforce  0  ； service  iptables  stop
7.  客户端安装 lftp 或者 ftp： yum -y  install lftp  或  yum  -y  install  ftp
8.  客户端使用 lftp 或 ftp 登录，共享的是 /var/ftp/pub ，客户端使用本地用户 test 登录,共享的是 test用户的家目录
9.  无论是lftp还是ftp，上传都是 put 文件名  ，下载都是 get 文件名

注：使用lftp登录默认是匿名用户登录不用输密码。使用ftp登录的话，可以使用服务器的本地用户登录，也可以使用账户名为ftp的匿名用户登录

注：vsftpd安装好后，会自动创建文件夹  /var/ftp/pub ,这个是默认共享目录，我们把要共享的东西放到目录中。

配置文件 /etc/[vsftpd](https://so.csdn.net/so/search?q=vsftpd&spm=1001.2101.3001.7020)/vsftpd.conf   的修改

```
anonymous_enable=YES              //默认是允许匿名用户登录，如果改成NO的话，则只允许以ftp服务器本地的用户名的身份登录了       
//将这两行的注释去掉，允许匿名用户上传文件      
anon_upload_enable=YES            //允许匿名用户上传      
anon_mkdir_write_enable=YES       //允许匿名用户写入
```


使用 lftp 用户登录，默认是匿名用户，共享的是 /var/ftp/pub 目录

![](https://img-blog.csdn.net/20180918192452778?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 使用 ftp登录，用的是 ftp 用户，共享的是 /var/ftp/pub 目录

![](https://img-blog.csdn.net/20180918192719216?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当使用服务器端的用户 test 登录时，共享的是 test 用户的家目录

![](https://img-blog.csdn.net/2018091819374152?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### ftp、sftp、ftps、vsftp、vsftpd的区别

*   ftp 是File Transfer Protocol的缩写，文件传输协议，用于在网络上进行文件传输的一套标准协议，使用客户/服务器模式。它属于网络传输协议的应用层。
*   sftp 是SSH File Transfer Protocol的缩写，安全文件传输协议。使用SFTP登录会在last和lastb中留下记录
*   FTPS是在安全套接层使用标准的FTP协议和指令的一种增强型FTP协议，为FTP协议和数据通道增加了SSL安全功能。FTPS也称作“FTP-SSL”和“FTP-over-SSL”。SSL是一个在客户机和具有SSL功能的服务器之间的安全连接中对数据进行加密和解密的协议。
*   vsftp 是一个基于GPL发布的类Unix系统上使用的ftp服务器软件，它的全称是Very Secure FTP从此名称可以看出来，编制者的初衷是代码的安全；
*   vsftpd 是very secure FTP daemon的缩写，安全性是它的一个最大的特点。vsftpd 是一个 UNIX 类操作系统上运行的服务器的名字，它可以运行在诸如 Linux、BSD、Solaris、 HP-UNIX等系统上面，是一个完全免费的、开放源代码的ftp服务器软件；

相关文章：[Linux文件共享服务之Samba](https://blog.csdn.net/qq_36119192/article/details/82733374)

                 [Linux文件共享服务之NFS](https://blog.csdn.net/qq_36119192/article/details/82747256)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中