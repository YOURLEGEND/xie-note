**目录**

[SSRF](#t0 "SSRF")

[SSRF漏洞的挖掘](#t1 "SSRF漏洞的挖掘")

[SSRF漏洞利用](#t2 "SSRF漏洞利用")

[SSRF漏洞防御](#t3 "SSRF漏洞防御")

* * *

SSRF
----

**SSRF**(Server-Side Request Forgery，服务器端请求伪造)漏洞，是一种由攻击者构造请求，由服务器端发起请求的安全漏洞，本质上是属于信息泄露漏洞。

**SSRF漏洞原理**：很多web应用都提供了从其他的服务器上获取数据的功能。使用用户指定的URL，web应用可以获取图片，下载文件，读取文件内容等。这个功能如果被恶意使用，可以利用存在缺陷的web应用作为代理攻击远程和本地的服务器。一般情况下， SSRF攻击的目标是外网无法访问的内部系统，黑客可以利用SSRF漏洞获取内部系统的一些信息 。（ 正是因为它是由服务端发起的，所以它能够请求到与它相连而与外网隔离的内部系统 ）。SSRF 形成的原因大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制 。比如从指定URL地址获取网页文本内容，加载指定地址的图片，下载等等。

**SSRF主要攻击方式如下：**

攻击者想要访问主机B上的服务，但是由于存在防火墙或者主机B是属于内网主机等原因导致攻击者无法直接访问主机B。而主机A存在SSRF漏洞，这时攻击者可以借助主机A来发起SSRF攻击，通过主机A向主机B发起请求，从而获取主机B的一些信息。

![](https://img-blog.csdnimg.cn/20190119144832657.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   利用file协议读取本地文件
*   对服务器所在内网、本地进行端口扫描，获取一些服务的banner信息
*   攻击运行在内网或本地的应用程序
*   对内网web应用进行指纹识别，识别企业内部的资产信息
*   攻击内外网的web应用，主要是使用HTTP GET请求就可以实现的攻击

### SSRF漏洞的挖掘

*   通过分享功能：通过URL地址分享网页内容，在早期分享应用中，为了更好的提供用户体验，WEB应用在分享功能中，通常会获取目标URL地址网页内容中的<tilte></title>标签或者<meta name="description" content=“”/>标签中content的文本内容作为显示以提供更好的用户体验。例如人人网分享功能中：http://widget.renren.com/\*\*\*\*\*?resourceUrl=https://www.nsfocus.com，通过目标URL地址获取了title标签和相关文本内容。而如果在此功能中没有对目标地址的范围做过滤与限制则就存在着SSRF漏洞.
*   转码服务：通过URL地址把原地址的网页内容调优使其适合手机屏幕浏览：由于手机屏幕大小的关系，直接浏览网页内容的时候会造成许多不便，因此有些公司提供了转码功能，把网页内容通过相关手段转为适合手机屏幕浏览的样式。例如百度、腾讯、搜狗等公司都有提供在线转码服务。
*   在线翻译：通过URL地址翻译对应文本的内容。提供此功能的国内公司有百度、有道等。
*   图片加载与下载：通过URL地址加载或下载图片，图片加载远程图片地址此功能用到的地方很多，但大多都是比较隐秘，比如在有些公司中的加载自家图片服务器上的图片用于展示。（此处可能会有人有疑问，为什么加载图片服务器上的图片也会有问题，直接使用img标签不就好了？ 没错是这样，但是开发者为了有更好的用户体验通常对图片做些微小调整例如加水印、压缩等，所以就可能造成SSRF问题）。
*   图片、文章收藏功能：此处的图片、文章收藏中的文章收藏就类似于分享功能中获取URL地址中title以及文本的内容作为显示，目的还是为了更好的用户体验，而图片收藏就类似于功能四、图片加载。
*   未公开的api实现以及其他调用URL的功能：此处类似的功能有360提供的网站评分，以及有些网站通过api获取远程地址xml文件来加载内容.

我们如果在链接中看到了存在url类型的参数的话，就可以尝试是否存在SSRF漏洞，以下是常见的url中的关键字：

*   `share`
*   `wap`
*   `url`
*   `link`
*   `src`
*   `source`
*   `target`
*   `u`
*   `3g`
*   `display`
*   `sourceURl`
*   `imageURL`
*   `domain`

### SSRF漏洞利用

测试目标网站支持的伪协议

```
file:///      
dict://      
sftp://      
ldap://      
tftp://      
gopher://
```


现在服务器上有一个ssrf.php的页面，该页面的功能是获取URL参数，然后将URL的内容显示到网页页面上。

```
#ssrf.php      
<?php      
function curl($url){      
	$ch=curl_init();      
	curl_setopt($ch,CURLOPT_URL,$url);      
	curl_setopt($ch,CURLOPT_HEADER,0);      
	curl_exec($ch);      
	curl_close($ch);      
}      1
$url=$_GET['url'];      1
curl($url);      1
?>      1
#程序获取url参数，通过curl_init()初始化curl组件后，将参数URL带入curl_setopt($ch,CURLOPT_URL,$url),然后调用curl_exec请求该URL。由于服务器端会将banner信息返回给客户端，所以可根据banner判断主机是否存在某些服务。
```


我们访问该链接：[http://127.0.0.1/ssrf.php?url=http://127.0.0.1/test.php](http://127.0.0.1/ssrf.php?url=http://127.0.0.1/test.php "http://127.0.0.1/ssrf.php?url=http://127.0.0.1/test.php") ，它会将test.php页面显示

![](https://img-blog.csdnimg.cn/20181212224612381.png)

如果我们把url的参数换成 http://www.baidu.com ，页面则会返回百度的页面

![](https://img-blog.csdnimg.cn/20181212224719287.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是我们可以将URL参数换成内网的地址，则会泄露服务器内网的信息。将URL换成file://的形式，就可以读取本地文件。这和文件包含漏洞很类似！ 如下，我们可以读取服务器host文件的信息

![](https://img-blog.csdnimg.cn/20181212225213948.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### SSRF漏洞防御

*   限制请求的端口只能为web端口，只允许访问http和https的请求(禁掉file协议)
*   限制不能访问内网的ip，以防止对内网进行攻击
*   屏蔽返回的详细信息

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[Weblogic SSRF漏洞(CVE-2014-4210)](https://xie1997.blog.csdn.net/article/details/89785985 "Weblogic SSRF漏洞(CVE-2014-4210)")

                  [\[WEB安全\]SSRF中URL的伪协议](https://www.cnblogs.com/-mo-/p/11673190.html "[WEB安全]SSRF中URL的伪协议")

                  [一次“SSRF-->RCE”的艰难利用](https://mp.weixin.qq.com/s/kfYF157ux_VAOymU5l5RFA "一次“SSRF-->RCE”的艰难利用")

                  [XXE(XML外部实体注入)漏洞](https://blog.csdn.net/qq_36119192/article/details/84993356 "XXE(XML外部实体注入)漏洞")