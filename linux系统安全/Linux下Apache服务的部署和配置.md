**目录**

[Apache服务的安装](#t0)

[yum源安装：](#t1)

[目录文件](#t2)

[源码包安装：](#t3)

[目录文件：](#t4)

[Apache中添加对php的支持](#t5)

[Apache中添加php对mysql数据库的支持](#t6)

[Apache服务的高级配置](#t7)

[1：配置基于ip的虚拟主机](#t8)

[2\. 配置基于端口的虚拟主机](#t9)

[3.配置基于域名的虚拟主机](#t10)

[HTTP服务的访问控制](#t11)

[基于ip的用户访问控制的配置：](#t12)

[基于用户身份认证授权的配置：](#t13)

* * *

Apache 是世界使用排名第一的Web服务器软件。它可以运行在几乎所有广泛使用的计算机平台上，由于其跨平台和安全性被广泛使用，是最流行的Web服务器端软件之一。它快速、可靠并且可通过简单的API扩充，将Perl/Python等解析器编译到服务器中。但是[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020)有一个缺点就是，重新加载配置文件时需要重启 httpd 服务，而nginx重载配置文件时只需重载reload配置文件即可。httpd服务默认监听 tcp / 80 端口

apache有两个日志文件，access.log记录了访问网站的日志；error.log记录了错误日志

Apache服务的安装
===========

**yum源安装：**
-----------

1.  安装httpd主程序：  yum  -y  install  httpd
2.  在/var/www/html 目录下写入我们自己的主页 index.html
3.  开启httpd程序： systemctl  start httpd
4.  防火墙开放http服务  firewall-cmd  --add-service=http ,关闭SElinux： setenforce=0
5.  查看apache版本号：httpd -v

### 目录文件

*   程序目录：/usr/sbin/httpd
*   默认网站主页存放目录： /var/www/html/
*   日志文件存放目录：/var/log/httpd/
*   主配置文件：/etc/httpd/conf/httpd.conf
*   从配置文件：/etc/httpd/conf.d/

主配置文件 /etc/httpd/conf/httpd.conf 的一些参数的意义

```
ServerRoot “/etc/httpd“                        //服务配置文件目录      
PidFile run/httpd.pid		                   //PID文件      
Listen 80			                           //默认监听端口      
Include conf.modules.d/*.conf		           //包含模块目录的配置文件      
User apache			                           //启动用户      
Group apache		        	               //启动组      
ServerAdmin root@localhost	                   //管理员邮箱      
ServerName www.example.com:80	               //域名主机名      
DocumentRoot “/var/www/html“	               //默认主页存放目录      1
DirectoryIndex index.html index.html.var	   //索引文件      1
<Directory "/var/www">                         //规定网站根目录的位置      1
    AllowOverride None      1
    # Allow open access:      1
    Require all granted      1
</Directory>      1
ErrorLog logs/error_log			               //错误日志      1
CustomLog logs/access_log combined	           //访问日志      1
AddDefaultCharset UTF-8                        //默认字符集      1
IncludeOptional conf.d/*.conf                  //包含了 conf.d/*.conf 的配置文件       2
<IfModule dir_module>      2
    DirectoryIndex index.html                  //默认索引      2
</IfModule>
```


 扩展配置文件： /etc/httpd/conf.d/   , 我们可以在这个目录下新建一些我们自己定义的配置文件，比如基于ip的虚拟主机，基于端口的虚拟主机，基于域名的虚拟主机等等

**源码包安装：**
----------

1.  源码安装之前先安装编译工具： yum  -y  install  gcc  gcc-c++  make 
2.  源码包： httpd-xxxx.tar.bz2
3.  解压，然后进入解压目录， ./configure 编译， make && make install  安装即可 
4.  进入安装目录的 bin 目录下, ./httpd 启动

### 目录文件：

![](https://img-blog.csdn.net/20180922091025475?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

安装完成后，软件默认安装在  /usr/loca/apache2/  下面，也可以编译的时候自定义安装目录

*   可执行文件目录：/usr/local/apache2/bin/
*   网站主页目录：/usr/local/apache2/htdocs/
*   配置文件目录：/usr/local/apache2/conf/
*   日志文件目录：/usr/local/apache2/logs/

Apache中添加对php的支持
================

安装php的支持包    yum  -y  install   php ，然后重启 httpd 服务即可

如果想让Apache识别默认的index.php文件，需要修改配置文件 /etc/httpd/conf/httpd.conf 中，在如下位置加入 index.php  

```
<IfModule dir_module>      
    DirectoryIndex index.php index.html      
</IfModule>
```


Apache中添加php对mysql数据库的支持
========================

安装php-mysql的包即可：yum  -y  install php-mysql  

Apache服务的高级配置
=============

以下不管是基于ip、端口还是基于域名创建的虚拟主机，网站主页目录只能在 /var/www/ 下，如果想更改到其他的任意目录的话，需要修改配置文件  /etc/httpd/conf/httpd.conf

```
<Directory "/var/www">        //这里的目录更改为你的网站主页目录      
    AllowOverride None      
    # Allow open access:      
    Require all granted      
</Directory>
```


**1：配置基于ip的虚拟主机**
-----------------

  
可以在一台服务器上配置多个网卡，也可以在一块网卡上配置爱多个ip  
我这里在一块网上上配置多个ip：   ifconfig  eno16777736:1   10.96.10.204

![](https://img-blog.csdn.net/20180919110046425?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后在从配置文件中  /etc/httpd/conf.d/   下创建一个配置文件，我们新建一个 ip.conf 文件，然后加入下面几行

```
<VirtualHost 10.96.10.203>      
        DocumentRoot /var/www/html/web1     //第一个ip的网站的主页面目录      
</VirtualHost>      
<VirtualHost 10.96.10.204>      
        DocumentRoot /var/www/html/web2    //第二个ip的网站的主页面目录      
</VirtualHost>
```


然后在/var/www 下新建 web1和web2目录，在web1和web2目录里面分别放入我们的网站页面。然后重启httpd服务即可。

**2\. 配置基于端口的虚拟主机**
-------------------

在配置文件中  /etc/httpd/conf.d/   下创建一个配置文件，我们新建一个 port.conf 文件，然后加入下面几行

```
Listen 8080      
<VirtualHost *:80>      
        DocumentRoot  /var/www/html/web1      //第一个端口的网站主目录      
</VirtualHost>      
<VirtualHost *:8080>      
        DocumentRoot  /var/www/html/web2      //第二个端口的网站主目录      
</VirtualHost>
```


然后在/var/www 下新建 web1和web2目录，在web1和web2目录里面分别放入我们的网站页面。然后重启httpd服务即可。

**3.配置基于域名的虚拟主机**
-----------------

在配置文件中  /etc/httpd/conf.d/   下创建一个配置文件，我们新建一个 domain.conf 文件，然后加入下面几行

```
NameVirtualHost *:80      
<VirtualHost *:80>      
        DocumentRoot /var/www/web1     //第一个域名的网站主目录      
        ServerName web1.xie.com      
</VirtualHost>      
<VirtualHost *:80>      
        DocumentRoot /var/www/web2    //第二个域名的网站主目录      
        ServerName web2.xie.com      
</VirtualHost>
```


然后在/var/www 下新建 web1和web2目录，在里面放入我们的网站页面。然后再重启httpd服务即可。

HTTP服务的访问控制
===========

控制对特定主页内容的访问，为相应网站目录添加访问控制  
常用访问控制方式分类：

*   客户机地址限制
*   用户授权限制

设置客户机地址访问

*   Order配置项，定义控制顺序 Order allow，deny 先允许后拒绝，默认拒绝 Order deny，allow 先拒绝后允许，默认允许
*   Allow、Deny配置项，设置允许或拒绝的网段或地址 Deny from IP地址 或者 网段 Allow from IP地址 或者 网段

**基于ip的用户访问控制的配置：**
-------------------

在配置文件中  /etc/httpd/conf.d/   下创建一个配置文件，我们新建一个 ip.conf 文件，然后加入下面几行

```
// 这个的意思的允许所有用户访问/var/www/html 目录下的网页，但是只允许 10.96.10.204/24这个网段的访问 /var/www/html/test/ 目录下的网页      
<VirtualHost 10.96.10.203>        //本机ip      
DocumentRoot /var/www/html        //网站主页的目录      
<Directory /var/www/html/test>         //要设置限制的目录      
Order allow,deny                  //匹配了允许的就拒绝所有      
Allow from 10.96.10.204/24        //只允许10.96.10.204这个地址访问      
</Directory>      
</VirtualHost>
```


**基于用户身份认证授权的配置：**
------------------

访问控制的配置

*   在配置文件中  /etc/httpd/conf.d/   下创建一个配置文件，我们新建一个auth.conf 文件，然后加入下面几行
*   htpasswd  -c   /etc/httpd/webpasswd    tom （第一次加 -c 参数，表示创建文件）;   htpasswd  /etc/httpd/webpasswd  jack（第二次不用加 -c参数 ，因为文件已经创建）
*   重启服务验证

```
<VirtualHost 10.96.10.203>           //本机ip       
DocumentRoot /var/www/html           //主页目录      
<Directory /var/www/html/test>       //设置认证的目录      
AuthType Basic                       //认证的方法：密码      
AuthName Password!                   //弹出登录框      
AuthUserFile /etc/httpd/webpasswd   //用户名和密码保存的文件      
require user tom  jack              //只允许 tom 和 jack 用户访问      
</Directory>      
</VirtualHost>
```


### 相关文章： [Linux中Nginx服务器的部署和配置](https://blog.csdn.net/qq_36119192/article/details/82782309)

###                    [Linux中Tomcat和Jboss的安装和部署](https://blog.csdn.net/qq_36119192/article/details/82797114)

###                    [LAMP环境搭建一个Discuz论坛](https://blog.csdn.net/qq_36119192/article/details/82888439)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中