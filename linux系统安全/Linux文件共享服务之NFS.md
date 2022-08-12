NFS(Network File System) 网络文件系统，是FreeBSD支持的文件系统中的一种，它允许网络中的计算机之间通过TCP/IP网络共享资源。在[NFS](https://so.csdn.net/so/search?q=NFS&spm=1001.2101.3001.7020)的应用中，本地NFS的客户端应用可以透明地读写位于远端NFS服务器上的文件，就像访问本地文件一样。NFS主要用于LInux与Linux之间进行文件系统共享。

简单的来说：它就是是可以透过网络，让不同的主机、不同的操作系统可以共享存储。

NFS 的基本原则是“容许不同的客户端及服务端通过一组RPC分享相同的文件系统”，它是独立于操作系统，容许不同硬件及操作系统的系统共同进行文件的分享。

　NFS在文件传送或信息传送过程中依赖于RPC协议。RPC(Remote Procedure Call)远程过程调用 是能使客户端执行其他系统中程序的一种机制。NFS本身是没有提供信息传输的协议和功能的，但NFS却能让我们通过网络进行资料的分享，这是因为NFS使用了一些其它的传输协议。而这些传输协议用到这个RPC功能的。可以说NFS本身就是使用RPC的一个程序。或者说NFS也是一个RPC SERVER。所以只要用到NFS的地方都要启动RPC服务，不论是NFS SERVER或者NFS CLIENT。这样SERVER和CLIENT才能通过RPC来实现PROGRAM PORT的对应。可以这么理解RPC和NFS的关系：NFS是一个文件系统，而RPC是负责负责信息的传输。  
需要安装的软件包：nfs-utils (NFS服务的主程序)  、 rpcbind (RPC服务的主程序)  
程序目录：/usr/lib/systemd/system/nfs-server.service    、  /usr/sbin/rpcbind   
服务器端nfs配置文件：/etc/exports    

NFS的优势：

*   节省本地存储空间，将常用的数据存放在一台NFS服务器上且可以通过网络访问，那么本地终端将可以减少自身存储空间的使用。
*   用户不需要在网络中的每个机器上都建有Home目录，Home目录可以放在NFS服务器上且可以在网络上被访问使用。
*   一些存储设备CDROM和Zip（一种高储存密度的磁盘驱动器与磁盘）等都可以在网络上被别的机器使用。这可以减少整个网络上可移动介质设备的数量。

**NFS文件共享服务的搭建：**  
服务器端 (Rhel7      ip: 192.168.10.17)  
客户端    (Centos 6.5 ip: 192.168.10.24)

1.  服务器端安装相应的软件包：  yum  -y  install  rpcbind  nfs-utils
2.  服务器端关闭SElinux和防火墙：setenforce  0  ； systemctl  stop  firewalld 
3.  创建共享目录(也可以不创建，直接共享现存的目录)，并且赋予权限：mkdir  /share  ;    chmod   777    /share
4.  修改共享配置文件 /etc/exports ,然后重新加载exports文件：exportfs  -a
5.  启动 rpcbind服务和nfs服务： systemctl  start  rpcbind ; systemctl  start  nfs
6.  客户端关闭Selinux和防火墙：setenforce  0  ； service  iptables  stop
7.  客户端安装rpcbind，并且启动：  yum  -y  install  recbind  ;  service   rpcbind  start 
8.  创建挂载点，查看，并且挂载：mkdir  /data ;  showmount  -e  192.168.10.17;  mount  -t nfs  192.168.10.17:/share  /data

注：rpcbind软件包yum安装必须用本地源安装！！用网络源会报错

1:服务器端安装相应的软件包：  yum  -y  install  rpcbind  nfs-utils

![](https://img-blog.csdn.net/20180917192158783?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2： 服务器端关闭SElinux和防火墙：setenforce  0  ； systemctl  stop  firewalld

![](https://img-blog.csdn.net/20180917192409414?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3： 创建共享目录（也可以不创建，直接共享现存的目录），并且赋予权限：mkdir  /share  ;    chmod   777    /share 

![](https://img-blog.csdn.net/20180917192606350?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

4：修改共享配置文件 /etc/exports ,然后重新加载exports文件：expotfs  -a 

![](https://img-blog.csdn.net/201809171929249?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
/share  192.168.10.0/24(rw,sync,no_root_squash)
```


5：systemctl  start  rpcbind ; systemctl  start  nfs

![](https://img-blog.csdn.net/20180917193054315?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

6：客户端关闭SElinux和防火墙：setenforce  0  ； service  iptables  stop

![](https://img-blog.csdn.net/20180917193253595?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

7：客户端安装rpcbind，并且启动：  yum  -y  install  recbind  ;  service  rpcbind  start

![](https://img-blog.csdn.net/20180917193436461?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

8：创建挂载点，查看，并且挂载：mkdir  /data ;  showmount  -e  192.168.10.17;  mount  -t nfs  192.168.10.17:/share   /data

注：这里我已经在客户端的hosts文件中添加了Redhat解析192.168.10.17

![](https://img-blog.csdn.net/2018091719544770?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

相关文章：  [Linux文件共享服务之Samba](https://mp.csdn.net/postedit/82733374)

                   [Linux文件共享服务之Vsftp](https://blog.csdn.net/qq_36119192/article/details/82748360)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中