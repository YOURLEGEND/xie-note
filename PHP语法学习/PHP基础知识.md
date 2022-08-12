**目录**

[PHP](#t0)

[关闭php的报错](#t1)

[open\_basedir(限制PHP所能打开文件的位置)](#t2)

[PHP的文件上传漏洞](#t3)

[文件包含](#t4)

[转义](#t5)

[PDO](#t6)

[使用PDO连接MySQL数据库](#t7)

[使用普通方式连接MySQL数据库](#t8)

[使用PDO连接SQLServer数据库](#t9)

* * *

PHP
===

PHP（全称：PHP：Hypertext Preprocessor，即"PHP：超文本预处理器"）是一种通用开源脚本语言。PHP 文件可包含文本、HTML 、JavaScript 代码和 PHP 代码。PHP 代码在服务器上执行，结果以纯 HTML 形式返回给浏览器。PHP 文件的默认文件扩展名是 .php。

PHP的配置文件是 **php.ini** 。

**关闭php的报错**
------------

*   将 php.ini 中的 display\_errors = On 修改为 display\_errors = Off ，就没有报错提示。
*   在 php 脚本开头添加 error\_reporting(0) ;  也可以达到关闭报错的作用。
*   还可以在执行语句前面添加@关闭报错

![](https://img-blog.csdnimg.cn/20200229131122588.png)

**open\_basedir(限制PHP所能打开文件的位置)**
---------------------------------

open\_basedir 将php所能打开的文件限制在指定的目录中，包括文件本身。当程序要使用例如 fopen() 或 file\_get\_contents() 打开一个文件时，这个文件的位置将会被检查。当文件在指定的目录树之外，程序将拒绝打开。用open\_basedir指定的限制实际上是前缀，不是目录名。 也就是说 open\_basedir=/home/a 也会允许访问/home/b，如果要将访问限制为目录，请使用斜线结束路径名，例如:open\_basedir=”/home/fdipzone/”。如果要设置多个目录，window使用 ; 分隔目录，linux使用 : 分隔目录。 使用open\_basedir可以限制程序可操作的目录和文件，提高系统安全性。但会影响I/O性能导致系统执行变慢，因此需要根据具体需求，在安全与性能上做平衡。

*   在linux系统中，open\_basedir主要在php.ini和fastcgi.conf文件中配置，php.ini中配置如下：open\_basedir="指定目录"；fastcgi.conf中配置如下：fastcgi\_param PHP\_VALUE "open\_basedir=指定目录"；
*   在windows下，主要在php.ini和apache的http.conf文件中配置，http.conf中配置如下：php\_admin\_value open\_basedir "指定目录"。

**PHP的文件上传漏洞**
--------------

在 php < 5.3.4 版本中，存储文件时处理文件名的函数认为 0x00 是终止符。于是在存储文件的时候，当函数读到 0x00(%00) 时，会认为文件已经结束。

**文件包含**
--------

将 allow\_url\_include=On 改成 allow\_url\_include=Off，则不允许文件包含。

可以在 php.ini 中查看该参数的值

![](https://img-blog.csdnimg.cn/20200229130901866.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**转义**
------

**addslashes()** ：这个函数在预定义字符之前添加反斜杠 \\ 。预定义字符：  单引号 ' 、双引号 " 、反斜杠 \\ 、NULL。但是这个函数有一个特点就是虽然会添加反斜杠 \\ 进行转义，但是 \\ 并不会插入到数据库中。这个函数的功能和魔术引号完全相同，所以当打开了魔术引号时，不应使用这个函数。可以使用 get\_magic\_quotes\_gpc() 来检测是否已经转义。

**mysql\_real\_escape\_string()** ：这个函数用来转义sql语句中的特殊符号x00 、\\n  、\\r  、\\  、‘  、“ 、x1a。

**魔术引号**：当打开时，所有的单引号’ 、双引号" 、反斜杠\\  和 NULL 字符都会被自动加上一个反斜线来进行转义，这个和 addslashes() 函数的作用完全相同。所以，如果魔术引号打开了，就不要使用 addslashes() 函数了。一共有三个魔术引号指令。

*   magic\_quotes\_gpc 影响到 HTTP 请求数据（GET，POST 和 COOKIE）。不能在运行时改变。在 PHP 中默认值为 on。 参见 get\_magic\_quotes\_gpc()。如果 magic\_quotes\_gpc 关闭时返回 0，开启时返回 1。在 PHP 5.4.0 起将始终返回 0，因为这个魔术引号功能已经从 PHP 中移除了。
*   magic\_quotes\_runtime 如果打开的话，大部份从外部来源取得数据并返回的函数，包括从数据库和文本文件，所返回的数据都会被反斜线转义。该选项可在运行的时改变，在 PHP 中的默认值为 off。 参见 set\_magic\_quotes\_runtime() 和 get\_magic\_quotes\_runtime()。
*   magic\_quotes\_sybase (魔术引号开关)如果打开的话，将会使用单引号对单引号进行转义而非反斜线。此选项会完全覆盖 magic\_quotes\_gpc。如果同时打开两个选项的话，单引号将会被转义成 ''。而双引号、反斜线 和 NULL 字符将不会进行转义。 

可以在 php.ini 中看这几个参数是否开启

![](https://img-blog.csdnimg.cn/20200229130802284.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**PDO**
-------

PDO(Php Data Object)PHP数据对象，扩展为PHP访问数据库定义了一个轻量级的一致接口。PDO 提供了一个数据访问抽象层，这意味着，不管使用哪种数据库，都可以用相同的函数（方法）来查询和获取数据。PDO随PHP5.1发行，在PHP5.0的PECL扩展中也可以使用，无法运行于之前的PHP版本。添加各种数据库扩展，需要修改php.ini文件

```
extension=php_pdo.dll      
extension=php_pdo_pgsql.dll      
extension=php_pdo_sqlite.dll      
extension=php_pdo_sqlsrv_54_ts.dll      
extension=php_sqlsrv_54_ts.dll
```


### 使用PDO连接MySQL数据库

```
<?php      
	$dbms='mysql';     //数据库类型      
	$host='localhost'; //数据库主机名      
	$dbName='security';    //使用的数据库      
	$user='root';      //数据库连接用户名      
	$pass='root';          //对应的密码      
	$dsn="$dbms:host=$host;dbname=$dbName";       
try {      1
    $dbh = new PDO($dsn, $user, $pass); //初始化一个PDO对象      1
    echo "连接成功<br/>";      1
    // 执行SQL语句      1
    foreach ($dbh->query('SELECT * from users')as $row) {      1
        print_r($row); //你可以用 echo($GLOBAL); 来看到这些值      1
    }      1
    $dbh = null;      1
} catch (PDOException $e) {      1
    die ("Error!: " . $e->getMessage() . "<br/>");      1
}      2
//默认这个不是长连接，如果需要数据库长连接，需要最后加一个参数：array(PDO::ATTR_PERSISTENT => true) 变成这样：      2
// $db = new PDO($dsn, $user, $pass, array(PDO::ATTR_PERSISTENT => true));      2
?>
```


![](https://img-blog.csdnimg.cn/20200302161542849.png)

### 使用普通方式连接MySQL数据库

```
<?php      
	$con = mysql_connect("localhost","root","root");  #数据库连接      
	$select_db = mysql_select_db('security');      
	if (!$select_db) {      
    	die("不能连接到数据库！:\n" .  mysql_error());      
	}else{      
		echo "连接成功<br/>";      
	}      
	$sql = "select * from users where id=1 ";      1
	$res = mysql_query($sql);      1
	if (!$res) {      1
	    die("could get the res:\n" . mysql_error());      1
	}      1
	while ($row = mysql_fetch_assoc($res)) {      1
	    print_r($row);      1
	}      1
	mysql_close($con);   //关闭数据库连接      1
?>
```


![](https://img-blog.csdnimg.cn/2020030216170414.png)

### 使用PDO连接SQLServer数据库

注：连接SQLServer数据库之前需要添加sqlsrv扩展。

```
<?php      
	header("Content-type: text/html; charset=utf-8");      
	$dbName = "sqlsrv:Server=127.0.0.1;Database=test";      
	$dbUser = "sa";      
	$dbPassword = "root";      
try {      
	$dbh = new PDO($dbName, $dbUser, $dbPassword);      
	if($dbh) {      
		echo "连接成功";      1
	}else{      1
		echo "连接失败";      1
	}      1
	// 执行SQL语句      1
	foreach ($dbh->query('SELECT * from users where id=1')as $row) {      1
        print_r($row); //你可以用 echo($GLOBAL); 来看到这些值      1
    }      1
	$dbh=null;      1
}catch(PDOException $e) {      1
	die ("Error!: " . $e->getMessage() . "<br/>");      2
}      2
//默认这个不是长连接，如果需要数据库长连接，需要最后加一个参数：array(PDO::ATTR_PERSISTENT => true) 变成这样：      2
// $db = new PDO($dsn, $user, $pass, array(PDO::ATTR_PERSISTENT => true));      2
?>
```


![](https://img-blog.csdnimg.cn/20200302164224369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用普通方式连接PostgreSQL数据库

```
<?php      
  $host="host=127.0.0.1";      
  $port="port=5432";      
  $dbname="dbname=postgres";      
  $credentials="user=postgres password=root";      
  $db = pg_connect( "$host $port $dbname $credentials" );      
  if(!$db){      
   echo "连接失败!\n";      
  } else {      1
   echo "连接成功!\n";      1
  }      1
?>
```


![](https://img-blog.csdnimg.cn/2020030322015974.png)

 
-