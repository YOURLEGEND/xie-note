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
# No protections, anything goes      
?>
```


从源代码可以看出，这里low级别的代码没有任何的保护性措施！

页面本意是叫我们选择默认的语言，但是对default参数没有进行任何的过滤

![](https://img-blog.csdn.net/20181003165918431?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以我们可以构造XSS代码，访问链接：

[http://127.0.0.1/vulnerabilities/xss\_d/?default=](http://127.0.0.1/vulnerabilities/xss_d/?default=%3Cscript%3Ealert%28%27hack%27%29%3C/script%3E)<script>alert('hack')</script>

可以看到，我们的script脚本成功执行了

![](https://img-blog.csdn.net/20181003170043643?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们查看源代码，可以看到，我们的脚本插入到代码中，所以执行了

![](https://img-blog.csdn.net/20181003195350979?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Medium
------

源代码：

```
<?php      
// Is there any input?      
if ( array_key_exists( "default", $_GET ) && !is_null ($_GET[ 'default' ]) ) {      
    $default = $_GET['default'];      
    # Do not allow script tags (不区分大小写)      
    if (stripos ($default, "<script") !== false) {      
        header ("location: ?default=English");      
        exit;      
    }      1
}      1
?>
```


可以看到，medium级别的代码先检查了default参数是否为空，如果不为空则将default等于获取到的default值。这里还使用了stripos 用于检测default值中是否有 <script  ，如果有的话，则将 default=English 。

很明显，这里过滤了 <script  (不区分大小写)，那么我们可以使用<img  src=1  οnerrοr=('hack')>

但是当我们访问URL：

[http://127.0.0.1/vulnerabilities/xss\_d/?default=<img src=1 οnerrοr=alert('hack')>](http://127.0.0.1/vulnerabilities/xss_d/?default=%3Cimg%20src=1%20οnerrοr=alert%28%27hack%27%29%3E) 

此时并没有弹出任何页面

![](https://img-blog.csdn.net/20181003194359149?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们查看网页源代码，发现我们的语句被插入到了value值中，但是并没有插入到option标签的值中，所以img标签并没有发起任何作用。

![](https://img-blog.csdn.net/2018100319454019?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以我们得先闭合前面的标签，我们构造语句闭合option标签：

 <option value='     " + lang + "      '>  "  \+ decodeURI(lang) +  "  </option>

所以，我们构造该链接：

[http://127.0.0.1/vulnerabilities/xss\_d/?default=></option><img src=1 οnerrοr=alert('hack')>](http://127.0.0.1/vulnerabilities/xss_d/?default=%3Cimg%20src=1%20οnerrοr=alert%28%27hack%27%29%3E) 

![](https://img-blog.csdn.net/20181003201026230?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

但是我们的语句并没有执行，于是我们查看源代码，发现我们的语句中只有 > 被插入到了option标签的值中，因为</option>闭合了option标签，所以img标签并没有插入

![](https://img-blog.csdn.net/20181003201110465?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

于是我们继续构造语句去闭合select标签，这下我们的img标签就是独立的一条语句了

我们构造该链接：

[http://127.0.0.1/vulnerabilities/xss\_d/?default= ></option></select><img src=1 οnerrοr=alert('hack')>](http://127.0.0.1/vulnerabilities/xss_d/?default=%3E%3C/option%3E%3C/select%3E%3Cimg%20src=1%20οnerrοr=alert%28%27hack%27%29%3E)

可以看到，我们的语句成功执行了

![](https://img-blog.csdn.net/2018100320125644?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们查看源代码，可以看到，我们的语句已经插入到页面中了

![](https://img-blog.csdn.net/20181003201427518?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

High
----

源代码： 

```
<?php      
// Is there any input?      
if ( array_key_exists( "default", $_GET ) && !is_null ($_GET[ 'default' ]) ) {      
    # White list the allowable languages      
    switch ($_GET['default']) {      
        case "French":      
        case "English":      
        case "German":      
        case "Spanish":      1
            # ok      1
            break;      1
        default:      1
            header ("location: ?default=English");      1
            exit;      1
    }      1
}      1
?>
```


这里high级别的代码先判断defalut值是否为空，如果不为空的话，再用switch语句进行匹配，如果匹配成功，则插入case字段的相应值，如果不匹配，则插入的是默认的值。这样的话，我们的语句就没有可能插入到页面中了。目前我也没有找到好的方法进行XSS注入。

Impossible
----------

源代码：

```
<?php      
# Don't need to do anything, protction handled on the client side      
?>
```


我们可以看到，impossible级别的代码没有任何东西，注释写的是保护的代码在客户端的里面

于是我们尝试访问链接

[http://127.0.0.1/vulnerabilities/xss\_d/?default=<script>alert('hack')</script>](http://127.0.0.1/vulnerabilities/xss_d/?default=)

发现页面并没有弹出任何东西，而且语言框内的值是我们输入的参数的经过URL编码后的数据

![](https://img-blog.csdn.net/20181003212031133?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们查看源代码，发现这里对我们输入的参数并没有进行URL解码，所以我们输入的任何参数都是经过URL编码，然后直接赋值给option标签。所以，就不存在XSS漏洞了。

![](https://img-blog.csdn.net/20181003212250597?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

###  相关文章： [XSS(跨站脚本攻击)漏洞详解](https://blog.csdn.net/qq_36119192/article/details/82469035)

###                     [DVWA之Reflected XSS(反射型XSS)](https://blog.csdn.net/qq_36119192/article/details/82935440#High)

###                     [DVWA之Stored XSS(存储型XSS)](https://blog.csdn.net/qq_36119192/article/details/82935895)