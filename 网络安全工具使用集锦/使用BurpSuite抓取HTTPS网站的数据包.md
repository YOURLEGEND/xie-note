因为以前https网站的数据包我都是用[Fiddler](https://so.csdn.net/so/search?q=Fiddler&spm=1001.2101.3001.7020)抓取的，Fiddlert自动帮我们配置好了证书，所以就没用BurpSuite抓取过，今天特意去学习了下如何使用BurpSuite抓取https网站的数据包。

关于HTTPS协议中证书的认证过程，传送门——>[HTTPS协议工作原理(SSL数字证书)](https://blog.csdn.net/qq_36119192/article/details/84395154 "HTTPS协议工作原理(SSL数字证书)")

BurpSuite之所以不能抓取https数据包，是因为BurpSuite作为中间人代理，我们和https网站之间的数据通信都是由BurpSuite来代理的，而https通信是需要SSL证书的，BurpSuite的证书我们浏览器默认是不信任的。所以要想实现BurpSuite抓取https的数据包，最关键的是让浏览器信任BurpSuite的证书。

下面我以Firefox浏览器为例，演示如何让Firefox信任BurpSuite的证书。

首先，打开BurpSuite，配置好代理。如果不知道怎么用BrupSuite的，移步——> [Burpsuite工具的使用](https://blog.csdn.net/qq_36119192/article/details/84310858 "Burpsuite工具的使用")

然后访问  [http://burp/](http://burp/ "http://burp/")   ,下载BurpSuite的证书

![](https://img-blog.csdnimg.cn/20181212135219371.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下载到桌面后，得到的是一个 cacert.der 的文件，而并不是我们常见的 .cer 格式的证书，这是因为证书的编码方式不一样导致的，但是这并不影响我们后续的导入证书

![](https://img-blog.csdnimg.cn/20181212135343390.png)

然后我们在Firefox中打开**设置——>选项——>高级——>证书——>查看证书——>证书机构——>导入**，选择我们刚刚下载到桌面的证书，点击打开

![](https://img-blog.csdnimg.cn/20181212141256566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后勾选信任由此证书颁发机构标识的网站，点击确定。

![](https://img-blog.csdnimg.cn/20181212141313106.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们的BurpSuite的证书就导入进Firefox的可信任证书里面了。

当我们访问https的网站，比如百度的时候，可以发现，BurpSuite已经能抓取数据包了。

![](https://img-blog.csdnimg.cn/20181212141533380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

对于其他的浏览器，比如chrome，QQ浏览器等，步骤都一样，只是最后导入证书是导入进受信任的根证书颁发机构。

![](https://img-blog.csdnimg.cn/2018121214260015.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)