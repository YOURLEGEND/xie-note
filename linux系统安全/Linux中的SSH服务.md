**目录**

[SSH](#t0)

[使用scp在两台Linux间传数据：](#t1)  

[基于SSH做远程访问，可以使用ftp服务的相关指令sftp root@192.168.10.10](#t2)

[Openssh公私钥验证：](#t3)

* * *

### SSH

SSH(Secure Shell)安全外壳协议，[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)是用来替代常见的不安全的网络应用的协议。比如Telnet、FTP，他们都是基于明文传输的协议，所以很不安全。Openssh 提供基于用户的身份验证，而且能够通过端口隧道转发不安全协议，在隧道里面，数据都是被加密的。  
SSH验证：

1.  password (sent  securely)
2.  RSA and DSA keys (公私钥验证)
3.  Kerberos
4.  s/key  and SecureID(OTP)

相关联的RPM包：openssh(底层包)  、 openssh-clients 、 openssh-server  
服务器端安装： yum  install  openssh-server   (安装了[openssh](https://so.csdn.net/so/search?q=openssh&spm=1001.2101.3001.7020)\-server会自动安装openssh)    
客户端安装： yum  install  openssh-clients      (安装了openssh-clients 会自动安装openssh)   
卸载： yum  remove  openssh （卸载了openssh，然后会自动卸载openssh-server和openssh-clients）  
服务：/usr/sbin/sshd  
服务端口：tcp / 22  
服务器端配置文件：/etc/ssh/sshd\_config  
客户端配置文件：  /etc/ssh/ssh\_config   
用法：ssh -p  22  root@192.168.10.10  ,如果配置文件中 /etc/ssh/ssh\_config 默认是22号端口，则可以直接   ssh  root@192.168.10.10  
服务开启/关闭/重启/开机自启/开启不自启： systemctl  start / stop / restart / enable / disable  sshd

ssh服务端的配置文件(部分):   /etc/ssh/sshd\_config

```
#Port 22                     //默认的端口号为22  修改端口时，取消注释      
#LoginGraceTime 2m           //ssh登录时，密码验证的超时时间，默认2分钟      
#PermitRootLogin yes         //是否允许以root用户身份登录      
#MaxAuthTries 6              //最大认证次数      
#MaxSessions 10              //登录Sessions保持的天数      
#PermitEmptyPasswords no     //是否允许空密码      
PasswordAuthentication yes   //开启密码认证
```


在安装了SSH的机器上，会有ssh和sshd两个程序，分别是 /usr/sbin/sshd 和 /usr/bin/ssh 。

![](https://img-blog.csdnimg.cn/20190714131129262.png)

sshd是服务端的程序，ssh是客户端的程序。我们现在用Xshell连接主机，此时主机上运行的是sshd程序。

![](https://img-blog.csdnimg.cn/2019071413141419.png)

我们现在用该主机连接其他的主机，此时我们上面还会运行ssh程序

![](https://img-blog.csdnimg.cn/20190714131949915.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190714131912210.png)

在 /etc/ssh/ 下面有 ssh\_config 和 sshd\_config ，分别是客户端的配置文件和服务端的配置文件。

![](https://img-blog.csdnimg.cn/20190714132030348.png)

### **使用scp在两台Linux间传数据：**  

  
本机传数据给目的主机

*   scp   /etc/fstab    root@192.168.10.10:/tmp     将本机 /etc/fstab 文件传送到目的主机的 /tmp目录下
*   scp  -r  /etc/ssh/  root@192.168.10.10:/tmp     将本机 /etc/ssh/ 文件夹传送到目的主机的 /tmp 目录下

将目的主机的数据下载到本地

*   scp root@192.168.10.5:/etc/fstab  /tmp              将目的主机的 /etc/fstab 文件下载到本机的 /tmp 目录下
*   scp -r  root@192.168.10.5:/etc/ssh/  /tmp           将目的主机的 /etc/ssh/ 文件夹下载到本机的 /tmp 目录下

### **基于SSH做远程访问，可以使用ftp服务的相关指令**

*   sftp root@192.168.10.10
*   get  /etc/fstab   将远端主机的 /etc/fstab下载到本机
*   put  /etc/fstab   将本机的 /etc/fstab 上传到远端主机

![](https://img-blog.csdnimg.cn/20190725164612145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：通过 sftp 成功登录，在对端主机的/var/log/wtmp是没有日志信息的。但是如果sftp登录失败，在对端主机的/var/log/btmp是有日志信息的。

​​​​查看本机有多少终端登录： who  和   w  
通过 who 显示远端10.0.0.1的地址通过ssh登录本机，然后本机开了两个tty终端  
然后再通过 w 可以查看他是几点登录的，当前正在运行的命令是top  
然后在通过 ps aux | grep pts 可以看到pts进程以及子进程

![](https://img-blog.csdn.net/20180915101338979?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **Openssh公私钥验证：**

1.  在主机A上，使用 ssh-keygen，然后输入保存的文件名(默认为id\_rsa)和私钥的密码，完成之后会在 ~/.ssh/ 生成的两个密钥: id\_rsa(私钥) 和 id\_rsa.pub(公钥)
2.  私钥（id\_rsa）保存在本地主机，公钥（id\_rsa.pub）通过 ssh-copy-id root@192.168.1.200  会自动传递到对端B主机的~/.ssh/目录下,并且自动重命名为 authorized\_keys
3.  登录目标主机:  ssh  root@192.168.1.200    使用刚刚输入的私钥密码登录

使用 ssh-keygen ，然后回车(默认是生成文件名为 id\_rsa ，也可以自己手动输入修改)，然后再输入[私钥](https://so.csdn.net/so/search?q=%E7%A7%81%E9%92%A5&spm=1001.2101.3001.7020)密码

![](https://img-blog.csdn.net/20180915104536831?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，在 /root/.ssh/  目录下已经生成了 id\_rsa 私钥和 id\_rsa.pub 公钥了

![](https://img-blog.csdn.net/20180915104653711?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

进入/root/.ssh/目录，使用  ssh-copy-id root@192.168.1.200 命令，它会将 id\_rsa.pub 公钥传到目标主机 ~/.ssh/ 目录下，并且会自动重命名为 authorized\_keys

![](https://img-blog.csdn.net/20180915105004352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，目标主机已经收到 id\_rsa.pub公钥了，并且已经重命名为  authorized\_keys

![](https://img-blog.csdn.net/20180915105103995?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后在主机上 ssh root@192.168.1.200  然后输入我们自己的私钥密码就可以登录了。

![](https://img-blog.csdn.net/20180915111250401?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果要在机器上管理多个私钥，可以在.ssh目录下创建 config文件，内容如下

```
Host github.com      
IdentityFile C:\Users\用户名\.ssh\id_rsa.github      
User git       
Host 192.168.10.10      
IdentityFile C:\Users\用户名\.ssh\id_rsa.vps      
User git
```


###  

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中