在HTTP的请求报文中，我们经常会看到Host字段，如下

```
GET /test/  HTTP/1.1      
Host: www.baidu.com      
Connection: keep-alive      
Upgrade-Insecure-Requests: 1      
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.101 Safari/537.36      
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8      
Referer: https://www.baidu.com/index.php      
Accept-Encoding: gzip, deflate, br      
Accept-Language: zh,zh-CN;q=0.8,ar;q=0.6,zh-TW;q=0.4      1
Cookie: BAIDUID=AE4D1DA6B2D6689BB8C557B3436893E3:FG=1;BIDUPSID=AE4D1DA6B2D6689BB8C557B3436893E3; PSTM=1501466227;
```


以前一直认为Host字段的作用在于指明HTTP报文发送的方向，访问的地址。但是实际上报文转发已经由网络层和传输层决定了IP和端口，http协议只是基于tcp协议的一个封装。

**那么，Host字段的作用是什么呢？**

通常，我们一台服务器上不止有一个web程序，很多情况下都有几个web程序，也就是说这几个web程序共用一个ip地址。当我们配置基于端口的虚拟主机，这是没有什么问题的，不同的web程序可以通过不同的端口来判断。但是，当我们配置基于域名的虚拟主机时，几个web程序共用一个ip地址(这里我们假设为192.168.1.1)和一个端口(这里我们假设为80端口)。那么，当客户端请求都发到了服务器的80端口，服务器怎么判断这条请求是发送给哪个web程序的呢？这时候，host头出现了，host头指定了应该由哪个网站或是web应用程序来处理一个传入的HTTP请求。web服务器使用该头部的值来将请求分派到指定的网站或web应用程序之上。

### Host头部攻击

这个Host头是在客户端的请求头中的，是由客户端提供的，也就是说客户端可以修改这个值。那么，只要是能被客户端修改的值，都是不可信任的。

当Host头部被修改为无效Host头会发生什么情况？大多数web服务器配置为将无法识别的Host头传送给列表中的第一台虚拟主机或者返回错误信息。因此，这使得把携带有任意Host头的请求发送到第一台虚拟主机上是可能的。

很多应用直接把Host值不做html编码便输出到了页面中，比如：

```
<link href=http://_SERVER["HTTP_HOST"]></link>    //触发一个get请求      
<form method=”POST”></form>                       //触发POST请求
```


当Host字段被修改为攻击者构造的恶意地址，这时，就会触发恶意请求。  
  
这样处理问题一般会很容易遭遇到两种常见的攻击：

*   缓存污染：缓存污染是指攻击者通过控制一个缓存系统来将一个恶意站点的页面返回给用户。
*   密码重置：密码重置这种攻击主要是因为发送给用户的内容是可以污染的，也就是说可以间接的劫持邮件发送内容。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Host头攻击技术解析及防御](http://netsecurity.51cto.com/art/201705/539759.htm "Host头攻击技术解析及防御")