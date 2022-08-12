**目录**

[CORS跨域资源共享](#t0 "CORS跨域资源共享")

[简单跨域请求](#t1 "简单跨域请求")

[非简单请求](#t2 "非简单请求")

[CORS的安全问题](#t3 "CORS的安全问题")

* * *

有关于浏览器的[同源策略](https://so.csdn.net/so/search?q=%E5%90%8C%E6%BA%90%E7%AD%96%E7%95%A5&spm=1001.2101.3001.7020)和如何跨域获取资源，传送门——> [浏览器同源策略和跨域的实现方法](https://blog.csdn.net/qq_36119192/article/details/82931250 "浏览器同源策略和跨域的实现方法")

**同源策略（SOP）限制了应用程序之间的信息共享，并且仅允许在托管应用程序的域内共享。这有效防止了系统机密信息的泄露。但与此同时，也带来了另外的问题。随着Web应用程序和微服务使用的日益增长，出于实用目的往往需要将信息从一个子域传递到另一个子域，或者在不同域之间进行传递（例如将访问令牌和会话标识符，传递给另一个应用程序）。**

为了允许跨域通信，开发人员必须使用不同的技术来绕过SOP并传递敏感信息，以至于现今也成为了一个棘手的安全问题。因此，为了在不影响应用程序安全状态的情况下实现信息共享，在HTML5中引入了跨源资源共享（CORS）。但问题也随之而来，许多人为了方便干脆直接使用默认的配置，或是由于缺乏对此的了解而导致了错误的配置。

CORS跨域资源共享
----------

跨域资源共享(CORS)是一种放宽同源策略的机制，它允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制，以使不同的网站可以跨域获取数据。

我们先来简单分析一下CORS跨域获取资源的过程：

CORS定义了两种跨域请求：**简单请求** 和 **非简单请求**。

*   简单跨域请求就是使用设定的请求方式请求数据
*   而非简单跨域请求则是在使用设定的请求方式请求数据之前，先发送一个OPTIONS预检请求，验证请求源是否为服务端允许源。只有"预检"通过后才会再发送一次请求用于数据传输。

当我们需要发送一个跨域请求的时候，浏览器会首先检查这个请求，如果它是简单跨域请求，浏览器就会立刻发送这个请求。如果它是非简单跨域请求，这时候浏览器不会马上发送这个请求，而是有一个跟服务器预检验证的过程。

### **简单跨域请求**

```
（1) 请求方法是以下三种方法之一：      
     HEAD      
     GET      
     POST      
（2）HTTP的头信息不超出以下几种字段：      
     Accept      
     Accept-Language      
     Content-Language      
     Last-Event-ID      1
     Content-Type：application/x-www-form-urlencoded、 multipart/form-data、text/plain
```


只有同时满足以上两个条件时，才是简单请求，否则为非简单请求。

浏览器判断该请求为简单请求时，会在Request Header中添加 **Origin** 字段，它表示我们的请求源。

如下，简单请求头：

![](https://img-blog.csdnimg.cn/20190117190807708.png)

CORS服务端会将该字段作为跨源标志。CORS接收到此次请求后， 首先会判断**Origin**是否在**允许源**（由服务端决定）范围之内。

如果Origin指定的源在许可范围内，即验证通过，服务端会在Response Header 添加下面几个字段

*   Access-Control-Allow-Origin：该字段是必须的。它的值要么是请求时`Origin`字段的值，要么是一个`*`，表示接受任意域名的请求。
*   Access-Control-Allow-Credentials：该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。当设置为`true时`，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。这个值也只能设为`true`，如果服务器不要浏览器发送Cookie，删除该字段即可
*   Access-Control-Expose-Headers：该字段可选。CORS请求时，`XMLHttpRequest`对象的`getResponseHeader()`方法只能拿到6个基本字段：`Cache-Control`、`Content-Language`、`Content-Type`、`Expires`、`Last-Modified`、`Pragma`。如果想拿到其他字段，就必须在`Access-Control-Expose-Headers`里面指定。

如下，CROS服务端的回应：

![](https://img-blog.csdnimg.cn/20190117191031867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果`Origin`指定的源不在许可范围内，即验证失败，服务器也会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息中的`Access-Control-Allow-Origin`字段不包含访问源，就知道出错了，从而抛出同源检测异常的错误。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

![](https://img-blog.csdnimg.cn/20190117190440185.png)

![](https://img-blog.csdnimg.cn/20190117190535606.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

上面说到，CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，指定`Access-Control-Allow-Credentials`字段。

```
Access-Control-Allow-Credentials:true
```


另一方面，开发者必须在AJAX请求中打开`withCredentials`属性 

```
var xhr = new XMLHttpRequest();      
xhr.withCredentials = true;
```


否则，即使服务器同意浏览器发送Cookie，浏览器也不会发送。或者，服务器要求设置Cookie，浏览器也不会处理。

但是，如果省略`withCredentials`设置，有的浏览器还是会一起发送Cookie。这时，可以显式关闭`withCredentials`

```
xhr.withCredentials = false;
```


需要注意的是，如果要发送Cookie，即Access-Control-Allow-Credentials:true时，`Access-Control-Allow-Origin`就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的`document.cookie`也无法读取服务器域名下的Cookie。

总结：简单请求只需要CORS服务端在接受到携带Origin字段的跨域请求后，在response header中添加Access-Control-Allow-Origin等字段给浏览器做同源判断。

### **非简单请求**

非简单请求是那种对服务器有特殊要求的请求，比如请求方法是`PUT`或`DELETE`，或者`Content-Type`字段的类型是`application/json`。非简单请求的CORS请求，会在正式通信之前，增加一次OPTIONS方法的预检请求。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的`XMLHttpRequest`请求，否则就报错。

下面简单分析一下非简单跨域请求的过程。浏览器先发送一个OPTIONS方法的预检请求。带有如下字段：

*   Origin: 在CORS中专门作为Origin信息供后端比对，表明来源域。
*   Access-Control-Request-Method:  接下来请求的方法，例如PUT、DELETE等等
*   Access-Control-Request-Headers: 自定义的头部，所有用setRequestHeader方法设置的头部都将会以逗号隔开的形式包含在这个头中

然后如果服务器配置了CORS，会返回对应对的字段，具体字段含义在返回结果是一并解释。

*   Access-Control-Allow-Origin:   允许进行跨域请求的域名
*   Access-Control-Allow-Methods:  允许进行跨域请求的方式
*   Access-Control-Allow-Headers:  允许进行跨区请求的头部

如下，OPTIONS预检的请求与相应

![](https://img-blog.csdnimg.cn/2019011719252215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后浏览器再根据服务器的返回值判断是否发送非简单请求。然后服务器处理完请求之后，会再返回结果中加上如下控制字段：

*   Access-Control-Allow-Origin: 允许跨域访问的域，可以是一个域的列表，也可以是通配符"\*"。这里要注意Origin规则只对域名有效，并不会对子目录有效。即http://foo.example/subdir/ 是无效的。但是不同子域名需要分开设置，这里的规则可以参照同源策略
*   Access-Control-Allow-Credentials: 是否允许请求带有验证信息
*   Access-Control-Expose-Headers: 允许脚本访问的返回头，请求成功后，脚本可以在XMLHttpRequest中访问这些头的信息
*   Access-Control-Max-Age: 缓存此次请求的秒数。在这个时间范围内，所有同类型的请求都将不再发送预检请求而是直接使用此次返回的头作为判断依据，非常有用，大幅优化请求次数
*   Access-Control-Allow-Methods: 允许使用的请求方法，以逗号隔开
*   Access-Control-Allow-Headers: 允许自定义的头部，以逗号隔开，大小写不敏感

然后浏览器通过返回结果的这些控制字段来决定是将结果开放给客户端脚本读取还是屏蔽掉。如果服务器没有配置CORS，返回结果没有控制字段，浏览器会屏蔽脚本对返回信息的读取，并报出同源检测异常的错误！

通过上面叙述，我们得知借助CORS我们不必关心发出的请求是否跨域，浏览器会帮我们处理这些事情，但是服务端需要支持CORS，服务端实现CORS的原理也很简单，在服务端完全可以对请求做上下文处理，已达到接口允许跨域访问的目的。

当然，也有很多第三方的CORS插件，例如：Spring MVC 在4.2以上版本也支持了CORS配置，这样，服务端也无需自己操心了！

CORS的安全问题
---------

CORS非常有用，可以共享许多内容，不过这里存在风险。因为它完全是一个盲目的协议，只是通过HTTP头来控制的。那么，CORS跨域资源共享漏洞是怎么发生的呢？由于程序员配置不当，Origin源不严格，从而造成跨域问题。

由以上可知，网站可以通过发送以下HTTP响应头部来启用CORS：

```
Access-Control-Allow-Origin: https://example.com
```


这样的话，就可以允许指定的源(http://example.com)来跨域请求服务器端的资源，并且服务器会响应。在默认情况下，发送跨域请求时不会携带cookie或其他凭据。因此，它不能用于窃取与用户相关的敏感信息（如CSRF令牌）。不过，网站服务器可以使用以下头部来启用凭据传输：

```
Access-Control-Allow-Credentials：true
```


这样浏览器在请求数据的时候就需要带上cookie。

实现对单个域的信任是非常容易的事情。不过，如果需要信任多个域的话，那该怎么办呢？根据相关规范的建议，只需列出相关的域，并用空格加以分隔即可，例如：

```
Access-Control-Allow-Origin：http://a.example.com  http://example.com
```


但是，没有哪个浏览器真正支持这一特性。

于是，我们可以通过使用通配符来信任所有子域，具体方法是：

```
Access-Control-Allow-Origin:  *.example.com
```


可是有一些偷懒的程序员，将Access-Control-Allow-Origin设置为允许来自所有域\*的跨域请求。

```
Access-Control-Allow-Origin：*
```


这样，所有的网站都可以对其进行跨域资源请求了，这是非常危险的。不过先别高兴的太早。其实这里在设计的时候有一个很好的限制。xmlhttprequest发送的请求需要使用 "withCredentials" 来带上Cookie，如果一个目标域设置成了允许任意域的跨域请求，这个请求又带着 Cookie 的话，这个请求是不合法的。（就是如果需要实现带 Cookie 的跨域请求，CORS服务端需要明确的配置允许来源的域，使用任意域的配置是不合法的）浏览器会屏蔽掉返回的结果。Javascript 就没法获取返回的数据了。这是CORS模型最后一道防线。假如没有这个限制的话，那么 Javascript 就可以获取返回数据中的 Cookie 和 CSRF Token，以及各种敏感数据。这个限制极大的降低了CORS的风险。

如下，这是不允许的：

```
Access-Control-Allow-Origin: *      
Access-Control-Allow-Credentials: true
```


这时，将在浏览器控制台中收到错误消息：当凭证标志为true时，无法在Access-Control-Allow-Origin中使用通配符(各个浏览器报错显示的不一样)。

![](https://img-blog.csdnimg.cn/20190117193421165.png)

**那么，CORS的漏洞到底出现在哪里呢？**

1：CORS服务端的 Access-Control-Allow-Origin 设置为了 \*，并且 Access-Control-Allow-Credentials 设置为false，这样任何网站都可以获取该服务端的任何数据了。

2：有一些网站的Access-Control-Allow-Origin他的设置并不是固定的，而是根据用户跨域请求数据的Origin来定的。这时，不管Access-Control-Allow-Credentials 设置为了 true 还是 false。任何网站都可以发起请求，并读取对这些请求的响应。意思就是任何一个网站都可以发送跨域请求来获得CORS服务端上的数据。

![](https://img-blog.csdnimg.cn/20190118101551395.png)

![](https://img-blog.csdnimg.cn/20190117195836216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下面的代码是通过AJAX来跨域请求获取服务端的数据

```
<html lang="en">      
<head>      
    <meta charset="UTF-8">      
    <title>Ajax</title>      
    <script type="text/javascript">      
        function foo(){      
            var xmlhttp=new XMLHttpRequest();      
	    	var url="http://127.0.0.1/1.txt";   //要跨域访问的资源      
            xmlhttp.open("POST",url,true);         1
			//xmlhttp.setRequestHeader('X-PINGOTHER','AAAA');     //自定义头部，如果这样的话，就属于非简单请求了      1
			//xmlhttp.setRequestHeader('Content-Type','text/xml');   //自定义头部，如果这样的话，就属于非简单请求了      1
            xmlhttp.send();      1
            xmlhttp.onreadystatechange=function()      1
            {      1
                if (xmlhttp.readyState==4 && xmlhttp.status==200)      1
                {      1
                    document.getElementById("my").innerHTML=xmlhttp.responseText;      1
                }      1
            }      2
        }      2
    </script>      2
</head>      2
<body>      2
    <button id="btn" onclick="foo()">确定</button>      2
    <p id="my">hello,word!</p>      2
</body>      2
</html>
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg) 

CORS漏洞的利用 
----------

[CORS（跨域资源共享）错误配置漏洞的高级利用](https://www.freebuf.com/articles/web/204023.html "CORS（跨域资源共享）错误配置漏洞的高级利用")

[三种对CORS错误配置的利用方法](https://www.freebuf.com/articles/web/200350.html "三种对CORS错误配置的利用方法")

参考文章：[对五家主流网站托管服务商进行的一次渗透测试](https://www.cnblogs.com/linuxsec/articles/10584677.html "对五家主流网站托管服务商进行的一次渗透测试")

                  [HTTP访问控制（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS "HTTP访问控制（CORS）")

                  [跨域——CORS详解](https://zhuanlan.zhihu.com/p/24411090 "跨域——CORS详解")

                  [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html "跨域资源共享 CORS 详解")

                  [如何利用CORS配置错误漏洞攻击比特币交易所](https://xz.aliyun.com/t/2702 "如何利用CORS配置错误漏洞攻击比特币交易所")