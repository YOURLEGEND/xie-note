**目录**

[JDK环境](#t0)

[yum源安装JDK](#t1)

[源码包安装JDK](#t2)

[Tomcat的安装](#t3)

[yum源安装](#t4)

[目录结构：](#t5)

[源码包安装](#t6)

[目录结构：](#%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84%EF%BC%9A)

[目录中主要的文件：](#t7)

[JBoss的安装](#t8)

[目录结构：](#t9) 

* * *

****Tomcat****是[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) 软件基金会（Apache Software Foundation）的Jakarta 项目中的一个核心项目，由Apache、Sun 和其他一些公司及个人共同开发而成。因为Tomcat 技术先进、性能稳定，而且免费，因而深受Java 爱好者的喜爱并得到了部分软件开发商的认可，成为目前比较流行的Java Web 应用服务器(Servlet 容器)或web中间件。实际上Tomcat 部分是Apache 服务器的扩展，但它是独立运行的，所以当你运行tomcat 时，它实际上作为一个与Apache 独立的进程单独运行的。Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试 JSP 程序的首选。Tomcat默认使用 8080 号端口

**Jboss**是一个基于Java EE的开放源代码的应用服务器。 它不但是**Servlet容器**，而且是**EJB容器**，从而受到企业级开发人员的欢迎，从而弥补了Tomcat只是一个Servlet容器的缺憾。JBoss是一个管理EJB的容器和服务器。但JBoss核心服务不包括支持 servlet/JSP 的WEB容器，一般与 Tomcat 或 Jetty 绑定使用。

Jboss的优势：JBoss Web采用业界最优的开源Java Web引擎，将Java社区中下载量最大，用户数最多，标准支持最完备的Tomcat内核作为其 Servlet 容器引擎，并加以审核和调优。单纯的Tomcat性能有限，在很多地方表现有欠缺，如活动连接支持、静态内容、大文件和HTTPS等。除了性能问题，Tomcat的另一大缺点是它是一个受限的集成平台，仅能运行Java应用程序。企业在使用时Tomcat，往往还需同时部署Apache Web Server以与之整合。此配置较为繁琐，且不能保证性能的优越性。JBoss在Tomcat的基础上，对其进行本地化，将Tomcat 以内嵌的方式集成到 JBoss 中。JBoss Web通过使用APR和Tomcat本地技术的混合模型来解决Tomcat的诸多不足。混合技术模型从最新的操作系统技术里提供了最好的线程和事件处理。结果，JBoss Web达到了可扩展性，性能参数匹配甚至超越了本地Apache HTTP服务器或者IIS。譬如JBoss Web能够提供数据库连接池服务，不仅支持 JSP 等 Java 技术，同时还支持其他 Web 技术的集成，譬如 PHP、.NET 两大阵营。

**JDK环境**
=========

tomcat的运行需要jdk环境，jdk 是（Java Development Kit），里面包含了java运行环境 JRE、java工具和java基础类。

java  -version   查看jdk的版本，如果显示了就说明安装了。如果没有显示，就说明没有安装，那么按照下面的过程进行安装

![](https://img-blog.csdn.net/20180922110901221?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

yum源安装JDK
---------

[yum](https://so.csdn.net/so/search?q=yum&spm=1001.2101.3001.7020)源安装的好处是可以不用自己手动去配置环境变量

先查看yum源中有多少关于java的包：yum  list  java\*   

然后安装jdk的包： yum -y install java-1.8.0-openjdk\*

源码包安装JDK
--------

安装： rpm -ivh  jdk-8u20-linux-x64.rpm

配置环境变量： 在系统配置文件 /etc/profile 中，加入以下几行

```
///视不同版本而定      
export JAVA_HOME=/usr/java/jdk1.8.0_20      
export JAVA_BIN=/usr/java/jdk1.8.0_20/bin      
export PATH=$PATH:$JAVA_HOME/bin      
export CLASSPATH=:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar      
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```


重载配置文件，使其生效： source  /etc/profile

Tomcat的安装
=========

**yum源安装**
----------

*   安装tomcat主程序：yum  -y  install  tomcat      
*   安装浏览器管理界面：yum  -y  install tomcat-webapps  tomcat-admin-webapp
*   安装完之后手动在 /etc/firewalld/services下新建 tomcat.xml 配置文件。此配置文件是用于 firewalld 防火墙添加 tomcat服务的。添加完该文件后，就可以用firewalld添加该服务了。firewall-cmd   --add-service=tomcat

```
<service>      
    <short> Tomcat Webserver</short>      
    <description>tomcat server</description>      
    <port protocol="tcp" port="8080"/>      
</service>
```


### 目录结构：

*   可执行文件目录： /usr/sbin/tomcat/
*   网站主页目录：/usr/share/tomcat/webapps/   (包含web应用的程序，JSP、Servlet 和 JavaBean等，主页显示的在 webapps/ROOT/ 目录下)
*   配置文件目录：/etc/tomcat/    
*   主配置文件： /etc/tomcat/server.xml
*   日志文件目录：/var/log/tomcat
*   库文件目录：/var/lib/tomcat

**源码包安装**
---------

注：jdk1.7 版本只能支持 tomcat7以及以下的源码包，jdk1.8可以支持tomcat7以上的源码包

我这里是jdk1.7的版本，所以我用tomcat7的包。解压 tomcat 压缩包：tar -zxvf apache-tomcat-7.0.47.tar.gz   ，得到绿色免安装版解压包，然后直接把解压包移动到你想要存放的路径，我直接把他移动到 /usr/local/ 目录下，并且重命名为 tomcat ： mv   apache-tomcat-7.0.47     /usr/local/tomcat/

![](https://img-blog.csdn.net/2018092211105681?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 目录结构：

*   可执行文件目录： bin
*   配置文件目录：conf  （主配置文件：conf/server.xml）
*   网站主页目录：webapps/     (包含web应用的程序，JSP、Servlet 和 JavaBean等，主页显示的在 webapps/ROOT/ 目录下)
*   日志文件目录：logs
*   tomcat运行需要的库文件目录：lib
*   临时文件存放目录：temp
*   工作目录：work   (里面的内容是 tomcat 自动生成，tomcat将jsp翻译成的java文件和class文件放在这里)

### 目录中主要的文件：

**bin：**

*   catalina.sh       用于启动和关闭tomcat服务器
*   configtest.sh    用于检查配置文件
*   startup.sh        启动Tomcat脚本
*   shutdown.sh   关闭Tomcat脚本

**conf：**

*   server.xml      Tomcat 的全局配置文件
*   web.xml         为不同的Tomcat配置的web应用设置缺省值的文件
*   tomcat-users.xml    Tomcat用户认证的配置文件

如果要修改网站根目录的话，在主配置文件 /conf/server.xml 中的 host 端中加入下面的内容

```
<Context path="" docBase="/var/www/html" debug="0" reloadable="true" crossContext="true" />
```


JBoss的安装
========

JBoss是一个基于J2EE的开放源代码的应用服务器。 JBoss代码遵循LGPL许可，可以在任何商业应用中免费使用。JBoss是一个管理EJB的容器和服务器，支持EJB 1.1、EJB 2.0和EJB3的规范。但JBoss核心服务不包括支持servlet/JSP的WEB容器，一般与Tomcat或Jetty绑定使用。

1、解压压缩包，得到绿色免安装版JBoss： tar  -xvzf   jboss-as-7.1.1.Final.tar.gz   
2、我把他移动到 /usr/local 目录下，并重命名为 jboss：mv   jboss-as-7.1.1.Final     /usr/local/jboss  
3、设置jboss环境变量，在/etc/profile.d/ 下新建 jboss.sh 文件，加入这两行内容：       
      export JBOSS\_HOME=/usr/local/jboss  
      export PATH=$JBOSS\_HOME/bin:$PATH  
4、修改默认监听地址，打开 /usr/local/jboss/standalone/configuration/standalone.xml，将下面的ip地址改为本机ip或者 0.0.0.0  
      <interface name="public">  
            <inet-address value="${jboss.bind.address:10.0.0.31}"/>  
     </interface>  
5、以后台形式启动jboss:    nohup standalone.sh  &

![](https://img-blog.csdn.net/2018092216000760?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

目录结构： 
------

*   可执行文件：bin
*   网站主页目录：welcome-content
*   配置文件：standalone/configuration
*   日志文件目录：standalone/log
*   库文件目录：standalone/lib

### 相关文件： [Linux下Apache服务的部署和配置](https://blog.csdn.net/qq_36119192/article/details/82768277)

###                    [Linux中Nginx服务器的部署和配置](https://blog.csdn.net/qq_36119192/article/details/82782309)

本文相关包： https://pan.baidu.com/s/1iaTTIb9t3p60wiF\_CEJVcA  提取码: qp57