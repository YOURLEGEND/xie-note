**目录**

[BloodHound](#t0 "BloodHound")

[BloodHound的安装](#t1 "BloodHound的安装")

[1：安装Neo4j数据库](#t2 "1：安装Neo4j数据库")

[2：安装BloodHound](#t3 "2：安装BloodHound")

[采集数据](#t4 "采集数据")

[导入数据](#t5 "导入数据")

[查询信息](#t6 "查询信息")

* * *

BloodHound
==========

BloodHound是一款域内免费的分析工具。BloodHound通过图与线的形式，将域内用户、计算机、组、会话、ACL之间的关系呈现出来。BloodHound使用图形理论，在活动目录环境中自动理清大部分人员之间的关系和防护。攻击者使用BloodHound可以快速、深入地了解活动目录中用户之间的关系。BloodHound可以在域内导出相关信息，将采集的数据导入本地[Neo4j](https://so.csdn.net/so/search?q=Neo4j&spm=1001.2101.3001.7020)数据库，并进行展示和分析。Neo4j是一款NoSQL图形数据库，它将结构化数据存储在网络内而不是表中。BloodHound正是利用Neo4j这种特性，通过合理的分析，直观地以节点空间的形式表达相关数据。

BloodHound的安装
-------------

我这里以64位的windows10系统为例

### 1：安装Neo4j数据库

Neo4j数据库的运行需要Java环境的支持，我这里已经安装好了Java环境。

![](https://img-blog.csdnimg.cn/20200131230538102.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

去Neo4j官网下载社区免费版：[Neo4j Download Center - Neo4j Graph Database Platform](https://neo4j.com/download-center/#community "Neo4j Download Center - Neo4j Graph Database Platform")

下载后，将安装包解压，然后打开命令行窗口，进入解压后的bin目录，输入命令：neo4j.bat console，启动Neo4j服务。

![](https://img-blog.csdnimg.cn/20200201003825690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

服务启动后，浏览器输入：http://127.0.0.1:7474/browser/ ，然后在打开的页面中输入用户名和密码即可。

Host默认是： bolt://127.0.0.1:7687 ，账号和密码默认是：neo4j

![](https://img-blog.csdnimg.cn/20200201003905203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击Connect，连接成功后，第一次会让我们修改密码，我这里将密码修改为123456。

![](https://img-blog.csdnimg.cn/20200201004023710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

充值完密码之后，就可以正常使用Neo4j了

![](https://img-blog.csdnimg.cn/20200201004234980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 2：安装BloodHound

下载BloodHound：[https://github.com/BloodHoundAD/BloodHound/releases/download/2.0.4/BloodHound-win32-x64.zip](https://github.com/BloodHoundAD/BloodHound/releases/download/2.0.4/BloodHound-win32-x64.zip "https://github.com/BloodHoundAD/BloodHound/releases/download/2.0.4/BloodHound-win32-x64.zip")

下载后解压，进入解压目录，找到BloodHound.exe，双击。这里输入我们上面的账号密码登录即可。

![](https://img-blog.csdnimg.cn/20200201004155253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

登录成功后的截图

![](https://img-blog.csdnimg.cn/20200201004632758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

界面右上角是菜单按钮和搜素栏。三个选项卡分别是数据库信息(Database Info)、节点信息(Node Info)和查询(Queries)。数据库信息选显卡中显示了所分析域的用户数量、计算机数量、组数量、会话数量、ACL数量、关系等信息，用户可以在此处执行基本的数据库管理操作，包括注销和切换数据库，以及清除当前加载的数据库。节点信息选项卡中显示了用户在图表中单击的节点的信息。查询选项卡中显示了BloodHound预置的查询请求和用户自己构建的查询请求。

界面左上角是设置区。

1.  第一个是刷新功能，BloodHound将重新计算并绘制当前显示的图形；
2.  第二个是导出图形功能，可以将当前绘制的图形导出为JSON或PNG文件；
3.  第三个是导入图形功能，可以导入JSON文件；
4.  第四个是上传数据功能，BloodHound将对上传的文件进行自动检测，然后获取CSV格式的数据；
5.  第五个是更改布局类型功能，用于在分层和强制定向图布局之间切换；
6.  第六个是设置功能，可以更改节点的折叠行为，以及在不同的细节模式之间切换。

采集数据
----

在使用BloodHound进行分析时，需要调用来自活动目录的三条信息：

1.  哪些用户登录了哪些机器？
2.  哪些用户拥有管理员权限？
3.  哪些用户和组属于哪些组？

BloodHound需要的这三条信息依赖于 PowerView.ps1脚本的BloodHound。BloodHound分为两部分：

1.  一是PowerShell采集器脚本(旧版本叫做BloodHound\_Old.ps1，新版本叫做 SharpHound.ps1)，
2.  二是可执行文件 SharpHound.exe。

执行以下命令用于收集信息，不需要管理员权限。

```
SharpHound.exe -c all
```


![](https://img-blog.csdnimg.cn/20200201102654606.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行完成后，会在当前目录生成 时间戳\_BloodHound.zip 的文件

导入数据
----

BloodHound支持通过界面上传单个文件和ZIP文件，最简单的方法是将压缩文件放到界面上节点信息选项卡以外的任意空白位置。文件导入后，即可查看内网的相关信息。

如图，该内网有7个用户、4台主机、41个组，2个sessions，411条ACLs、472个关系。

![](https://img-blog.csdnimg.cn/20200201103548349.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查询信息
----

进入查询模块，可以看到预定义的12个常用查询条件

![](https://img-blog.csdnimg.cn/20200201103948401.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

1.  Find all Domain Admins：查询所有域管理员
2.  Find Shortest Paths to Domain Admins：查找到达域管理员的最短路径
3.  Find Principals with DCSync Rights：查找具有DCSync权限的主体
4.  Users with Foreign Domain Group Membership：具有外部域组成员身份的用户
5.  Groups with Foreign Domain Group Membership：具有外部域组成员身份的组
6.  Map Domain Trusts：映射域信任
7.  Shortest Paths to Unconstrained Delegation Systems：无约束委托系统的最短路径
8.  Shortest Paths from Kerberoastable Users：Kerberoastable用户的最短路径
9.  Shortest Paths to Domain Admins from kerberoastable Users：从Kerberoathable用户到域管理员的最短路径
10.  Shortest Path from Owned Principals：拥有主体的最短路径
11.  Shortest Paths to Domain Admins from Owned Principals：从所属主体到域管理员的最短路径
12.  Shortest Paths to High Value Targets：高价值目标的最短路径

![](https://img-blog.csdnimg.cn/20200201105242196.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

本文所需工具，关注公众号：xie\_sec ，后台回复：BloodHound 即可获取下载链接

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-5343c7cd78b84d08a66ee736c1eb2cc0)[使用JDBC操作数据库](https://edu.csdn.net/skill/java/java-5343c7cd78b84d08a66ee736c1eb2cc0)[数据库操作](https://edu.csdn.net/skill/java/java-5343c7cd78b84d08a66ee736c1eb2cc0)44596 人正在系统学习中