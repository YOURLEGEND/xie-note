**目录**

[Samba](#t0)

[Samba的配置](#t1)

* * *

### Samba

Samba是一个能让Linux系统应用Microsoft网络通讯协议的软件，而SMB是Server Message Block的缩写，即为服务器消息块 ，SMB主要是作为Microsoft的网络通讯协议，后来[Samba](https://so.csdn.net/so/search?q=Samba&spm=1001.2101.3001.7020)将SMB通信协议应用到了Linux系统上，就形成了现在的Samba软件。而 CIFS（Common Internet File System）为SMB的跨平台版本，即公共 Internet 文件系统，并且加入了许多新的功能，这样一来，使得Samba具有了更强大的功能。

**SMB的版本：**

```
SMB 1.0 (or SMB1) – The version used in Windows 2000, Windows XP, Windows Server 2003 and Windows Server 2003 R2      
SMB 2.0 (or SMB2) – The version used in Windows Vista (SP1 or later) and Windows Server 2008      
SMB 2.1 (or SMB2.1) – The version used in Windows 7 and Windows Server 2008 R2      
SMB 3.0 (or SMB3) – The version used in Windows 8 and Windows Server 2012
```


**samba服务的进化：**

*   smb协议：server message block 服务消息块
*   cifs协议 ：common internet file system 通用互联网文件系统

Samba最大的功能就是可以用于 Linux 与 windows 系统直接的文件共享和打印共享，Samba既可以用于windows与Linux之间的文件共享，也可以用于Linux与Linux之间的资源共享，由于NFS(网络文件系统）可以很好的完成Linux与Linux之间的数据共享，因而 Samba较多的用在了Linux与windows之间的数据共享上面。

SMB是基于客户机/服务器型的协议，因而一台Samba服务器既可以充当文件共享服务器，也可以充当一个Samba的客户端。例如，一台在Linux 下已经架设好的Samba服务器，windows客户端就可以通过SMB协议共享Samba服务器上的资源文件，同时，Samba服务器也可以访问网络中其它windows系统或者Linux系统共享出来的文件。Samba在windows下使用的是NetBIOS协议，如果你要使用Linux下共享出来的文件，请确认你的windows系统下是否安装了NetBIOS协议。

组成Samba运行的有两个服务，一个是SMB，另一个是NMB；SMB是Samba 的核心启动服务，主要负责建立 Linux Samba服务器与Samba客户机之间的对话， 验证用户身份并提供对文件和打印系统的访问，只有SMB服务启动，才能实现文件的共享，监听TCP 139端口；而NMB服务是负责解析用的，类似与DNS实现的功能，NMB可以把Linux系统共享的工作组名称与其IP对应起来，如果NMB服务没有启动，就只能通过IP来访问共享文件，NMB监听UDP的137和138 端口

例如：某台Samba服务器的IP地址为192.1.68.10.10，对应的工作组名称为 xie，那么在Windows的IE浏览器输入下面两条指令都可以访问共享文件。其实这就是Windows下查看Linux Samba服务器共享文件的方法：  
　　\\\\192.168.10.10\\共享名  
　 　\\\\xie\\共享名

smaba服务主要有两个程序

*   smb：提供对服务器中文件的共享访问，监听TCP139端口
*   nmb：提供基于netbios主机名称的解析，监听UDP 137、138端口 

程序目录：  /usr/lib/systemd/system/smb.service (Rhel7)   或    /etc/init.d/smb （Rhl6）

配置文件： /etc/samba/smb.conf

用户访问samba server一共有四种验证方式 ：

1.  share：用户访问Samba Server不需要提供用户名和口令, 安全性能较低。
2.  user：共享目录只能被授权的用户访问,由Samba Server负责检查账号和密码的正确性。账号和密码要在本Samba Server中建立。
3.  server：依靠其他Windows NT/2000或Samba Server来验证用户的账号和密码,是一种代理验证。此种安全模式下,系统管理员可以把所有的Windows用户和口令集中到一个NT系统上,使用Windows NT进行Samba认证, 远程服务器可以自动认证全部用户和口令,如果认证失败,Samba将使用用户级安全模式作为替代的方式。
4.  domain：域安全级别,使用主域控制器(PDC)来完成认证

### Samba的配置

### 服务器端

1：关闭 SElinux 和配置防火墙  
setenforce  0     
firewall-cmd  --add-port=139/tcp ;  firewall-cmd  --add-port=137-138/udp   
2：安装samba程序：   yum  -y  install  samba  
3：修改配置文件 ：   /etc/samba/smb.conf

```
-----------------------------global settings-----------------------------------------      
[global]   //该设置都是与Samba服务整体运行环境有关的选项，它的设置项目是针对所有共享资源的      
workgroup = MYGROUP              //设定 Samba Server 所要加入的工作组或者域。      
server string = Samba Server Version %v       //设定 Samba Server 的注释，可以是任何字符串，也可以不填。宏%v表示显示Samba的版本号。      
log file = /var/log/samba/log.%m     //设置Samba Server日志文件的存储位置以及日志文件名称。在文件名后加个宏%m（主机名），表示对每台访问Samba Server的机器都单独记录一个日志文件。如果pc1、pc2访问过Samba Server，就会在/var/log/samba目录下留下log.pc1和log.pc2两个日志文件          
max log size = 50            //最大日志大小为50kb      
security = user              //用户访问samba的验证方式，一共有四种，当为 share 时，则是允许匿名访问      
passdb backend = tdbsam     //passdb backend就是用户后台的意思。目前有三种后台：smbpasswd、tdbsam和ldapsam;sam是security account manager（安全账户管理）的简写      
load printers = yes         //设置是否在启动Samba时就共享打印机。      1
cups options = raw       1
----------------------------Share Definitions----------------------------------------      1
[homes]                                  //默认的共享，默认共享samba用户创建时创建的home目录，可以删除      1
        comment = Home Directories       //comment是对该共享的描述，可以是任意字符串。      1
        browseable = no                  //browseable用来指定该共享是否可以浏览      1
        writable = yes                   //writable用来指定该共享路径是否可写      1
        valid users = %S                 //允许访问该共享的用户      1
        invalid users                    //禁止访问该共享的用户      1
        valid users = MYDOMAIN\%S       2
[printers]              //默认的共享，共享打印，可以删除      2
        comment = All Printers      2
        path = /var/spool/samba      2
        browseable = no      2
        printable = yes      2
[samba]                 //自己创建的共享，共享名      2
        comment=share      2
        path=/etc                   //共享目录是 /etc      2
        writeable=yes               //是否可写入      3
        browseable=yes              //网上邻居是否可见      3
        guest ok=yes                //是否允许所有人访问      3
        valid users=test            //允许访问该共享的用户是test用户      3
        write list=test             //只有test用户可以写入
```


查看我们的配置： grep   -v   "#"   /etc/samba/smb.conf  |  grep -v ";" |  egrep "=|\]"        使用 testparm 命令可以测试smb.conf配置是否正确  
4：创建samba用户：  
useradd  -M  -s  /sbin/nologin        test            
smbpasswd   -a  test      给smaba用户test设置密码  
5：然后启动服务：  systemctl  start  smb

### 客户端

  
windows端访问：    \\\\192.168.10.10\\共享名        如：\\\\192.168.10.10\\samba

![](https://img-blog.csdn.net/20180919133036909?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  
Linux端访问： 

*      smbclient  -U  用户名   //ip地址/共享名      如： smbclient  -U   test   //192.168.10.10/samba
*      smbclient   -L  192.168.10.10      然后提示输入密码 (以当前用户身份登录)
*      mount  -o  username=xx    //ip地址/共享名      挂载点     将共享文件挂载到本地

![](https://img-blog.csdn.net/20180919132938929?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180919132806266?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180919133841965?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

相关文章：   [Linux文件共享服务之NFS](https://blog.csdn.net/qq_36119192/article/details/82747256)

                     [Linux文件共享服务之Vsftp](https://blog.csdn.net/qq_36119192/article/details/82748360)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中