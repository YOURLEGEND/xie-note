**目录**

[Amoeba](#t0)

[Amoeba读写分离的配置](#t1)

* * *

### Amoeba

**Amoeba(变形虫)** 项目，该开源框架于2008年开始发布一款 Amoeba for Mysql软件。 这个软件基于Java致力于MySQL的分布式数据库前端代理层，处于在应用和数据库之间，对客户端透明，它主要在应用层访问MySQL的时候充当SQL路由功能，解析应用传递过来的SQL语句，专注于分布式数据库代理层（Database Proxy）开发。

Amoeba 主要解决的以下几个问题：

*   数据切分后复杂数据源整合
*   提供数据切分规则并降低数据切分规则给数据库带来的影响
*   降低数据库与客户端的连接数
*   读写分离路由

![](https://img-blog.csdn.net/20180927163006390?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Amoeba读写分离的配置

如图拓扑，客户端要求也安装有mysql数据库，而装有Amoeba的服务器则只需安装Amoeba即可。本文主要是配置Amoeba读写分离的，所以两个数据库之间主从配置不讲。

![](https://img-blog.csdn.net/20180927193706178?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

中间服务器的配置

1、因为Amoeba的运行需要java环境，所以先安装Java环境。详情---> [Linux中配置java环境](https://blog.csdn.net/qq_36119192/article/details/82871807)

2、将 Amoeba压缩包解压到 /usr/loca/amoeba/ 下, 压缩包地址---> 链接: [Amoeba压缩包](https://pan.baidu.com/s/1JsV_eN_uTSZphGQ4-2agDw)  提取码: 5ppq

3、配置Amoeba环境变量，在 /etc/profile 结尾加入以下的内容

```
# # #### for amoeba      
AMOEBA_HOME=/usr/local/amoeba/      
export PATH=$PATH:$AMOEBA_HOME/bin
```


4、进入 /usr/local/amoeba/bin/ 底下，给所有文件755权限： chmod  755  \*

5、打开bin底下的amoeba文件，将这行的最后的128改成256。

![](https://img-blog.csdn.net/20180927200034337?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

6、测试： amoeba     如果显示 amoeba  start | stop 的话，证明依赖关系安装完成

7、修改 /usr/local/amoeba/conf/dbServer.xml 配置文件

![](https://img-blog.csdn.net/20180927203429497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180927203639579?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

8、修改 /usr/local/amoeba/conf/amoeba.xml 配置文件

![](https://img-blog.csdn.net/20180927204307859?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180927204522580?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

数据库服务器的配置

1、分别在两台数据库服务器上配置：

*   grant  all  on  \*.\*  to  amoeba @'192.168.10.30'  identified  by  'amoeba';
*   grant  all  on  \*.\*  to  test@'192.168.10.30'  identified  by  '123';

注：这两个用户，amoeba用户是amoeba服务连接mysql数据库专用的用户，而test用户则是用户访问后写入或读取数据库的登陆用户，两个要区别开来

2、在两台数据库服务器上分别建 stu 的数据库，然后建立不同的表，测试。

3、数据库服务器关闭防火墙和Selinux

客户端的配置

验证：在客户端连接amoeba服务器： mysql  -uamoeba  -pamoeba   -h192.168.10.30  -P8066

### 相关文章：[SQL语句](https://blog.csdn.net/qq_36119192/article/details/82875868)