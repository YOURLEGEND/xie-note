**目录**

[Nginx安装方式：](#t0)

[yum源安装](#t1)

[目录结构：](#t2)

[源码包安装](#t3)

[目录结构：](#%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84%EF%BC%9A)

[Nginx中支持PHP](#t4)

[Nginx中配置php对mysql数据库的支持](#t5)

[Nginx配置反向代理服务器](#t6)

[正常代理](#t7)

[根据不同端口进行转发](#t8) 

[负载均衡](#t9) 

[本地转发代理(基于不同后缀)](#t10) 

* * *

**Nginx**是一个高性能的HTTP和[反向代理](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86&spm=1001.2101.3001.7020)服务器，该程序由俄罗斯Rambler.ru 站点开发，Nginx因为性能稳定、低系统资源消耗而闻名。默认监听端口： tcp / 80

[nginx](https://so.csdn.net/so/search?q=nginx&spm=1001.2101.3001.7020)安装包官网下载地址：[http://nginx.org/en/download.html](http://nginx.org/en/download.html)

Nginx安装方式：
==========

**yum源安装**
----------

[yum](https://so.csdn.net/so/search?q=yum&spm=1001.2101.3001.7020)安装会比源码包编译安装简单的多，默认会安装许多模块，但缺点是如果以后想安装第三方模块那就没办法了。

但是默认的 yum 源很多都没有nginx包的，想安装nginx，我们可以在 /etc/yum.repos.d/ 下新建一个 nginx.repo 文件，然后加入下面的内容 （Redhat 7 和 Centos 7 中适用）

```
[nginx]      
name=nginx repo      
baseurl=http://nginx.org/packages/centos/7/$basearch/      
gpgcheck=0      
enabled=1       
//如果是Centos6的系统，把baseurl中的7换成6即可
```


然后  yum  -y  install  nginx   安装即可。

### 目录结构：

程序可执行文件： /usr/sbin/nginx  
主要配置文件：   /etc/nginx/conf.d/default.conf  
网页主页目录：/usr/share/nginx/html/  
日志文件目录：/var/log/nginx/  
控制服务： systemctl   start / stop / restart  / status        nginx

访问服务器，如果看到下面页面，说明nginx服务器搭建完成！

![](https://img-blog.csdnimg.cn/20190426110016900.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**源码包安装**
---------

*   源码安装之前先安装编译工具： yum  -y  install  gcc  gcc-c++  make
*   源码安装包含三个源码包：   nginx程序主包：nginx-xxx            nginx程序依赖包 ：pcre-xxx 和  zlib-xxx                            百度云网盘链接：链接： [https://pan.baidu.com/s/17msvtirIlbL2vXJaU-cp7Q](https://pan.baidu.com/s/17msvtirIlbL2vXJaU-cp7Q)   提取码: nq7w
*   解压，然后分别进入三个解压目录， ./configure   编译， make && make install  安装即可

### 目录结构：

安装完成后，nignx默认安装在  /usr/local/  目录下  
程序可执行文件： /usr/local/nginx/sbin/nginx  
配置文件：  /usr/local/nginx/conf/nginx.conf  
网页主页目录：/usr/loca/nginx/html/  
日志文件目录：/usr/local/nginx/log/

但是，当你去 /usr/local/nginx/sbin/ 下执行  ./nginx  命令运行程序时，有可能会报错

```
[root@Centos sbin]# ./nginx       
./nginx: error while loading shared libraries: libpcre.so.1: cannot open shared object file: No such file or directory
```


报这个错是因为我们的 libpcre 的相关库文件在  /usr/loca/lib/  下，而nginx默认读的是 /lib64/  下的。所以，我们只需要添加一个软链接即可解决：  ln   -s   /usr/local/lib/libpcre.\*    /lib64/

解决了上面的问题，我们就可以直接启动nignx了：我们可以使用这个命令启动nginx ： /usr/local/nginx/sbin/nginx  ，也可以去 /usr/local/nginx/sbin/   目录下直接 nginx 启动。但是这样未免太麻烦了。于是，我们也可以创建一个软链接： ln  -s    /usr/local/nginx/sbin/nginx     /usr/sbin/nginx       ，这样，无论我们到了哪个位置，都可以直接 nginx 启动。然后停止的话，可以用   nginx  -s  stop 。

![](https://img-blog.csdn.net/20180920140914301?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Nginx中支持PHP
===========

ningx中支持php的包为 php-fpm (php fastcgi process manager) , PHP-FPM提供了更好的PHP进程管理方式，可以有效控制内存和进程、可以平滑重载PHP配置。

安装php-fpm：  yum  -y install  php-fpm

然后修改nginx配置文件： /etc/nginx/conf.d/default.conf

```
location / {                                             //默认所有形式的后缀都由这里处理                                     
        root   /usr/share/nginx/html;                    //主页目录      
        index   index.php index.html index.htm;         //在这里加上 index.php      
   }       
// 把下面这几行的注释给取消，然后修改      
location ~ \.php$ {                                  //将后缀为 .php 的由这里处理      
        root           /usr/share/nginx/html;        //主页目录      
        fastcgi_pass   127.0.0.1:9000;      1
        fastcgi_index  index.php;      1
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;   //这里需要修改      1
        include        fastcgi_params;      1
    }
```


修改完nginx配置文件后，开启php-fpm： systemctl  start  php-fpm    
然后让nginx重新加载配置文件：    nginx    -s  reload

php的配置文件： /etc/php.ini    是用来控制php解析器

/etc/php-fpm.conf是控制php-fpm守护进程的

Nginx中配置php对mysql数据库的支持
=======================

yum  -y  install  php-mysql

Nginx配置反向代理服务器
==============

正常代理
----

![](https://img-blog.csdn.net/20180922194216978?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

将外界客户端发来的请求，转发给真正提供服务的服务器。

修改nginx配置文件：/etc/nginx/nginx.conf   ，在最后的 http{   } 中加入以下内容

```
// 下面这些代码的意思是将本机80号端口接受来的消息转发给 192.168.10.10 的 80 号端口      
upstream www.xie.com{                          //这里的域名随便填，但是要和下面的对应      
        server 192.168.10.10:80;            //真正提供服务的服务器的ip地址和端口      
    }         
server{      
listen  80;                                  // 监听80号端口发过来的消息      
location  /{      
         proxy_pass http://www.xie.com;      
         index  index.html index.php;      1
         }         1
}
```


根据不同端口进行转发 
-----------

###  

### ![](https://img-blog.csdn.net/20180922193849493?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
//这些代码的意思是将80端口接收到的信息转发给 192.168.10.10的80端口，而将接受到的 8080端口的信息转发给 192.168.10.20的8080端口      
upstream www.xie.com{      
        server 192.168.10.10:80;      
 }       
upstream www.xie2.com{      
        server 192.168.10.20:8080;      
 }       1
server{      1
listen 80;      1
location /{      1
    proxy_pass http://www.xie.com;      1
    index  index.html index.php;      1
         }      1
}       1
server{      1
listen 8080;      2
location /{      2
    proxy_pass http://www.xie2.com;      2
    index  index.html index.jsp;      2
        }      2
}
```


负载均衡 
-----

![](https://img-blog.csdn.net/20180922194417273?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
1：轮询：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除      
upstream www.xie.com{      
        server 192.168.10.10:80;      
        server 192.168.10.20:80;      
    }      
server{      
listen 80;      
location /{      
  proxy_pass http://www.xie.com;      1
  index  index.html index.php index.jsp;      1
  }      1
}       1
2：ip_hash：每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。      1
upstream www.xie.com{      1
        ip_hash;      1
        server 192.168.10.10:80;      1
        server 192.168.10.20:80;      1
    }      2
server{      2
listen 80;      2
location /{      2
  proxy_pass http://www.xie.com;      2
  index  index.html index.php index.jsp;      2
  }      2
}       2
3:weight:指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。      2
upstream www.xie.com{      3
        server 192.168.10.10:80 weight=10;      3
        server 192.168.10.20:80 weight=20;      3
    }      3
server{      3
listen 80;      3
location /{      3
  proxy_pass http://www.xie.com;      3
  index  index.html index.php index.jsp;      3
  }      3
}       4
4: fair : 按后端服务器的响应时间来分配请求，响应短的服务器优先分配      4
upstream www.xie.com{      4
        server 192.168.10.10:80 weight=10;      4
        server 192.168.10.20:80 weight=20;      4
        fair;      4
    }      4
server{      4
listen 80;      4
location /{      5
  proxy_pass http://www.xie.com;      5
  index  index.html index.php index.jsp;      5
  }      5
}
```


本地转发代理(基于不同后缀) 
---------------

以上的反向代理的工作原理，都是根据监听不同端口，然后将流量转发到真正服务器的不同端口。

还有一种反向代理是监听80端口，然后根据请求的页面的不同后缀(.php / .jsp / 等等)，来转发给不同的端口进行解析。

![](https://img-blog.csdnimg.cn/20190426100011713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
server {      
    listen       80;      
    server_name  localhost;      
    location / {      
        root   /usr/share/nginx/html;      
        index  index.php index.html index.htm;      
    }       
    location ~ \.php$ {              //当请求的后缀是以 .php结尾的话，将流量转发给本地的800端口      1
        proxy_pass   http://127.0.0.1:800;      1
    }      1
    location ~ \.jsp$ {            //当请求的后缀是以 .jsp结尾的话，将流量转发给本地的8080端口      1
        proxy_pass   http://127.0.0.1:8080;      1
    }      1
    location ~ \.(jpg|png)$ {     //当请求的后缀是以 .jpg或.png 结尾的话，则请求 /img 目录下      1
       root  /img;      1
    }      1
}
```


### 相关文章： [Linux下Apache服务的部署和配置](https://blog.csdn.net/qq_36119192/article/details/82768277)

###                    [Linux中Tomcat和Jboss的安装和部署](https://blog.csdn.net/qq_36119192/article/details/82797114)

###                    [LNMP环境搭建Wordpress博客](https://blog.csdn.net/qq_36119192/article/details/82891142)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中