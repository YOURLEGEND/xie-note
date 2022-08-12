**目录**

[Low](#t0)

[Medium](#t1)

[Middle](#t2)

[Impossible](#t3)

* * *

**命令执行漏洞的原理：**在操作系统中， &  、&& 、|  、 ||   都可以作为命令连接符使用，用户通过浏览器提交执行命令，由于服务器端没有针对执行函数做过滤，导致在没有指定绝对路径的情况下就执行命令

### Low

源代码：

```
<?php      
if( isset( $_POST[ 'Submit' ]  ) ) {      
    // Get input      
    $target = $_REQUEST[ 'ip' ];      
    // Determine OS and execute the ping command.      
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {      
        // Windows      
        $cmd = shell_exec( 'ping  ' . $target );      
    }      1
    else {      1
        // *nix      1
        $cmd = shell_exec( 'ping  -c 4 ' . $target );      1
    }      1
    // Feedback for the end user      1
    echo "<pre>{$cmd}</pre>";      1
}      1
?>
```


可以看到，low级别的代码接收了用户输入的ip，然后根据服务器是否是Windows NT系统，对目标ip进行不同的ping测试。但是这里对用户输入的ip并没有进行任何的过滤，所以我们可以进行命令执行漏洞

我们ping一下百度的ip地址看看，可以看到能ping通

![](https://img-blog.csdn.net/20181001171518176?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们尝试输入 61.135.169.125 & ipconfig  ,在操作系统中，"  &  、&& 、|  、 ||   "都可以作为命令连接符使用,我们在ping完后再执行ipconfig 命令查看ip信息

可以看到，成功执行。然后我们就可以继续执行我们的命令了。把ipconfig换成其他的系统命令

![](https://img-blog.csdn.net/20181001172031165?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

比如，我们执行 61.135.169.125 & net user xie /add ，尝试ping完后新建一个用户，哈哈，结果被360给拦截了。如果服务器没有装杀毒软件，我们的命令就可以执行了，我们就可以成功创建用户了。

![](https://img-blog.csdn.net/20181001172153933?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Medium

源代码：

```
<?php       
if( isset( $_POST[ 'Submit' ]  ) ) {      
    // Get input      
    $target = $_REQUEST[ 'ip' ];      
    // Set blacklist      
    $substitutions = array(      
        '&&' => '',      
        ';'  => '',      1
    );      1
    // Remove any of the charactars in the array (blacklist).      1
    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );       1
    // Determine OS and execute the ping command.      1
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {      1
        // Windows      1
        $cmd = shell_exec( 'ping  ' . $target );      1
    }      1
    else {      2
        // *nix      2
        $cmd = shell_exec( 'ping  -c 4 ' . $target );      2
    }      2
    // Feedback for the end user      2
    echo "<pre>{$cmd}</pre>";      2
}       2
?>
```


可以看到，medium级别的代码在low级别的代码上增加量了对 && 和 ；的过滤，但是这并没有什么软用。

我们根本就不用 && ，我们直接用 &

&&和&的区别在于，&&是执行完前面的命令然后执行后面的命令，&是不管前面的命令是否值执行，后面的都执行

可以看到，我们直接按照low级别的命令就可以绕过执行。

![](https://img-blog.csdn.net/20181001173008123?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Middle

源代码：

```
<?php       
if( isset( $_POST[ 'Submit' ]  ) ) {      
    // Get input      
    $target = trim($_REQUEST[ 'ip' ]);      
    // Set blacklist      
    $substitutions = array(      
        '&'  => '',      
        ';'  => '',      1
        '| ' => '',      1
        '-'  => '',      1
        '$'  => '',      1
        '('  => '',      1
        ')'  => '',      1
        '`'  => '',      1
        '||' => '',      1
    );      1
    // Remove any of the charactars in the array (blacklist).      1
    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );      2
    // Determine OS and execute the ping command.      2
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {      2
        // Windows      2
        $cmd = shell_exec( 'ping  ' . $target );      2
    }      2
    else {      2
        // *nix      2
        $cmd = shell_exec( 'ping  -c 4 ' . $target );      2
    }      2
    // Feedback for the end user      3
    echo "<pre>{$cmd}</pre>";      3
}      3
?>
```


可以看到，High级别的代码进行了黑名单过滤，把一些常见的命令连接符给过滤了。黑名单过滤看似安全，但是如果黑名单不全是话，是很容易进行绕过的。我们仔细看黑名单过滤中的| ,|后面还有一个空格，所以我们可以进行绕过，输入下面命令

61.135.169.125|ipconfig

可以看到，成功执行了！

![](https://img-blog.csdn.net/20181001173508992?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Impossible

源代码：

```
<?php      
if( isset( $_POST[ 'Submit' ]  ) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Get input      
    $target = $_REQUEST[ 'ip' ];      
    $target = stripslashes( $target );      
    // Split the IP into 4 octects      
    $octet = explode( ".", $target );      1
    // Check IF each octet is an integer      1
    if( ( is_numeric( $octet[0] ) ) && ( is_numeric( $octet[1] ) ) && ( is_numeric( $octet[2] ) ) && ( is_numeric( $octet[3] ) ) && ( sizeof( $octet ) == 4 ) ) {      1
        // If all 4 octets are int's put the IP back together.      1
        $target = $octet[0] . '.' . $octet[1] . '.' . $octet[2] . '.' . $octet[3];      1
        // Determine OS and execute the ping command.      1
        if( stristr( php_uname( 's' ), 'Windows NT' ) ) {      1
            // Windows      1
            $cmd = shell_exec( 'ping  ' . $target );      1
        }      1
        else {      2
            // *nix      2
            $cmd = shell_exec( 'ping  -c 4 ' . $target );      2
        }      2
        // Feedback for the end user      2
        echo "<pre>{$cmd}</pre>";      2
    }      2
    else {      2
        // Ops. Let the user name theres a mistake      2
        echo '<pre>ERROR: You have entered an invalid IP.</pre>';      2
    }      3
}      3
// Generate Anti-CSRF token      3
generateSessionToken();      3
?>
```


stripslashes(string) : 该函数会删除字符串string中的反斜杠，返回已剥离反斜杠的字符串。

explode(separator,string,limit): 该函数把字符串打散为数组，返回字符串的数组。参数separator规定在哪里分割字符串，参数string是要分割的字符串，可选参数limit规定所返回的数组元素的数目。

is\_numeric(string): 该检测string是否为数字或数字字符串，如果是返回TRUE，否则返回FALSE。

可以看到，Impossible级别的代码加入了Anti-CSRF token，同时对参数ip进行了严格的限制，只有诸如“数字.数字.数字.数字”的输入才会被接收执行，因此不存在命令注入漏洞。

### 相关文章：[命令执行和代码执行漏洞](https://blog.csdn.net/qq_36119192/article/details/82834063)