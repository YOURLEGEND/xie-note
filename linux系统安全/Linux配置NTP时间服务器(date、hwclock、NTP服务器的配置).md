**目录**

[date命令](#t0)

[hwclock命令](#t1)

[NTP服务的部署](#t2)

[服务端](#t3)

[客户端](#t4)

* * *

date命令
------

**date** 命令的作用是查看和设置Linux中的系统日期时间

*   date                                              显示当前系统的时间
*   date  +%参数                                 以指定格式控制输出日期和时间
*   date  -s  "18:58:48 2020-10-10 "     将当前系统时间设置为指定的日期 

![](https://img-blog.csdn.net/20180920081837721?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

hwclock命令
---------

**hwclock**命令的作用是查看Linux中的硬件时间，也就是主板上[BIOS](https://so.csdn.net/so/search?q=BIOS&spm=1001.2101.3001.7020)的时间，由主板电池供电来维持运行，系统开机时要读取这个时间，并根据它来设定系统时间

*   hwclock              查看硬件时间
*   hwclock  -s         将当前的硬件时间设置为系统时间
*   hwclock  -w        将当前的系统时间设置为硬件时间

![](https://img-blog.csdn.net/20180920083714662?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

NTP服务的部署
--------

**NTP(Network Time Protocol)** 网络时间协议，是用来使计算机[时间同步](https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E5%90%8C%E6%AD%A5&spm=1001.2101.3001.7020)化的一种协议，它可以使计算机对其服务器或时钟源（如石英钟，GPS等等)做同步化，它可以提供高精准度的时间校正（LAN上与标准间差小于1毫秒，WAN上几十毫秒），且可介由加密确认的方式来防止恶毒的协议攻击。时间按NTP服务器的等级传播。按照离外部UTC源的远近把所有服务器归入不同的Stratum（层）中。

安装包： ntp  
配置文件：/etc/ntp.conf

使用时间服务器来同步时间：  ntpdate  0.centos.pool.ntp.org  （可以使用/etc/ntp.conf里面指定的时间服务器也可以使用自己的时间服务器）   

![](https://img-blog.csdn.net/20180930155024375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **服务端**

  
安装ntp服务：  yum  -y  install  ntp   
修改配置文件： /etc/ntp.conf

```
//将这行给注释      
#restrict default nomodify notrap nopeer noquery             
//然后添加这两行 ，意思是 允许IPV4 IPV6 查询 拒绝修改      
restrict -4 default kod notrap nomodify          
restrict -6 default kod notrap nomodify       
//添加修改上层时间服务器，如果能联通外网，则不需要修改，      1
server 0.rhel.pool.ntp.org iburst      1
server 1.rhel.pool.ntp.org iburst      1
server 2.rhel.pool.ntp.org iburst      1
server 3.rhel.pool.ntp.org iburst      1
//如果联不通外网，则把上面几行注释，然后添加下面两行，意思是设置成时间服务器是自己      1
server 127.127.1.0      1
fudge 127.127.1.0 stratum 8
```


开启ntp服务：  systemctl  start  ntpd

### **客户端**

安装ntp服务：  yum  -y install ntp

修改配置文件，添加上层时间服务器

```
server   192.168.10.20   iburst
```


开启ntp服务：service ntpd  start 

同步时间:   ntpdate    192.168.10.20  

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中