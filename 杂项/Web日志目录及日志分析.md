**目录**

[Apache](#t0)

[Nginx](#t1)

[Tomcat](#t2)

[Web日志的分析](#t3)

* * *

![](https://img-blog.csdnimg.cn/20200101165757271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在很多时候，我们经常需要分析网站的日志，以此来查看网站运行的各种情况。比如说如果网站被攻击，我们可以通过查看日志来溯源攻击者。

### Apache

日志目录：/[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020)/logs/

![](https://img-blog.csdnimg.cn/20191107153206143.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

logs目录下有两个文件，一个是 access.log ，就是用户的访问日志。还有一个是 error.log，这个是apache运行时的错误日志。

![](https://img-blog.csdnimg.cn/20191107153350673.png)

### Nginx

日志目录：/[Nginx](https://so.csdn.net/so/search?q=Nginx&spm=1001.2101.3001.7020)/logs/

![](https://img-blog.csdnimg.cn/20191107153832409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

logs目录下有两个文件，一个是 access.log ，就是用户的访问日志。还有一个是 error.log，这个是apache运行时的错误日志。

![](https://img-blog.csdnimg.cn/20191107154002346.png)

### Tomcat

日志目录：/Tomcat/logs/

![](https://img-blog.csdnimg.cn/20191107180028138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**logs目录下有五类文件：**

*   **catalina.日期.log**：这个主要是记录tomcat启动时候的信息，类似于我们在eclipse中启动服务的时候在控制台看到的信息
*   **host-manager.日期.log：**这个是放tomcat的自带的manager项目的日志信息的，也没有看到有什么重要的日志信息
*   **localhost.日期.log：**这个类似于catalina日志，可是信息没有第一种全
*   **localhost\_access\_log.日期.txt：**这个是存放访问tomcat的请求的所有地址以及请求的路径、时间，请求协议以及返回码等信息(重要)
*   **manager.日志.log：**这个估计是manager项目专有的日志文件

![](https://img-blog.csdnimg.cn/20191107180341583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Web日志的分析

如下，我们分析日志中第8条的更个部分的含义

*   访问者ip：127.0.0.1
*   访问日期：07/Nov/2019:18:11:54 +0800
*   请求方式：GET
*   请求链接：/dvwa/phpinfo.php
*   请求参数：PHPE9568F34-D428-11d2-A769-00AA001ACF42
*   HTTP协议：HTTP/1.1
*   返回转态码：200
*   返回包的长度：2536
*   请求包的Referer：http://127.0.0.1/dvwa/phpinfo.php
*   访问者的浏览器信息：Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36

![](https://img-blog.csdnimg.cn/20191107181551126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)