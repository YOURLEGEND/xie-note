**目录**

[DNS](#t0)

[域传送漏洞](#t1)

[域名空间结构](#t2)

[DNS解析过程](#t3) 

[各种解析记录](#t4)

[DNS服务器的安装与部署](#t5)

[主从DNS服务器的搭建：](#%C2%A0%20%E4%B8%BB%E4%BB%8EDNS%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%9A%84%E6%90%AD%E5%BB%BA%EF%BC%9A)

[转发DNS服务器的配置](#t6)

* * *

### DNS

DNS(Domain Name Service) [域名解析](https://so.csdn.net/so/search?q=%E5%9F%9F%E5%90%8D%E8%A7%A3%E6%9E%90&spm=1001.2101.3001.7020)服务，就是将域名和 ip 之间做相应的转换，利用 TCP 和 UDP 的53号端口  
DNS系统作用：

*   正向解析：根据域名查找对应的ip地址
*   反向解析：根据ip地址查找对应的域名

DNS服务器的分类：

*   主要名称服务器：存放该区域中相关设置的DNS服务器，其存放的是区域文件的正本数据
*   辅助名称服务器：从其他服务器中复制数据，数据为副本无法修改
*   主控名称服务器：提供趋于数据复制的DNS服务器
*   缓存域名服务器：通过像根或其他服务器查询获得域名到ip的解析关系，将查询结果缓存到本地，提高重复查找速度

### 域传送漏洞

**域传送：**是指后备服务器从主服务器拷贝数据，并用得到的数据更新自身数据库。 在主备服务器之间同步数据库，需要使用“DNS域传送”。

**域传送漏洞：**是由于DNS服务器配置不当，导致匿名用户利用DNS域传送协议获取某个域的所有DNS记录。

**域传送漏洞的危害**：网络拓扑结构泄露给潜在的攻击者，包括一些安全性较低的内部主机，如测试服务器等。直接加快、助长攻击者的入侵过程。

**测试过程：**

```
1) 输入nslookup命令进入交互式shell：nslookup      
2) server 命令参数设定查询将要使用的DNS服务器: server dns服务器      
3) ls命令列出某个域中的所有域名： ls 域名      
4) exit命令退出
```


![](https://img-blog.csdnimg.cn/2019021311484312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190214143214356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 域名空间结构

根域(.)

顶级域(顶级域包括组织域和国家/地区顶级域 (net、edu、com、gov、mail、org、cn、uk.... ）

   --组织顶级域 (net、edu、com、gov、mail、org....)

   --国家/地区顶级域(cn、uk...)

二级域名(baidu、taobao...)

三级域名(www、mail...)

| 
组织顶级域名

 | 

说明

 |
| --- | --- |
| 

gov

 | 

政府部门

 |
| 

com

 | 

商业部门

 |
| 

edu

 | 

教育部门

 |
| 

org

 | 

民间团体组织

 |
| 

net

 | 

网络服务机构

 |
| 

mil

 | 

军事部门

 |

| 
国家顶级域名

 | 

说明

 |
| --- | --- |
| 

cn

 | 

中国

 |
| 

hk

 | 

中国香港

 |
| 

uk

 | 

英国

 |
| 

…

 | 

 |

![](https://img-blog.csdn.net/20181018154244134?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### DNS解析过程 

![](https://img-blog.csdn.net/20181018160157176?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**DNS域名解析过程：**

客户机访问域名，先查看自己主机的DNS缓存(有时间限制)，如果主机DNS缓存有，则直接访问对应ip。

如果主机DNS缓存没有，则查看本地 hosts文件。

如果 hosts 文件没有，则将该请求发送给主机指定的域名服务器。域名服务器收到请求后，先查询本地的缓存，如果有该纪录项，则域名服务器就直接把查询的结果返回。如果指定的域名服务器的缓存中没有该记录，则进行以下迭代查询。

**【迭代查询】**

本地域名服务器向根域名服务器查询，根域名服务器告诉它下一步到哪里去查询，然后它在根据结果逐层向下查询，直到得到最终结果。每次它都是以DNS客户机的身份去各个服务器查询，即迭代查询是本地服务器进行的操作。

具体流程描述：

> (1)用户主机A先向本地域名服务器B递归查询abc.exmaple.com
> 
> (2)B首先尝试使用本地DNS记录查询abc.exmaple.com，若本地不存在abc.example.com的相关记录，则B以DNS客户机的身份发起迭代查询abc.example.com
> 
> (3)B向一个根域名服务器C查询abc.example.com
> 
> (4)根域名服务器C告诉B下一步到.com顶级域名服务器D去查询，并告知D的IP地址1.2.3.4
> 
> (5)B向.com顶级域名服务器D进行查询
> 
> (6)D告诉本地域名服务器B，下一步请到.example.com权限服务器E去查询，并告知E的IP地址2.3.4.5
> 
> (7)B向.example.com权限服务器E进行查询
> 
> (8)E告诉本地域名服务器B所查询域名abc.example.com的主机是否存在，并告知其IP地址3.4.5.6
> 
> (9)本地域名服务器B向用户主机A反馈所查询的域名abc.exmaple.com对用的IP地址为3.4.5.6

所以，优先级：  本地DNS缓存 > hosts文件  > DNS服务器  
windows中hosts文件存放路径：  C:\\Windows\\System32\\drivers\\etc\\hosts  
Linux中hosts文件存放路径：       /etc/hosts

### 各种解析记录

| 类型 | 说明 |
| --- | --- |
| A | 主机记录，记录域名对应的ip |
| PTR | 反向地址解析记录，记录ip对应的域名 |
| CNAME | 别名记录 |
| MX | 邮箱交换记录 |
| NS | 服务器记录 |
| SOA | 权威记录 |
| TXT | 为记录说明 |
| SRV | 列出正在提供特定服务的服务器 |
| AAAA | ipv6地址记录 |

**A记录**  
A (Address) 记录也叫主机记录，是用来指定域名对应的IP地址记录。用户可以将该域名下的网站服务器指向到自己的网页服务器(web server)上。同时也可以设置域名的子域名。通俗来说A记录就是服务器的IP，域名绑定A记录就是告诉DNS，当输入域名的时候给你引导向设置在DNS的A记录所对应的服务器。在命令行下可以通过nslookup -qt=a www.baidu.com来查看A记录。

![](https://img-blog.csdnimg.cn/20190411183416755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**PTR记录**  
相对于A记录，PTR记录是把IP地址转换为域名

![](https://img-blog.csdnimg.cn/20190411183350167.png)  
  
**CNAME记录**  
CNAME记录也称别名记录，它允许你将多个记录映射到同一台计算机上。比如你建了如下几条记录：

![](https://img-blog.csdn.net/20181018171226867?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
我们访问a1（a2，a3）.baidu.com的时候，域名解析服务器会返回一个CNAME记录，并且指向a.baidu.com，然后我们的本地电脑会再发送一个请求，请求a.baidu.com的解析，然后域名服务器会返回a.baidu.com的IP地址。

![](https://img-blog.csdn.net/20181018171217616?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
当我们要指向很多的域名到一台电脑上的时候，用CNAME比较方便，就如上面的例子，我们如果服务器更换IP了，我们只要更换 a.baidu.com 的A记录即可。  
在命令行下可以使用 nslookup -qt=cname a1.baidu.com 来查看CNAME记录。

![](https://img-blog.csdn.net/20181018172554253?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
**MX记录**  
MX记录的权重对 mail 服务是很重要的，当发送邮件时，Mail 服务器先对域名进行解析，查找 mx 记录。先找权重数最小的服务器（比如说是 10），如果能连通，那么就将发送过去；如果无法连通 mx 记录为 10 的服务器，那么才将邮件发送到权重为 20 的 mail 服务器上。  
这里有一个重要的概念，权重 20 的服务器在配置上只是暂时缓存 mail ，当权重 20 的服务器能连通权重为 10 的服务器时，仍会将邮件发送的权重为 10 的 Mail 服务器上。当然，这个机制需要在 Mail 服务器上配置。  
在命令行下可以通过 nslookup  -qt=mx  baidu.com 来查看MX记录。

![](https://img-blog.csdnimg.cn/20190411183458681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
**TXT记录**   
TXT记录一般是为某条记录设置说明，比如你新建了一条 a.ezloo.com 的TXT记录，TXT记录内容"this is a test TXT record."，然后你用 nslookup -qt=txt a.ezloo.com ，你就能看到"this is a test TXT record"的字样。  
除外，TXT还可以用来验证域名的所有，比如你的域名使用了Google的某项服务，Google会要求你建一个TXT记录，然后Google验证你对此域名是否具备管理权限。  
在命令行下可以使用 nslookup -qt=txt    baidu.com 来查看TXT记录

![](https://img-blog.csdnimg.cn/20190325140743944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里讲到TXT记录，我们就要谈谈TXT记录里面的 SPF 了。SPF是 Sender Policy Framework 的缩写，一种以IP地址认证电子邮件发件人身份的技术。 接收邮件方会首先检查域名的SPF记录，来确定发件人的IP地址是否被包含在SPF记录里面，如果在，就认为是一封正确的邮件，否则会认为是一封伪造的邮件进行退回。  
    SPF可以防止别人伪造你来发邮件，是一个反伪造性邮件的解决方案。当你定义了你域名的SPF记录之后， 接收邮件方会根据你的SPF记录来确定连接过来的IP地址是否被包含在SPF记录里面，如果在，则认为是一封正确的邮件，否则则认为是一封伪造的邮件。   
设置正确的 SPF 记录可以提高邮件系统发送外域邮件的成功率，也可以一定程度上防止别人假冒你的域名发邮件。

MX记录的作用是给寄信者指明某个域名的邮件服务器有哪些。SPF的作用跟MX相反，它向收信者表明，哪些邮件服务器是经过某个域名认证的可以发送邮件的。

由定义可以看出，SPF的作用主要是反垃圾邮件，主要针对那些发信人伪造域名的垃圾邮件。

**AAAA记录**  
AAAA记录是一个指向IPv6地址的记录。  
可以使用 nslookup -qt=aaaa  a.ezloo.com 来查看AAAA记录。  
**NS记录**  
NS记录是域名服务器记录，用来指定域名由哪台服务器来进行解析。  
可以使用 nslookup -qt=ns   baidu.com 来查看。

![](https://img-blog.csdnimg.cn/20190325140923698.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
**TTL值**  
TTL=time to live，表示解析记录在DNS服务器中的缓存时间。比如当我们请求解析www.ezloo.com的时候，DNS服务器发现没有该记录，就会向下个NS服务器发出请求，获得记录之后，该记录在DNS服务器上保存TTL的时间长度。当我们再次发出请求解析www.ezloo.com 的时候，DNS服务器直接返回刚才的记录，不去请求NS服务器。TTL的时间长度单位是秒，一般为3600秒

**SOA记录**

定义了该域中的权威名称服务器

![](https://img-blog.csdnimg.cn/2019041118410884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**SRV记录**

列出正在提供特定服务的服务器

### DNS服务器的安装与部署

程序所需的包：  bind (DNS服务器软件包) 、bind-utils(DNS测试工具，包含dig、host、nslookup等)、bind-chroot(使BIND运行在指定的目录中的安全增强工具)、caching-nameserver（高速缓存DNS服务器的基本配置文件，建议一定安装）  
可执行文件所在目录： /usr/sbin/named （Rhel7）     /etc/init.d/named （Rhel6）  
配置文件所在目录：  /etc/named.conf  
区域配置文件所在目录：/var/named/xxx.zone

1.  安装DNS程序： yum  -y  install  bind\*
2.  修改主配置文件：/etc/named.conf
3.  添加并修改区域配置文件  /var/named/xxx.zone
4.  修改文件权限： chown   named:named   /var/named/xxx.zone
5.  开启服务，并验证     systemct  start  named  ;    nslookup  www.xie.com

修改主配置文件： /etc/named.conf

```
options {      
        listen-on port 53 { any; };             //修改行      
        listen-on-v6 port 53 { ::1; };      
        directory       "/var/named";      
        dump-file       "/var/named/data/cache_dump.db";      
        statistics-file "/var/named/data/named_stats.txt";      
        memstatistics-file "/var/named/data/named_mem_stats.txt";         
        allow-query     { any; };              //修改行      
}       1
zone "." IN {                              //系统自定义的根域服务器，必须要有      1
        type hint;      1
        file "named.ca";      1
};       1
zone "xie.com." IN{                        //自定义的，添加正向解析      1
        type master;                       //主从DNS      1
        file "xie.com.zone";               //指定区域配置文件名,在/var/named/ 目录下      1
};       2
zone "10.168.192.in-addr.arpa" IN{        //自定义的，添加反向解析      2
        type master;      2
        file "xie.com.zone";                      2
};       2
zone "mi.com." IN {                       //添加对另一个域名的解析      2
        type master;      2
        file "mi.com.zone"      2
}
```


在 /var/named/ 目录下分别创建  xie.com.zone 和 mi.com.zone  文件 ，并且如下配置

```
//  xie.com.zone 文件的配置      
$TTL 1D      
@       IN SOA  www.xie.com.  root.xie.com. (                      // @代表本机      
                                        0       ; serial      
                                        1D      ; refresh      
                                        1H      ; retry      
                                        1W      ; expire      
                                        3H )    ; minimum      
        NS      www.xie.com.      1
        MX 10   root.xie.com.         //邮箱      1
www     IN A    192.168.10.100       //这个必须写在第一条！！      1
web     IN A    192.168.10.110      1
root    IN A    192.168.10.120      1
*       IN A    192.168.10.130     //添加一个默认匹配，当配置文件中的都没匹配，则匹配此条      1
ftp     IN CNAME   www              //给www.xie.com添加一个别名 ftp.xie.com      1
1       IN PTR  web1.xie.com.          //添加反向解析记录  192.168.10.1 解析为 web1.xie.com      1
2       IN PTR  web2.xie.com.        2
// mi.com.zone 文件的配置       2
$TTL 1D      2
@       IN SOA  www.mi.com. root.mi.com. (      2
                                        0       ; serial      2
                                        1D      ; refresh      2
                                        1H      ; retry      2
                                        1W      ; expire      2
                                        3H )    ; minimum      2
        NS      www.mi.com.      3
        MX 10   root.mi.com.      3
www     IN A    192.168.10.100      3
web     IN A    192.168.10.200
```


###   主从DNS服务器的搭建：

主DNS服务器在上面配置的基础上，在主配置文件 /etc/named.conf 中加入下面一行，意思是允许转移

```
allow-transfer  { 192.168.10.10; };     // 允许 从DNS服务器的ip地址
```


然后从DNS服务器要同步哪个域名的就写哪个zone，我们这里只同步 xie.com 这个域名的 ，同步的文件路径默认是  /var/named/slaves　下

```
zone "xie.com." IN {      
        type slave;      
        file "slaves/xie.com.zone";            //指定配置文件目录      
	masters { 192.168.10.124; };              //指定主DNS IP      
	};      
zone "10.168.192.in-addr.arpa" IN{      
        type slave;      
        file "slaves/xie.com.zone";      
        masters{ 192.168.10.128; };      1
};
```


配置完成以后重启 named 服务： systemctl restart  named  ,然后同步域配置文件：  rndc  reload

### 转发DNS服务器的配置

转发DNS服务器就是当你向一个DNS服务器请求DNS解析时，他把DNS请求转发到了另一个DNS服务器上

转发DNS服务器也要求安装bind包： yum  -y  install  bind\*

然后修改主配置文件：/etc/named.conf

```
 option{      
   listen-on port 53 { any; };   #修改       
   allow-query     { any; };     #修改       
   forwarders  { 192.168.10.10; };   # 指定你要把DNS请求转发给哪个DNS服务器      
}
```


相关文章：[子域名查询、DNS信息探测、IP转换经纬度](https://blog.csdn.net/qq_36119192/article/details/84068625)