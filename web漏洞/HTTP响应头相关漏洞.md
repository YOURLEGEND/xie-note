**目录**

[X-Frame-Options(点击劫持)](#t0 "X-Frame-Options(点击劫持)")

[会话Cookie中缺少Http Only属性](#t1 "会话Cookie中缺少Http Only属性")

[检测到目标URL启用了不安全的HTTP方法](#t2 "检测到目标URL启用了不安全的HTTP方法")

[X-XSS-Protection](#t3 "X-XSS-Protection")

[X-Content-Type-Options](#t4 "X-Content-Type-Options")

[Strict-Transport-Security](#t5 "Strict-Transport-Security")

[X-Content-Security-Policy](#t6 "X-Content-Security-Policy")

[一些大公司使用这些安全响应头示例](#t7 "一些大公司使用这些安全响应头示例 ") 

* * *

X-Frame-Options(点击劫持)
---------------------

![](https://img-blog.csdnimg.cn/20190121211411994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

X-[Frame](https://so.csdn.net/so/search?q=Frame&spm=1001.2101.3001.7020)\-Options 响应头是用来给浏览器指示是否允许一个页面在<frame>, <iframe> 或者 <object> 中展现。网站可以使用此功能，来确保自己网站的内容没有被嵌到别人的网站中去，也从而避免了**点击劫持 (clickjacking)** 的攻击。

**X-Frame-Options** 响应头有三个可选的值：

*   DENY：页面不能被嵌入到任何 iframe 或 frame 中
*   SAMEORIGIN：页面只能被本站页面嵌入到 iframe 或 frame 中
*   ALLOW-FROM：页面允许嵌入到 iframe 或 frame 中

**在服务端设置的方式如下：**

Java代码:

```
response.addHeader("x-frame-options","SAMEORIGIN");
```


Nginx配置:

```
add_header X-Frame-Options SAMEORIGIN
```


Apache配置:

```
Header always append X-Frame-Options SAMEORIGIN
```


比如如果我们使用phpstudy搭建的环境，我们可以 **其他选项菜单—> php扩展及设置—>Apache模块**，勾选 **headers\_module** 模块，然后在Apache的配置文件 httpd.conf 中的空白行加入  **Header always append X-Frame-Options SAMEORIGIN** 即可！

**加之前**

![](https://img-blog.csdnimg.cn/20181125130026807.png)

**加之后**

![](https://img-blog.csdnimg.cn/20181125125848401.png)

**测试网站是否设置了X-Frame-Options**

将如下的代码中iframe中的链接换成待测网站的，保存为.html文件，本地打开。如果打开的页面中显示了待测网站，则说明没有设置，反之设置了。

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8" >      
    <title>点击劫持测试</title>      
</head>      
<body>      
    <iframe src="http://www.xxx.com/"  width="500"  height="500"  frameborder="10"> </iframe>      
</body>      1
</html>
```


**点击劫持危害**

如下的网页我们通过opacity控制iframe框的透明度

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title>CLICK JACK!!</title>      
    <style>      
        iframe{      
            width:900px;      
            height:250px;       1
            position: absolute;      1
            top:-195px;      1
            left:-740px;      1
            z-index:2;       1
            -moz-opacity:0.5;      1
            opacity: 0.5;  #通过该参数控制iframe框的透明度，设置为0时看不见      1
            filter:alpha(opacity=0.5);      1
        }      2
        button{      2
            position: absolute;      2
            top:10px;      2
            left:10px;      2
            z-index: 1;      2
            width: 120px;      2
        }      2
    </style>      2
</head>      2
<body>      3
    <iframe src="http://www.qidian.com" frameborder="0" scrolling="no"></iframe>      3
    <button>CLICK HERE!</button>      3
</body>      3
</html>
```


![](https://img-blog.csdnimg.cn/2019062809165742.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当我们把opacity参数设置为0.000001(设置为0时，虽然也不可见，但是不能点击到iframe框里的内容 )时，用户就看不到iframe框了，但是当他点击CLICK HERE按钮时，实际上是点击到iframe上的内容。

![](https://img-blog.csdnimg.cn/20190628091746670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击劫持和CSRF有异曲同工之妙，都是在用户不知情的情况下诱使用户点击完成一些动作。但是在CSRF攻击的过程中，如果出现用户交互的页面，则攻击可能会无法顺利完成。与之相反的是，点击劫持没有这个顾虑，它利用的就是与用户产生交互的页面。

 会话Cookie中缺少Http Only属性
-----------------------

![](https://img-blog.csdnimg.cn/20190121211451572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果会话的Cookie中不含有Http Only属性，那么注入站点的恶意脚本可能访问此 cookie，并窃取它的值。任何存储在会话令牌中的信息都可能被窃取，并在稍后用于身份盗窃或用户伪装，从而使黑客能够以该用户身份访问服务器。如果在Cookie中设置HttpOnly属性为true，兼容浏览器接收到HttpOnly cookie，那么客户端通过程序(JS脚本、Applet等)将无法读取到Cookie信息，这将有助于缓解跨站点脚本威胁。

```
//java示例      
HttpServletResponse response2 = (HttpServletResponse)response;      
response2.setHeader( "Set-Cookie", "name=value; HttpOnly");
```


检测到目标URL启用了不安全的HTTP方法
---------------------

![](https://img-blog.csdnimg.cn/20190121213708486.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Web服务器（以IIS为例）在没有任何设置时(即默认条件下)，使用OPTIONS命令，可以返回所有能够响应的HTTP方法，如OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK。

发送OPTIONS请求：

![](https://img-blog.csdnimg.cn/20190121214918558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

服务器响应可以使用的HTTP方法，见Allow部分。

![](https://img-blog.csdnimg.cn/2019012121495233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

检查原始测试响应的“Allow”头，并验证是否包含下列一个或多个不需要的选项：DELTE，SEARCE，COPY，MOVE，PROPFIND，PROPPATCH，MKCOL，LOCK，UNLOCK，PUT。

**响应头信息如下：**

```
HTTP/1.1 200 OK       
Server: Apache-Coyote/1.1       
Allow: GET, HEAD, POST, PUT, DELETE, OPTIONS       
Content-Length: 0       
Date: Mon, 25 Jul 2016 10:12:23 GMT
```


我们首先了解一下这几个方法的由来：HTTP1.0定义了三种请求方法： GET, POST 和 HEAD方法；HTTP1.1新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。  
WebDAV完全采用了HTTP1.1的方法，并且扩展了一些其他方法：

*   Options、Head、Trace：主要由应用程序来发现和跟踪服务器支持和网络行为
*   Get：检索文档
*   Put和Post：将文档提交到服务器
*   Delete：销毁资源或集合
*   Mkcol：创建集合
*   PropFind和PropPatch：针对资源和集合检索和设置属性
*   Copy和Move：管理命名空间上下文中的集合和资源
*   Lock和Unlock：改写保护

很显然上述操作明细可以对web服务器进行上传、修改、删除等操作，对服务造成威胁。虽然WebDAV有权限控制但是网上一搜还是一大堆的攻击方法，所以如果不需要这些方法还是建议直接屏蔽就好了。

所以，在不需要的情况下，建议关闭WebDAV。

X-XSS-Protection
----------------

顾名思义，这个响应头是用来防范[XSS](https://so.csdn.net/so/search?q=XSS&spm=1001.2101.3001.7020)的。现在主流浏览器都支持，并且默认都开启了XSS保护，用这个header可以关闭它。它有几种配置：

*   0：禁用XSS保护；
*   1：启用XSS保护；
*   1; mode=block：启用XSS保护，并在检查到XSS攻击时，停止渲染页面（例如IE8中，检查到攻击时，整个页面会被一个#替换）；

浏览器提供的XSS保护机制并不完美，但是开启后仍然可以提升攻击难度，总之没有特别的理由，不要关闭它

X-Content-Type-Options
----------------------

互联网上的资源有各种类型，通常浏览器会根据响应头的Content-Type字段来分辨它们的类型。例如："text/html"代表html文档，"image/png"是PNG图片，"text/css"是CSS样式文档。然而，有些资源的Content-Type是错的或者未定义。这时，某些浏览器会启用 MIME-sniffing 来猜测该资源的类型，解析内容并执行。

例如，我们即使给一个html文档指定Content-Type为"text/plain"，在IE8-中这个文档依然会被当做html来解析。利用浏览器的这个特性，攻击者甚至可以让原本应该解析为图片的请求被解析为JavaScript。通过下面这个响应头可以禁用浏览器的类型猜测行为：

```
X-Content-Type-Options: nosniff
```


这个响应头的值只能是nosniff，可用于IE8+和Chrome 

Strict-Transport-Security
-------------------------

HTTP Strice Transport Security，简称为HSTS。它允许一个HTTPS网站，要求浏览器总是通过HTTPS来访问它。现阶段，除了Chrome浏览器，Firefox4+，以及Firefox的NoScript扩展都支持这个响应头。

我们知道HTTPS相对于HTTP有更好的安全性，而很多HTTPS网站，也可以通过HTTP来访问。开发人员的失误或者用户主动输入地址，都有可能导致用户以HTTP访问网站，降低了安全性。一般，我们会通过Web Server发送301/302重定向来解决这个问题。现在有了HSTS，可以让浏览器帮你做这个跳转，省一次HTTP请求。另外，浏览器本地替换可以保证只会发送HTTPS请求，避免被劫持。

要使用HSTS，只需要在你的**HTTPS**网站响应头中，加入下面这行：

```
strict-transport-security: max-age=16070400; includeSubDomains
```


includeSubDomains是可选的，用来指定是否作用于子域名。支持HSTS的浏览器遇到这个响应头，会把当前网站加入HSTS列表，然后在max-age指定的秒数内，当前网站所有请求都会被重定向为https。即使用户主动输入`http://`或者不输入协议部分，都将重定向到`https://`地址。

Chrome内置了一个HSTS列表，默认包含Google、Paypal、Twitter、Linode等等服务。我们也可以在Chrome输入chrome://net-internals/#hsts，进入HSTS管理界面。在这个页面，你可以增加/删除/查询HSTS记录。例如，你想一直以https访问某网址，通过“add Domain”加上去就好了。

X-Content-Security-Policy
-------------------------

Content-Security-Policy，简称 CSP。顾名思义，这个规范与内容安全有关，主要是用来定义页面可以加载哪些资源，减少 XSS 的发生。

Chrome 扩展已经引入了 CSP，通过 manifest.json 中的 `content_security_policy` 字段来定义。一些现代浏览器也支持通过响应头来定义 CSP

要使用 CSP，只需要服务端加入类似这样的响应头就行了：

```
Content-Security-Policy: default-src 'self'
```


`default-src` 是 CSP 指令，多个指令之间用英文分号分割；`'self'` 是指令值，多个指令值用英文空格分割。

一些大公司使用这些安全响应头示例 
-----------------

**Google+**，使用了这几个本文提到的响应头

```
x-content-type-options: nosniff      
x-frame-options: SAMEORIGIN      
x-xss-protection: 1; mode=block
```


**Twitter**使用了这些：

```
strict-transport-security: max-age=631138519      
x-frame-options: SAMEORIGIN      
x-xss-protection: 1; mode=block
```


**PayPal**的：

```
X-Frame-Options: SAMEORIGIN      
Strict-Transport-Security: max-age=14400
```


**Facebook**则使用了这些（配置了详细的CSP，关闭了XSS保护）：

```
strict-transport-security: max-age=60      
x-content-type-options: nosniff      
x-frame-options: DENY      
x-xss-protection: 0      
content-security-policy: default-src *;script-src https://*.facebook.com http://*.facebook.com https://*.fbcdn.net http://*.fbcdn.net *.facebook.net *.google-analytics.com *.virtualearth.net *.google.com 127.0.0.1:* *.spotilocal.com:* chrome-extension://lifbcibllhkdhoafpjfnlhfpfgnpldfl 'unsafe-inline' 'unsafe-eval' https://*.akamaihd.net http://*.akamaihd.net;style-src * 'unsafe-inline';connect-src https://*.facebook.com http://*.facebook.com https://*.fbcdn.net http://*.fbcdn.net *.facebook.net *.spotilocal.com:* https://*.akamaihd.net ws://*.facebook.com:* http://*.akamaihd.net https://fb.scanandcleanlocal.com:*;
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[一些安全相关的HTTP响应头](https://imququ.com/post/web-security-and-response-header.html "一些安全相关的HTTP响应头")