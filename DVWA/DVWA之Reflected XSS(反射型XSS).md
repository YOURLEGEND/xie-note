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
header ("X-XSS-Protection: 0");      
// Is there any input?      
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {      
    // Feedback for end user      
    echo '<pre>Hello ' . $_GET[ 'name' ] . '</pre>';      
}      
?>
```


可以看到，low级别的代码只是判断了name参数是否为空，如果不为空的话就直接打印出来，并没有对name参数做任何的过滤和检查，存在非常明显的[XSS](https://so.csdn.net/so/search?q=XSS&spm=1001.2101.3001.7020)漏洞

我们输入 <script>alert('hack')</script>　直接就执行了我们的 js 代码

![](https://img-blog.csdn.net/2018100409255615?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看源代码可以看到，我们的js代码已经被插入到页面中了

![](https://img-blog.csdn.net/20181004092723222?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Medium
------

源代码：

```
<?php      
header ("X-XSS-Protection: 0")      
// Is there any input?      
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {      
    // Get input      
    $name = str_replace( '<script>', '', $_GET[ 'name' ] );      
    // Feedback for end user      
    echo "<pre>Hello ${name}</pre>";      
}      1
?>
```


<pre> </pre> 元素可定义预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。而文本也会呈现为等宽字体。 

可以看到，medium级别的代码只是在low级别上增加了对于<scripit>的过滤，并没有什么卵用，我们可以直接大写绕过

我们输入  <SCRIPT>alert('hack')</SCRIPT>  ，直接就执行了我们的js代码

![](https://img-blog.csdn.net/20181004093629491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看源代码，可以看到，我们的代码虽然输入的时候是大写(躲过了过滤)，但是最后输出的时候确是小写的

![](https://img-blog.csdn.net/20181004093813972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

High
----

源代码：

```
<?php      
header ("X-XSS-Protection: 0");      
// Is there any input?      
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {      
    // Get input      
    $name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $_GET[ 'name' ] );      
    // Feedback for end user      
    echo "<pre>Hello ${name}</pre>";      
}      1
?>
```


 可以看到，high级别的代码使用了正则表达式直接把 <\*s\*c\*r\*i\*p\*t  给过滤了，\* 代表一个或多个任意字符，i 代表不区分大小写。所以，我们的<script>标签在这里就不能用了。但是我们可以通过img、body等标签的事件或者iframe等标签的src注入恶意的js代码。

我们输入 <img  src=1  οnerrοr=alert('hack')>

上面输入的意思是，当图片显示错误时，然后执行 alert('hack') ，这里我们的src=1肯定显示错误啊，所以就执行 alert语句

可以看到，我们的代码执行了

![](https://img-blog.csdn.net/2018100409442689?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看源代码，可以看到，我们的代码插入到了页面中。

![](https://img-blog.csdn.net/20181004094734822?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Impossible
----------

源代码：

```
<?php      
// Is there any input?      
if( array_key_exists( "name", $_GET ) && $_GET[ 'name' ] != NULL ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );      
    // Get input      
    $name = htmlspecialchars( $_GET[ 'name' ] );      
    // Feedback for end user      
    echo "<pre>Hello ${name}</pre>";      1
}      1
// Generate Anti-CSRF token      1
generateSessionToken();      1
?>
```


htmlspecialchars(string)： 把预定义的字符 "<" （小于）、 ">" （大于）、& 、‘’、“” 转换为 HTML 实体，防止浏览器将其作为HTML元素

可以看出，impossible级别的代码先判断name是否为空，不为空的话然后验证其token，来防范CSRF攻击。然后再用htmlspecialchars函数将name中的预定义字符转换成html实体，这样就防止了我们填入标签

当我们输入 <script>alert('hack')</script> 时，因为 htmlspecialchars 函数会将 < 和 > 转换成html实体,并且${name}取的是$name的值，然后包围在<pre></pre>标签中被打印出来，所以我们插入的语句并不会被执行。

![](https://img-blog.csdn.net/20181004085217323?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们可以查看源代码，表单提交的过程中，把我们的user\_token也一并提交了，来和服务器端的session\_token做验证，防止CSRF攻击。我们输入的代码，直接被当成html文本给打印出来了，并不会被当成js脚本执行

![](https://img-blog.csdn.net/20181004085924874?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 相关文章： [XSS(跨站脚本攻击)漏洞详解](https://blog.csdn.net/qq_36119192/article/details/82469035)

###                    [DVWA之DOM XSS(DOM型跨站脚本攻击)](https://blog.csdn.net/qq_36119192/article/details/82932557)

###                    [DVWA之Stored XSS(存储型XSS)](https://blog.csdn.net/qq_36119192/article/details/82935895)