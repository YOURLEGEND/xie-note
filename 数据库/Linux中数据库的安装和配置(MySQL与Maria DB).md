**目录**

[MySQL和Maria DB的介绍](#t0)

 [MySQL和Maria DB的安装](#t1)

[yum源安装MySQL（Centos6.5+Mysql5.1）](#t2)

[源码包安装MySQL](#t3)

[yum源安装Maria DB](#t4)

[源码包安装Maria DB](#t5)

[数据库的配置文件](#t6)

[数据库的配置](#t7)

[数据库的初始化配置](#t8)

[数据库的用户管理](#t9)

[数据库的用户权限](#t10)

[配置phpMyAdmin管理数据库](#t11)

[MySQL数据库的安全加固](#t12)

[修改root用户口令](#t13)

[绕过权限认证，空密码登录](#t14)

* * *

MySQL和Maria DB的介绍
-----------------

**MySQL**是世界上最流行的开源数据库，是由瑞典的MySQL AB公司开发，目前属于Oracle公司所有。端口号：tcp / 3306

MySQL的优点：MySQL是免费的，并且MySQL可以支持多种平台，从小型的WEB应用到大型的企业应用，MySQL都能经济有效的支撑。 MySQL支持标准的结构化查询语言 MySQL为客户端提供了不同的程序接口和链接库，如C、C++、Java、PHP等

**Maria DB**：MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。MariaDB继承了MySQL的所有特点，并且二者之间在许多功能上也是兼容的，同时MariaDB还进行了大量的改进。在存储引擎方面，Maria DB使用XtraDB来代替MySQL的InnoDB。MariaDB基于事务的Maria存储引擎，替换了MySQL的MyISAM存储引擎。

Maria DB的历史：2008年 Sun Microsystems以10亿美元收购MySQL AB这家开发并拥有MySQL数据库的公司。2011年Oracle收购了SUN Microsystems从而将MySQL也一并收归旗下。 虽然Oracle没有中止MySQL项目，但从MySQL项目的后继开发进程来看，MySQL存在巨大的闭源风险。开源社区采用分支的方式避免这种风险，于是创建了MySQL的新分支MariaDB。

Maria DB的版本：MariaDB有两个系列的版本，一个是5.x版，由于MariaDB的历史原因，需要与MySQL保持兼容，因此这个版本在接口上与MySQL是相通的。 第二个是自2012年开始发行的10.X版，10.0.X.以MySQL5.5为基础，加上5.6版本中的功能以及自行开发的功能组合而成。现在的情况是，自10.x版开始，MariaDB将慢慢彻底与Mysql分道扬镳，从而形成一套新的开源数据库产品。

Maria DB包的命名(和MySQL类似)：  
MariaDB-5.1.71：

*   第1个数字 5 是主版本号，相同主版本号具有相同的文件格式。 
*   第2个数字 1 是发行级别，主版本号和发行级别组合到一起便构成了发行序列号。
*   第3个数字 71 是在此发行系列的版本号，随每个新发版本递增

 MySQL和Maria DB的安装
------------------

安装MySQL，目前Centos7以上的版本，yum源都是提供的Maria DB的包，所以Centos7以上的版本，要想安装MySQL，只能使用源码包安装。Centos7以下的版本，yum源都是提供的MySQL5.1版本的包，所以如果想安装更高MySQL版本的包的话，也得需要源码包安装。

安装Maria DB的话，可以使用yum源或者源码包安装都可以，使用源码包安装定制性更强。

注：虽然在Centos7及以上的版本中，mysql更改为MariaDB了，但是许多工具仍然是以 mysql 命名，是为了保持兼容性。

### yum源安装MySQL（Centos6.5+Mysql5.1）

1.  安装mysql相关包：yum   -y   install    mysql-server
2.  开启mysqld服务： service   mysqld   start 
3.  登录： mysql

### 源码包安装MySQL

*   创建mysql的数据目录： mkdir   -p   /data/mysql
*   添加mysql用户： useradd   -M   -s  /sbin/nologin   mysql
*   修改数据库目录属主和属组： chown  -R  mysql:mysql   /data/mysql
*   安装cmake及其依赖： yum  -y  install   cmake   gcc\*   ncurses-devel  
*   下载mysql源码包：wget http://dev.mysql.com/get/Downloads/MySQL-5.5/mysql-5.5.49.tar.gz
*   解压，然后进入其解压包内：  tar  -xzvf  mysql-5.5.49.tar.gz   
*   cmake编译：

```
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DDEFAULT_CHARSET=utf8 -DENABLED_LOCAL_INFILE=1 -DMYSQL_DATADIR=/data/mysql -DWITH_EXTRA_CHARSETS=all -DWITH_READLINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_TCP_PORT=3306 -DDEFAULT_COLLATION=utf8_general_ci
```


*   安装：  make && make install
*   修改安装目录属主和属组： chown  -R   mysql:mysql    /usr/local/mysql
*   配置文件的修改： vim  /etc/my.cnf    

```
[mysqld]      
datadir=/data/mysql              //修改成我们自己创建的数据库目录      
socket=/tmp/mysql.sock           //将这个修改，因为在 /usr/local/mysql/support-files/ 目录中的配置文件(my-small/medium/large.cnf)中指定了 socket 的目录为 /tmp/mysql.sock       
[mysqld_safe]      
log-error=/data/mysql/mysql.log     //修改成我们自己创建的数据库目录，文件名也可以自己定义      
pid-file=/data/mysql/mysql.pid      //修改成我们自己创建的数据库目录，文件名也可以自己定义       1
!includedir /etc/my.cnf.d
```


*   初始化mysql，进入 /usr/local/mysql/scripts/ 目录下：./mysql\_install\_db  --user=mysql  --basedir=/usr/local/mysql/   --datadir=/data/mysql/
*   启动MySQL： service  mysqld  start   （cp   /usr/local/mysql/support-files/mysql.server    /etc/init.d/mysqld）
*   登录mysql： mysql   （为了使得能在任意位置执行，执行命令：ln  -s  /usr/local/mysql/bin/\*    /usr/local/bin/）

### yum源安装Maria DB

1.  安装mariadb相关包：yum   -y   install   mariadb-server
2.  开启mariadb服务： systemctl    start    mariadb
3.  登录：  mysql

### 源码包安装Maria DB

首先在安装Maria DB之前，先安装好 cmake。因为Maria DB的源码包是使用 cmake 工具编译的。而安装 cmake 工具前，先安装好 cmake的依赖包： readline-devel 、zlib-devel 、openssl-devel 、libaio-devel 。

*   安装cmake依赖包：yum -y install readline-devel  zlib-devel  openssl-devel  libaio-devel
*   安装 cmake 工具： yum  -y install  cmake

因为我们需要对Maria DB进行定制，所以在安装Maria DB之前先做好准备：

*   创建数据库存放目录： mkdir  -p   /data/mariadb
*   创建 mariadb 用户： useradd -M  -s   /sbin/nologin    mysql
*   修改数据库目录的属主和属组： chown  -R  mysql:mysql   /data/mariadb

做好了准备工作之后，我们就可以进行安装了。解压Marid DB压缩包，然后进入其压缩包。

cmake配置编译：  

```
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb -DMYSQL_DATADIR=/data/mariadb -DSYSCONFDIR=/etc -DWITHOUT_TOKUDB=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STPRAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWIYH_READLINE=1 -DWIYH_SSL=system -DVITH_ZLIB=system -DWITH_LOBWRAP=0 -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci       
//各参数的意义      
-DCMAKE_INSTALL_PREFIX是指定安装的位置， 这里是/usr/local/mariadb      
-DMYSQL_DATADIR是指定MySQL的数据目录，这里是/data/mariadb      
-DSYSCONFDIR是指定配置文件所在的目录，一般都是/etc ，具体的配置文件是/etc/my.cnf      
-DWITHOUT_TOKUDB=1这个参数一般都要设置上，表示不安装tokudb引擎，tokudb是MySQL中一款开源的存储引擎，可以管理大量数据并且有一些新的特性，这些是Innodb所不具备的，这里之所以不安装，是因为一般计算机默认是没有Percona Server的，并且加载tokudb还要依赖jemalloc内存优化，一般开发中也是不用tokudb的，所以暂时屏蔽掉，否则在系统中找不到依赖会出现：CMake Error at storage/tokudb/PerconaFT/cmake_modules/TokuSetupCompiler.cmake:179 (message)这样的错误      
然后后面那些参数都是可选的，可以加也可以不加，最后的编码建议设置一下，所以编译指令也可以简化成下面这样：      1
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb  -DMYSQL_DATADIR=/data/mariadb  -DSYSCONFDIR=/etc -DWITHOUT_TOKUDB=1 -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci       1
注意：如果万一执行中有了错误，可以执行： rm -f CMakeCache.txt 删除编译缓存，然后重新执行指令，否则每次读取这个文件，命令修改正确也是报错
```


*   编译和安装：  make  &&  make  install
*   修改安装目录的属主和属组：chown  -R  mysql:mysql   /usr/local/mariadb
*   进入/usr/local/mariadb/scripts目录下，初始化数据库： mysql\_install\_db  --datadir=/data/mariadb   --user=mariadb
*   修改/etc/my.cnf 配置文件 

```
datadir=/data/mariadb          //修改为我们创建的数据库目录      
//socket=/var/lib/mysql/mysql.sock   //如果是这样的话，开启mysqld程序的时候，会开启非常久，最后显示失败。但是进程已经在后台运行了      
socket=/tmp/mysql.sock       //这样的话，可以开启。但是mysql进入的时候，还要建立软链接 ln -s /tmp/mysql.sock  /var/lib/mysql/mysql.sock       
[mysqld_safe]      
log-error=/data/mariadb/mariadb.log    //日志文件的路径，必须在数据库目录下      
pid-file=/data/mariadb/mariadb.pid    //pid文件的路径，必须在数据库目录下
```


*   启动数据库： service mysqld  start  （前提是执行了该命令：cp  /usr/local/mariadb/support-files/mysql.server   /etc/init.d/mysqld）
*   登录： mysql  (提前建立软链接：ln  -s  /tmp/mysql.sock   /var/lib/mysql/mysql.sock）

### 数据库的配置文件

不管是mysql还是Maria DB，都有一个默认配置文件 /etc/my.cnf  ，当我们在数据库中没有指定配置文件时，读取的是 /etc/my.cnf ， 当我们在数据库中自己创建了 my.cnf  文件时 ，读取的是数据库中我们自己创建的 my.cnf  配置文件

数据库目录默认为： /var/lib/mysql

```
[mysqld]      
datadir=/var/lib/mysql          //数据库的路径      
socket=/var/lib/mysql/mysql.sock      
# Disabling symbolic-links is recommended to prevent assorted security risks      
symbolic-links=0      
# Settings user and group are ignored when systemd is used.      
# If you need to run mysqld under a different user or group,      
# customize your systemd unit file for mariadb according to the      
# instructions in http://fedoraproject.org/wiki/Systemd       1
[mysqld_safe]       1
log-error=/var/log/mariadb/mariadb.log         //日志文件的路径，必须在数据库目录下      1
pid-file=/var/run/mariadb/mariadb.pid          //pid文件的路径，必须在数据库目录下       1
#      1
# include all files from the config directory      1
#      1
!includedir /etc/my.cnf.d
```


数据库的配置
------

### 数据库的初始化配置

数据库安装好之后，我们就需要对数据库进行一些基本的配置了。不管是MySQL还是Maria DB，数据库的配置方法都是一样的。

数据库安装好之后，root用户默认是没有密码的，我们可以设置初始密码：mysqladmin  –u  root  password

数据库的安全初始化： mysql\_secure\_installation

*   为root用户设置密码 (如果之前设置了初始化密码的话，这里也可以修改，也可以不修改)
*   删除匿名账号 
*   取消root用户远程登录 
*   删除test库和对test库的访问权限
*   刷新授权表使修改生效

### 数据库的用户管理

创建用户： create   user   用户名@'ip'   identified   by   '密码'

*   create   user  tom@'localhost'  identified   by  '123456';    //创建tom用户，密码为123，只允许本地登录
*   create   user  jack@'%'  identified   by   'abc';     //创建jack用户，密码abc，允许任意ip登录

修改用户名：rename  user   原用户名@'ip'    to    新用户名

*   rename  user   tom@'localhost'   to  jack;       //将user用户重命名为jack

删除用户：drop  user   用户名@‘ip’

*   drop  user  tom@'localhost';           //删除tom用户

修改密码：set  password  for  用户名@'ip'=password('新密码');   (方法一)  
                update  mysql.user  set password=password('新密码')   where  user='用户名';  （修改完之后  flush  privileges 刷新权限）

*    set  password  for  jack@'localhost'=password('123456');
*    update  mysql.user  set  password=password('123456')  where  user='jack' ;   flush   privileges;

如果忘记了root用户的密码：

*   停止mysql服务：  service   mysqld   stop
*   跳过授权列表：mysqld\_safe  --user=mysql  --skip-grant-tables &
*   登录： mysql  -uroot
*   更新密码：update mysql.user set  password=password('newpassword')   where    user='root';
*   刷新权限：mysql> flush  privileges;
*   退出：mysql> quit
*   然后重启mysql服务，登录即可： service  mysqld  restart ;  mysql  -uroot  -p 

### 数据库的用户权限

权限可以分为以下四个层级：

*   全局级别：\*.\*
*   数据库级别： 数据库名.\*
*   表级别： 数据库名.表名
*   列级别：权限(列1、列2、.....)on  数据库名.表名

全局级别的权限存放在 mysql.user表中，数据库级别的权限存放在 mysql.db 或者 mysql.host 中，表级别的权限存放在 mysql.tables\_priv 中，列级别的权限存放在 mysql.columns\_priv 中  
查看用户权限： show  grants   for  用户名@'ip';

*   show  grants  for  tom@'localhost';

赋予用户权限： grant  权限列表  on   权限层级　to   用户@'ip'

*   grant  select,update,delete,insert  on   \*.\*  to   jack@'localhost';
*   grant  select,update   on   test.\*  to   jack@'localhost';
*   grant  select,update   on   test.user  to   jack@'localhost';
*   grant  select(id,name) on test.user  to  jack@'localhost'
*   grant  all   on  \*.\*   to   jack@'192.168.10.10' identified  by  '123';        创建jack用户，密码为123，并且授予全局权限

配置phpMyAdmin管理数据库
-----------------

phpmyadmin是一款利用网页图形化管理数据库的一款应用，需要php的支持。所以，需要提前配置好php的环境。

phpmyadmin的安装可以使用yum源安装或者源代码安装。

yum  -y  install  phpmyadmin  ，然后将 /usr/local/phpMyAdmin 移动到网站根目录下即可

源代码安装的话直接把解压包移动到网站根目录即可。

MySQL数据库的安全加固
-------------

### **修改root用户口令**

```
#方法一      
mysqladmin -u root password "newpass"      适用于root未设置密码的时候      
mysqladmin -u root -p password new_pass    root设置了密码后，可以使用这个命令。回车后输入旧密码即可       
#方法二      
当MySQL<5.7版本时      
use mysql;      
update user set password=PASSWORD("root_pass") where user="root";      
flush privileges;       1
当MySQL>=5.7版本时      1
use mysql;      1
update user set authentication_string=password('root_pass') where user='root';      1
flush privileges;       1
当MySQL>=8.0时，取消了 password() 函数，所以我们这里第二步new_pass可以先用其他版本的mysql的password函数生成秘钥，然后写入。      1
use mysql;      1
update user set authentication_string=new_pass where user='root';      1
flush privileges;
```


![](https://img-blog.csdnimg.cn/20191105221739300.png)

### ![](https://img-blog.csdnimg.cn/20191105224713549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191105224546567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191105224013773.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 绕过权限认证，空密码登录

在Linux环境中，当我们忘记了MySQL的密码该咋办呢？

```
关闭MySQL数据库进程：service mysqld stop      
以不检查权限的方式启动：mysqld_safe --skip-grant-tables&      
空密码登录：mysql -uroot -p      
登录进去之后，然后可以按照上面的步骤修改数据库密码了。
```


![](https://img-blog.csdnimg.cn/20191105230658433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[SQL语句](https://blog.csdn.net/qq_36119192/article/details/82875868)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10973 人正在系统学习中