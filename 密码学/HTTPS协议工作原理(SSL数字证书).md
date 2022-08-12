**目录**

[HTTPS](#t0)

[SSL协议的工作过程](#t1)

[SSL数字证书的查看](#t2)

* * *

HTTPS
=====

我们都知道HTTP协议是明文传输的，并且不能验证对方的身份，而且不能保证数据的完整性。而当我们在网络上进行购物电子交易时，电[子网](https://so.csdn.net/so/search?q=%E5%AD%90%E7%BD%91&spm=1001.2101.3001.7020)银转账时，这种方式就显得很不安全了。如果黑客截取了我们和服务器端的通信数据，那么黑客就能获取我们的一些敏感信息了。所以，HTTPS应运而生！

HTTPS是在HTTP协议的基础上加入了[SSL](https://so.csdn.net/so/search?q=SSL&spm=1001.2101.3001.7020)协议，SSL协议加在了传输层和应用层之间。在加入了SSL协议后，HTTPS相比HTTP更加的安全，其对数据的传输进行了加密处理，并且能验证通信双方的身份，还保证了数据传输过程的完整性。这样，即使黑客截取了我们和服务器之间的通信数据，它也获取不到任何有用的信息。

需要说明的是，SSL协议是独立于 HTTP 的协议，所以不光是 HTTP 协议，其他运行在应用层的 SMTP和 Telnet 等协议均可配合 SSL 协议使用。可以说 SSL 是当今世界上应用最为广泛的网络安全技术。

![](https://img-blog.csdnimg.cn/20181123154351520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SSL协议的工作过程
----------

那么SSL协议是如何工作的呢？

首先，这需要服务器拥有SSL证书，SSL证书是需要向第三方CA机构申请的，现在大部分的SSL证书都是要付费申请的。

我们拿 baidu.com为例：百度首先利用公钥密码体制生成一对公私钥，私钥自己保存着，然后将公钥和baidu.com的一些身份信息发送给第三方可信任CA机构，CA机构收到百度的申请之后，将执行一些必要的步骤，以确信请求确实由百度发送而来，并且这些信息是正确的。然后，认证中心对百度发来的公钥和百度的身份信息进行数字签名，生成数字证书，然后发送给百度。数字证书里面包含了**百度的公钥、百度的身份信息** 和 **第三方CA机构的数字签名**。

![](https://img-blog.csdnimg.cn/2018112316515614.png)

 那么百度拥有了SSL数字证书之后，我们和百度之间是如何工作的呢？

1.  客户端访问 https://www.baidu.com ，要求与百度Web服务器建立SSL连接
2.  百度Web服务器接收到客户端请求后，会将网站的证书信息发送给客户端
3.  客户端收到百度的证书信息后，通过证书上第三方可信任机构CA的签名，验证证书的真假。
4.  验证真实性之后，客户端的浏览器与百度Web服务器开始协商SSL连接的安全等级，也就是信息加密的等级。
5.  客户端的浏览器根据双方同意的安全等级，建立会话密钥对，然后利用网站证书中的公钥将会话密钥对的公钥进行加密，并发送给百度Web服务器
6.  百度Web服务器利用自己的私钥解密出会话密钥对的公钥。所以现在我们有了百度的公钥，百度也有了我们浏览器会话密钥对的公钥
7.  浏览器和百度Web服务器分别利用对方的公钥对数据进行加密传输。

![](https://img-blog.csdnimg.cn/20181123172559461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SSL数字证书的查看
----------

**浏览器工具->Internet选项->内容->证书**，就可以看到我们浏览器上的数字证书了。所谓证书，其实是对公钥的封装，在公钥的基础上添加了诸如颁发者之类的信息。

![](https://img-blog.csdnimg.cn/20181116135452428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/201811231726461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181123172701524.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[为什么站点使用https加密之后还能看到相关数据](https://www.wosign.com/news/2016-0126-01.htm)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[网络技能树](https://edu.csdn.net/skill/network/network-bd22073e575c4d4ea1325760a3912954)[支撑应用程序的协议](https://edu.csdn.net/skill/network/network-bd22073e575c4d4ea1325760a3912954)[HTTP协议](https://edu.csdn.net/skill/network/network-bd22073e575c4d4ea1325760a3912954)6484 人正在系统学习中