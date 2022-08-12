**目录**

[Redis数据库的安装](#t0) 

[Redis数据库的基本操作](#t1)

[Redis数据库的备份与恢复](#t2)

[Redis的端口转发](#t3)

* * *

### Redis数据库的安装 

```
yum install gcc-c++            #安装gcc编译器      
wget  http://download.redis.io/releases/redis-4.0.2.tar.gz   #下载redis源码      
tar -zxvf redis-4.0.2.tar.gz   #解压      
cd  redis-4.0.2                #进入解压目录      
make && make install           #安装      
默认安装在 /usr/local/bin 下       
cp redis.conf  /usr/local/bin/redis.conf          #将配置文件复制到安装目录
```


第一次启动，我们需要对 redis.conf 配置文件进行一些修改

把下面这行给注释了，如果不注释的话，redis数据库则只允许本地 127.0.0.1 连接

![](https://img-blog.csdnimg.cn/20190816211519184.png)

redis 的默认端口为 6379，可以自己修改

![](https://img-blog.csdnimg.cn/20190816212206218.png)

保护模式修改为 no

![](https://img-blog.csdnimg.cn/20190816211613244.png)

Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程，则启动后会在后台运行。

![](https://img-blog.csdnimg.cn/20190816212129765.png)

在 requirepass foobared 下面加一行，为 redis设置密码为：123456 。

![](https://img-blog.csdnimg.cn/20190816211924118.png)

启动 redis 服务： ./redis-server  redis.conf   ，启动时加载配置文件

![](https://img-blog.csdnimg.cn/20190816212432572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

客户端连接：./redis-cli  -h  127.0.0.1 ，认证密码：auth  123456

![](https://img-blog.csdnimg.cn/20190816212615571.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Redis数据库的基本操作

```
info          #查看redis的信息和服务器信息      
flushall      #删除所有数据      
del key       #删除键为key的数据      
get key       #获得参数key的数据      
keys *        #查看所有的key
```


### Redis数据库的备份与恢复

使用 save 命令保存数据库，会生成备份文件 dump.rdb 文件 。如果要恢复数据库备份文件，只需要将 dump.rdb  移动到 Redis 安装目录并启动服务即可。获取 Redis 安装目录可以使用 config get dir  命令。

![](https://img-blog.csdnimg.cn/20190816213913303.png)

### Redis的端口转发

现在有这么一个场景，我们获得了Web服务器A的权限，并且知道了该服务器上存在Redis服务，但是只允许本地连接，现在我们需要连接到该服务器上的Redis服务。我们可以使用SSH做端口转发，将16379端口的流量转发到6379端口。所以我们只需要连接该服务器的16379端口即可。

![](https://img-blog.csdnimg.cn/20190816215953691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190816215333692.png)

![](https://img-blog.csdnimg.cn/20190816215644941.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190816215304601.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Redis数据库的使用](https://www.runoob.com/redis/redis-security.html)

                  [Redis未授权访问漏洞](https://blog.csdn.net/qq_36119192/article/details/84620648)