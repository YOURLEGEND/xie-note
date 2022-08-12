**目录**

[Low](#t0)

[Medium](#t1)

[High](#t2)

[Impossible](#t3)

* * *

Low
---

源代码：

```
<?php      
if( isset( $_POST[ 'btnSign' ] ) ) {      
    // Get input      
    $message = trim( $_POST[ 'mtxMessage' ] );      
    $name    = trim( $_POST[ 'txtName' ] );      
    // Sanitize message input      
    $message = stripslashes( $message );      
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    // Sanitize name input      1
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    // Update database      1
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";      1
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
    //mysql_close();      1
}      1
?>
```


trim(string,charlist) ： 移除string字符两侧的预定义字符，预定义字符包括\\t 、 \\n 、\\x0B 、\\r以及空格，可选参数charlist支持添加额外需要删除的字符

stripslashes(string)： 去除掉string字符的反斜杠＼

mysqli\_real\_escape\_string(string,connection) ：函数会对字符串string中的特殊符号（\\x00，\\n，\\r，\\，‘，“，\\x1a）进行转义。

$GLOBALS ：引用全局作用域中可用的全部变量。$GLOBALS 这种全局变量用于在 PHP 脚本中的任意位置访问全局变量（从函数或方法中均可）。PHP 在名为 $GLOBALS\[index\] 的数组中存储了所有全局变量。变量的名字就是数组的键。

可以看出，low级别的代码对我们输入的message和name并没有进行[XSS](https://so.csdn.net/so/search?q=XSS&spm=1001.2101.3001.7020)过滤，而且数据存储在数据库中，存在比较明显的存储型XSS漏洞

我们输入 1 和 <script>alert('hack')</script> ，可以看到，我们的js代码立即就执行了

![](https://img-blog.csdn.net/20181004103452979?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181004103354402?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看数据库，可以看到我们的js代表插入进去了

![](https://img-blog.csdn.net/20181004103738455?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看源代码，可以看到，Message位置显示的是我们的js代码，因为这里显示的数据是调用数据库里的数据

![](https://img-blog.csdn.net/20181004103626908?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Medium
------

源代码：

```
<?php      
if( isset( $_POST[ 'btnSign' ] ) ) {      
    // Get input      
    $message = trim( $_POST[ 'mtxMessage' ] );      
    $name    = trim( $_POST[ 'txtName' ] );      
    // Sanitize message input      
    $message = strip_tags( addslashes( $message ) );      
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    $message = htmlspecialchars( $message );      1
    // Sanitize name input      1
    $name = str_replace( '<script>', '', $name );      1
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    // Update database      1
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";      1
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
    //mysql_close();      1
}      1
?>
```


addslashes(string) ：函数返回在预定义字符之前添加反斜杠的字符串，预定义字符 ' 、" 、\\ 、NULL

strip\_tags(string) ：函数剥去string字符串中的 HTML、XML 以及 PHP 的标签

htmlspecialchars(string)： 把预定义的字符 "<" （小于）、 ">" （大于）、& 、‘’、“” 转换为 HTML 实体，防止浏览器将其作为HTML元素

当我们再次输入1 和 <script>alert('hack')</script> ，strip\_tags函数把<script>标签给剥除了，addslashes函数把 '  转义成了  \\'

![](https://img-blog.csdn.net/20181004110641848?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

虽然 Message 参数把所有的XSS都给过滤了，但是name参数只是过滤了<script>标签而已，我们依然可以在name参数进行注入

可是发现name参数对长度有限制，最大长度是10

![](https://img-blog.csdn.net/20181004111636956?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以我们想到了抓包，然后进行篡改，我们输入如下的，然后抓包

![](https://img-blog.csdn.net/20181004111758136?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如下框中的数据就是 <script>al 经过URL编码后的数据

![](https://img-blog.csdn.net/20181004111841842?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们需要将其修改为 <SCRIPT>alert('hack')</SCRIPT> 经过URL编码后的数据

![](https://img-blog.csdn.net/20181004112017475?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

提交后，就可以看到弹出此框了，说明我们的js代码执行了

![](https://img-blog.csdn.net/20181004111440190?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看数据库，可以看到我们的代码插入进数据库了。

![](https://img-blog.csdn.net/20181004112636531?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看页面源代码，可以看到name位置插入了我们的js代码

![](https://img-blog.csdn.net/2018100411251331?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

High
----

源代码：

```
<?php      
if( isset( $_POST[ 'btnSign' ] ) ) {      
    // Get input      
    $message = trim( $_POST[ 'mtxMessage' ] );      
    $name    = trim( $_POST[ 'txtName' ] );      
    // Sanitize message input      
    $message = strip_tags( addslashes( $message ) );      
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      
    $message = htmlspecialchars( $message );      1
    // Sanitize name input      1
    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $name );      1
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    // Update database      1
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";      1
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );      1
    //mysql_close();      1
}      1
?>
```


可以看到，high级别只是在medium级别上，name参数用了正则表达式进行过滤而已，我们仍然可以在name参数做手脚，抓包，然后改包，只不过这次改成 <img src=1  οnerrοr=alert('hack')>

我们输入如下的

![](https://img-blog.csdn.net/20181004113115561?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后将  <img src=1  οnerrοr=alert('hack')> 进行URL编码

![](https://img-blog.csdn.net/20181004150203538?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，我们的js代码执行了

![](https://img-blog.csdn.net/20181004113326734?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看数据库，我们的代码被插入进数据库了

![](https://img-blog.csdn.net/2018100411334255?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看源代码，可以看到name位置插入了我们的img标签

![](https://img-blog.csdn.net/20181004113454301?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Impossible
----------

源代码：

```
<?php      
if( isset( $_POST[ 'btnSign' ] ) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Get input      
    $message = trim( $_POST[ 'mtxMessage' ] );      
    $name    = trim( $_POST[ 'txtName' ] );      
    // Sanitize message input      
    $message = stripslashes( $message );      1
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    $message = htmlspecialchars( $message );      1
    // Sanitize name input      1
    $name = stripslashes( $name );      1
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));      1
    $name = htmlspecialchars( $name );      1
    // Update database      1
    $data = $db->prepare( 'INSERT INTO guestbook ( comment, name ) VALUES ( :message, :name );' );      1
    $data->bindParam( ':message', $message, PDO::PARAM_STR );      1
    $data->bindParam( ':name', $name, PDO::PARAM_STR );      2
    $data->execute();      2
}      2
// Generate Anti-CSRF token      2
generateSessionToken();      2
?>
```


可以看到，这次impossible在high级别的基础上对name参数也进行了更严格的过滤，导致name参数也无法进行XSS攻击。而且使用了Anti-CSRF token防止CSRF攻击，完全杜绝了XSS漏洞和CSRF漏洞。

### 相关文章： [XSS(跨站脚本攻击)漏洞详解](https://blog.csdn.net/qq_36119192/article/details/82469035)

###                    [DVWA之DOM XSS(DOM型跨站脚本攻击)](https://blog.csdn.net/qq_36119192/article/details/82932557)

###                    [DVWA之Reflected XSS(反射型XSS)](https://blog.csdn.net/qq_36119192/article/details/82935440)