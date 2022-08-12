**目录**

[MongoDB](#t0)

[发展历史](#t1)

[特点](#t2)

[Windows下安装MongoDB](#t3)

[启动和连接](#t4)

[为MongoDB数据库设置账户密码登录](#t5)

[Linux下安装MongoDB](#t6)

[启动和连接](#t7)

[MongoDB的数据库](#t8)

[总结](#t9)

* * *

MongoDB
=======

[MongoDB](https://so.csdn.net/so/search?q=MongoDB&spm=1001.2101.3001.7020)是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。MongoDB 是一个介于关系型数据库和非关系型数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。官网：[https://www.mongodb.com/try#community](https://www.mongodb.com/try#community)

MongoDB在高负载的情况下，添加更多的节点，可以保证服务器性能。MongoDB 将数据存储为一个文档，[数据结构](https://so.csdn.net/so/search?q=%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84&spm=1001.2101.3001.7020)由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

![](https://img-blog.csdnimg.cn/img_convert/d0ac996b2e5b8355ac7a26ad146eea47.png)

发展历史
----

*   2007年10月，MongoDB由10gen团队所发展。2009年2月首度推出。
*   2012年05月23日，MongoDB2.1 开发分支发布了! 该版本采用全新架构，包含诸多增强。
*   2012年06月06日，MongoDB 2.0.6 发布，分布式文档数据库。
*   2013年04月23日，MongoDB 2.4.3 发布，此版本包括了一些性能优化，功能增强以及bug修复。
*   2013年08月20日，MongoDB 2.4.6 发布。
*   2013年11月01日，MongoDB 2.4.8 发布。
*   ……

特点
--

*   MongoDB 是一个面向文档存储的数据库，操作起来比较简单和容易。
*   你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。
*   你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
*   如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。
*   Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
*   MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
*   Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
*   Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。
*   Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。
*   GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。
*   MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。
*   MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
*   MongoDB安装简单。

Windows下安装MongoDB
-----------------

先去官网下载安装包：[https://www.mongodb.com/try/download](https://www.mongodb.com/try/download)

![](https://img-blog.csdnimg.cn/20201029143231270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201029144212296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下载链接为：[https://fastdl.mongodb.org/win32/mongodb-win32-x86\_64-2008plus-ssl-4.0.20-signed.msi](https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-4.0.20-signed.msi)

下载了.msi文件后，双击安装即可。我这里下载的是4.0.20版本的。

![](https://img-blog.csdnimg.cn/20201029144458429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20201029144519553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201029144555264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里取消勾选安装MongoDB Compass。这是图形化管理MongoDB的工具，我们先不装，后期需要再安装。

![](https://img-blog.csdnimg.cn/20201029144620231.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020102914464112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

安装完成后，安装目录如下，其中bin目录是执行文件目录。data目录是数据库目录。log目录是日志目录。

![](https://img-blog.csdnimg.cn/20201029155925748.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

默认的配置文件  mongod.cfg 在bin目录下

![](https://img-blog.csdnimg.cn/20201029160032340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 启动和连接

**启动**

到安装目录的bin目录下执行如下命令启动。也可以加各种参数启动，如：--auth

```
mongod.exe
```


![](https://img-blog.csdnimg.cn/20201029160107105.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 **本机连接**

如果你需要进入MongoDB后台管理，你需要先打开mongodb装目录的下的bin目录，然后执行 mongo.exe 文件，MongoDB Shell是MongoDB自带的交互式Javascript shell,用来对MongoDB进行操作和管理的交互式环境。当你进入mongoDB后台后，它默认会链接到 test 文档（数据库）：

```
mongo.exe
```


![](https://img-blog.csdnimg.cn/20201029154715986.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**其他机器连接**

MongoDB默认是只允许本机连接的，如果我们需要其他机器连接，需要修改bin目录下的mongod.cfg配置文件，将绑定的ip修改为 0.0.0.0 (默认是127.0.0.1)

![](https://img-blog.csdnimg.cn/20201029160232766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们可以使用 Robomongo 或者 MongoDB Compass 进行连接。默认是可以未授权访问的，即不使用账户密码进行认证即可连接。连接成功后，默认是有三个数据库：admin 、local 和 config 。

使用弱口令爆破工具爆破可以爆破出来，这里爆破出来密码为空说明可以进行未授权访问。

![](https://img-blog.csdnimg.cn/20201118223023461.png)

**使用Robomongo连接**

不勾选认证选项即可连接。

![](https://img-blog.csdnimg.cn/2020111822321760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![Robomongo截图](https://img-blog.csdnimg.cn/20201029162033312.png)

**使用MongoDB Compass进行连接**

```
mongodb://test:123456@192.168.10.129:27017/admin
```


![](https://img-blog.csdnimg.cn/20201119173412964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者

![](https://img-blog.csdnimg.cn/20201118223700616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![MongoDB Compass连接截图](https://img-blog.csdnimg.cn/20201029162107495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

为MongoDB数据库设置账户密码登录
-------------------

为MongoDB设置连接账号密码

```
切换到admin数据库      
use admin      
显示用户      
show users      
创建管理员用户      
db.createUser({user:"xie",pwd:"P@ss123",roles:["root"]})       
创建普通用户zhang，对test2数据库读写权限,对test3数据库只读权限      
db.createUser({user:"zhang",pwd:"P@ss123",roles:[{role:"readWrite",db:"test2"},{role:"read",db:"test3"}]})       1
认证      1
db.auth("xie","P@ss123")       1
删除用户      1
db.dropUser("zhang")       1
修改用户密码      1
db.updateUser("zhang",{pwd:"123456"})
```


![](https://img-blog.csdnimg.cn/20201118224307487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用 --auth 参数启动

![](https://img-blog.csdnimg.cn/20201118230332379.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

连接的时候进行认证

![](https://img-blog.csdnimg.cn/20201118231119423.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Linux下安装MongoDB
---------------

![](https://img-blog.csdnimg.cn/20201119150007444.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201119150946509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**安装Mongodb数据库**

```
#安装依赖包      
yum -y install libcurl openssl      
#下载      
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-4.0.21.tgz      
#解压      
tar -zxvf mongodb-linux-x86_64-rhel70-4.0.21.tgz      
#将解压包移动到/usr/local/ 目录下，重命名为 mongodb4      
mv mongodb-linux-x86_64-rhel70-4.0.21/ /usr/local/mongodb4      
#将可执行文件添加到 PATH 路径中      1
export PATH=/usr/local/mongodb4/bin:$PATH
```


 ![](https://img-blog.csdnimg.cn/20201119151227203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**创建数据库目录** 

默认情况下 MongoDB 启动后会初始化以下两个目录：

*   数据存储目录：/var/lib/mongodb
*   日志文件目录：/var/log/mongodb

我们在启动前可以先创建这两个目录并设置当前用户有读写权限

```
mkdir -p /var/lib/mongo      
mkdir -p /var/log/mongodb      
chown `whoami` /var/lib/mongo     # 设置权限      
chown `whoami` /var/log/mongodb   # 设置权限
```


### **启动和连接**

```
#启动      
mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork      
#查看启动日志      
tail -10f /var/log/mongodb/mongod.log      
#查看监听端口      
netstat -pantu | grep 27017
```


![](https://img-blog.csdnimg.cn/20201119151544296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**本地连接Mongodb数据库**

```
#执行命令连接      
mongo      
#查看当前数据库      
db
```


![](https://img-blog.csdnimg.cn/20201119151739789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**其他机器连接**

MongoDB默认是只允许本机连接的，如果我们需要其他机器连接，只需要启动的时候设置 --bind\_ip 0.0.0.0即可

```
mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork --bind_ip 0.0.0.0
```


![](https://img-blog.csdnimg.cn/2020111915242616.png)

然后我们可以使用 Robomongo 或者 MongoDB Compass 进行连接。默认是可以未授权访问的，即不使用账户密码进行认证即可连接。连接成功后，默认是有三个数据库：admin 、local 和 config 。

使用弱口令爆破工具爆破可以爆破出来，这里爆破出来密码为空说明可以进行未授权访问。

![](https://img-blog.csdnimg.cn/20201119152656303.png)

**使用Robomongo连接**

不勾选认证选项即可连接。

![](https://img-blog.csdnimg.cn/2020111822321760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![Robomongo截图](https://img-blog.csdnimg.cn/20201029162033312.png)

**使用MongoDB Compass进行连接**

![](https://img-blog.csdnimg.cn/2020111915283552.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020111915290372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201119153017669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork --bind_ip 0.0.0.0 --auth
```


![](https://img-blog.csdnimg.cn/20201119153116203.png)

![](https://img-blog.csdnimg.cn/20201119153252864.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

虽然设置了账号密码登录，但是用工具探测还是未授权访问，其实是这个工具探测有误

![](https://img-blog.csdnimg.cn/20201119153225730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

用工具连接发现不设置账号密码已经不能连接了，必须输入账号密码才能连接。

![](https://img-blog.csdnimg.cn/20201119153323269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201119153352379.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201119153609683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

MongoDB的数据库(文档)
===============

MongoDB默认情况下只有三个数据库(文档)。其中admin和local属于系统表，config是配置表。其实还有一个 test ，即连接后默认进入的数据库。

*   admin
*   config
*   local

![](https://img-blog.csdnimg.cn/20201119173819772.png)

![](https://img-blog.csdnimg.cn/20201119174139714.png)

**总结**
======

不管在windows还是linux下面，mongodb的启动参数和语法都是一样的。mongodb启动后默认是只允许监听本地的27017端口的。如果需要让其他机器连接，既可以启动的时候加 --bind\_ip  0.0.0.0参数启动，也可以通过修改配置文件来设置。windows下安装默认是有配置文件的，所以在windows环境下是通过修改配置文件来设置的。Linux下安装默认无配置文件，所以我是通过参数来设置的。至于设置用户名和密码，命令都是一样的。还有一个需要指出的是，使用超级弱口令工具来爆破Mongodb，无论存不存在未授权访问，都是提示未收权访问，这个要注意。

参考文章：[Windows 平台安装 MongoDB](https://www.runoob.com/mongodb/mongodb-window-install.html)

                  [Linux平台安装MongoDB](https://www.runoob.com/mongodb/mongodb-linux-install.html)