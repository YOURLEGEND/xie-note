**LAMP**是Linux+[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020)+Mysql/MariaDB+Perl/PHP/Python的简称。一组常用来搭建动态网站或者服务器的开源软件，本身都是各自独立的程序，但是因为常被放在一起使用，拥有了越来越高的兼容度，共同组成了一个强大的Web应用程序平台。因为各个部分都是免费的，所以受到广大中小网站的青睐。

apache主要实现如下功能：

*   处理http的请求、构建响应报文等自身服务
*   配置让Apache支持PHP程序的响应（通过PHP模块或FPM） 
*   配置Apache具体处理php程序的方法，如通过反向代理将php程序交给fcgi处理

php主要实现如下功能：

*   提供apache的访问接口，即CGI或Fast CGI(FPM)
*   提供PHP程序的解释器
*   提供mairadb数据库的连接函数的基本环境

MySQL/Mariadb主要实现如下功能：

*   提供PHP程序对数据的存储
*   提供PHP程序对数据的读取(通常情况下从性能的角度考虑，尽量实现数据库的读写分离)

### **LAMP架构工作原理**

![](https://img-blog.csdn.net/2018092822542953?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

下面就开始在LAMP环境下搭建Discuz论坛！

Apache的配置

*   安装apache： yum  -y install  httpd  
*   开启httpd服务： systemctl  start  httpd
*   设置httpd服务开机自启： systemctl  enable  httpd

php组件的配置

*   安装php组件： yum  -y  install  php
*   安装php连接mysql的组件： yum  -y install   php-mysql

数据库的配置

*   安装mysql： yum  -y  install   mysql-server / mariadb-server   ,
*   开启数据库： systemctl  start  mysqld / mariadb 
*   并且设置为开机自启： systemctl  enable  mysqld /  mariadb
*   初始化数据库，设置root密码： mysql\_secure\_installation
*   登录数据库： mysql  -uroot  -p
*   创建discuz的数据库： mysql> create  database   discuz;
*   给discuz创建一个用户，并且赋予权限： grant  all  on  discuz.\*   to  test@'localhost' identified  by '123'

网站的配置：

*   下载Discuz包：wget http://download.comsenz.com/DiscuzX/3.2/Discuz\_X3.2\_SC\_UTF8.zip
*   解压：unzip Discuz\_X3.2\_SC\_UTF8.zip
*   进入解压包的upload目录，将里面所有文件移动到/var/www/html/目录下：mv  \*  /var/www/html
*   赋予/var/www/html目录下的所有文件777权限：  chmod  -R 777  /var/www/html/
*   重启httpd服务： systemctl restart  httpd

访问页面即可，如果显示下面页面说明配置正确

![](https://img-blog.csdn.net/20180928190928422?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180928191005282?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 相关文章：[Linux下Apache服务的部署和配置](https://blog.csdn.net/qq_36119192/article/details/82768277)

###                   [Linux中数据库的安装和配置(MySQL与Maria DB)](https://blog.csdn.net/qq_36119192/article/details/82834755)