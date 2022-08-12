**目录**

[Cookie](#t0)

[Session认证机制](#t1)

[Session的一些安全配置](#t2)

[Token认证机制](#t3)

[Token预防CSRF](#Session%E7%9A%84%E4%B8%80%E4%BA%9B%E5%AE%89%E5%85%A8%E9%85%8D%E7%BD%AE)

[Session认证和Token认证的区别](#t4)

* * *

> 前言：HTTP是一种无状态的协议，为了分辨链接是谁发起的，需要浏览器自己去解决这个问题。不然有些情况下即使是打开同一个网站的不同页面也都要重新登录。而Cookie、[Session](https://so.csdn.net/so/search?q=Session&spm=1001.2101.3001.7020)和Token就是为了解决这个问题而提出来的两个机制

用户通过浏览器登录一个网站，在该浏览器内打开网站其他页面时，不需要重新登录。而HTTP是无状态的协议，那么网站后端是如何判断用户已经登陆了呢？不同的网站，判断用户登录状态的方法都不一样。有的网站是通过session来验证用户的登录状态，有的网站是通过token来验证用户的登录状态，也有的网站是通过其他的方式来判断。本文讲了两个比较常用的方法，利用session或token来验证用户登录状态。

Cookie
------

**Cookie**：cookie是服务器发送给客户端的用于验证某一会话信息的数据，cookie中有很多字段。不同网站Cookie中字段是不一样的，是由服务器端设置的。Cookie中常放入session\_id 或者 token 用来验证会话的登录状态。

Cookie为什么能验证登录状态？那是因为cookie中放入了session\_id 值或者 token 值！

**cookie的分类：**

*   **Session Cookie**：key, value形式，过期时间可设置，如不设，则浏览器关掉就消失了，存储在内存当中，否则就按设置的时间来存储在硬盘上的，过期后自动清除
*   **Permenent Cookie**：Cookie的主要内容包括：名字，值，过期时间，路径和域等等

**Session Cookie:** 我们打开一个浏览器访问某个网站，该网站服务器就会返回一个Session Cookie，当我们访问该网站下其他页面时，用该Cookie验证我们的身份。所以，我们不需要每个页面都登录。但是，当我们关闭浏览器重新访问该网站时，需要重新登录获取浏览器返回的Cookie。Session Cookie在访问一个网站的过程中，一般是不变化的，有时也会变化，比如，切换不同的权限时，Cookie值会变化。

如下图，是Session Cookie的生成和作用

![](https://img-blog.csdn.net/20181003093337497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在整个会话过程中，cookie主要的值是不变化的，某些值会变化。如图，是DVWA不同等级之间用户的Session cooke。

![](https://img-blog.csdn.net/20181003103108246?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**Permenent Cookie** 是保存在浏览器客户端上存储用户登录信息的数据，Permenent  Cookie是由服务器端生成，然后发送给User-Agent（一般是浏览器），浏览器会将Cookie保存到某个目录下的文本文件内，下次请求同一网站时就发送该Cookie给服务器（前提是浏览器设置为启用cookie，大部分浏览器默认都是开启了cookie）。

例如，下面的截图是保存的baidu.com域名下的 Permenent Cookie。

![](https://img-blog.csdn.net/20181001204205954?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Session认证机制
-----------

**Session**：session是保存在服务器端的经过加密的存储特定用户会话所需的属性及配置信息的数据。当我们打开浏览器访问某网站时，session建立，只要浏览器不关闭(也有时间限制，可以自己设置超时时间)，这个网站就可以记录用户的状态，当浏览器关闭时，session结束。

*   浏览器第一次发送请求时，服务器自动生成了Session(用户会话所需的属性及配置信息)，并且生成了Session ID来唯一标识这个Session，并将其通过响应发送到浏览器。浏览器第二次发送请求会将前一次服务器响应中的Session ID放在请求的Cookie中一并发送到服务器上，服务器从请求中提取出Session ID，并和保存的所有Session ID进行对比，找到这个用户所对应的Session，从而知道了用户的登录信息。一般Session ID会有时间限制，超时后毁掉这个值，默认30分钟。
*   当用户在应用程序的 Web页间跳转时，也就是一次会话期间，浏览器不关闭时，Session ID是一直不变的。

![](https://img-blog.csdnimg.cn/20181215141413189.png)

SessionID除了可以保存在Cookie中外，还可以保存在URL中，作为请求的一个参数(sid)。

### Session的一些安全配置

*   session应该设置时效性，比如半个小时，当用户在半小时内未操作，即清除session
*   关闭浏览器，即清除session
*   如果应用为了用户体验，只要session一直在使用，则session不失效。这样，当用户cookie被盗后，可能导致session保持攻击。这样的做法是：(1) 给session设置一个时间，无论该session活动与否，都强制清除session。(2) 用户的IP、UserAgent等信息变化后，强制清除session。

Token认证机制
---------

**Token**：Token是服务器端生成的用于验证用户登录状态的加密数据，和用session验证差不多。只不过Token验证服务器端不需要存储用户会话所需的配置等数据。只需要后端将Token进行验证签名，然后再发给浏览器。所以，使用Token进行验证，在一次会话中，Token值是不变化的，这和session一样。

![](https://img-blog.csdnimg.cn/20181215141206355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181215140322263.png)

Token预防CSRF
-----------

上面利用Token验证是将Token值放在Cookie中用来验证用户登录状态，而我们现在要利用Token来预防[CSRF](https://so.csdn.net/so/search?q=CSRF&spm=1001.2101.3001.7020)的话，就不能将Token值放在CSRF中了。我们可以如下做：

当我们访问的网页中含有需要修改数据的地方，后端服务器就会随机发送一个Token值给前端，然后我们修改完数据提交的请求包中，就会有该token字段，后端提取该token验证登录状态。当我们每次访问该网页或者其他修改数据的网页时，服务器端返回的Token值都是不同的，都是随机的。这样，可以有效预防CSRF。

相关文章：[DVWA之CSRF(High级别)](https://blog.csdn.net/qq_36119192/article/details/82918141#High)

                  [CSRF(跨站请求伪造)](https://blog.csdn.net/qq_36119192/article/details/82820115)

Session认证和Token认证的区别
--------------------

现在大多数网站用户认证都是基于 session 的，即在服务端生成用户相关的 session 数据，而发给客户端 sesssion\_id 存放到 cookie 中，这样用客户端请求时带上 session\_id 就可以验证服务器端是否存在 session 数据，以此完成用户认证。这种认证方式，可以更好的在服务端对会话进行控制，安全性比较高(session\_id 随机），但是服务端需要存储 session 数据(如内存或数据库），这样无疑增加维护成本和减弱可扩展性(多台服务器)。 CSRF 攻击一般基于 cookie 。

基于 token 的用户认证是一种服务端无状态的认证方式，服务端不用存放 token 数据。用户验证后，服务端生成一个 token(hash 或 encrypt)发给客户端，客户端可以放到 cookie 或 localStorage 中，每次请求时在 Header 中带上 token ，服务端收到 token 通过验证后即可确认用户身份。这种方式相对 cookie 的认证方式就简单一些，服务端不用存储认证数据，易维护扩展性强， token 存在 localStorage 可避免 CSRF 。不过这种方式在加密或解密的时候会有一些性能开销(好像也不是很大)，有些对称加密存在安全隐患(aes cbc 字节翻转攻击)。