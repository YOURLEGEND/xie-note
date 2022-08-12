**目录**

[心脏出血漏洞(CVE-2014-0160)](#t0 "心脏出血漏洞(CVE-2014-0160)")

[OpenSSL CCS注入漏洞(CVE-2014-0224)](#t1 "OpenSSL CCS注入漏洞(CVE-2014-0224)")

[OpenSSL FREAK Attack漏洞(CVE-2015-0204)](#t2 "OpenSSL FREAK Attack漏洞(CVE-2015-0204)")

[TLS/SSL协议RC4算法漏洞（CVE-2013-2566）](#t3 "TLS/SSL协议RC4算法漏洞（CVE-2013-2566）")

[SSLv3 POODLE攻击信息泄露漏洞（CVE-2014-3566）](#t4 "SSLv3 POODLE攻击信息泄露漏洞（CVE-2014-3566）")

* * *

>  关于[SSL](https://so.csdn.net/so/search?q=SSL&spm=1001.2101.3001.7020)相关漏洞在线检测网站： [SSL漏洞在线检测](http://scan.ssleye.com/# "SSL漏洞在线检测")

**SSL**是Secure Sockets Layer（安全套接层协议）的缩写，可以在Internet上提供秘密性传输。其目标是保证两个应用间通信的保密性和可靠性,可在服务器端和用户端同时实现支持。已经成为Internet上保密通讯的工业标准。

SSL能使用户/服务器应用之间的通信不被攻击者窃听，并且始终对服务器进行认证，还可选择对用户进行认证。SSL协议要求建立在可靠的传输层协议(TCP)之上。SSL协议的优势在于它是与应用层协议独立无关的，高层的应用层协议(例如：HTTP，FTP，Telnet等)能透明地建立于SSL协议之上。SSL协议在应用层协议通信之前就已经完成加密算法、通信秘钥的协商及服务器认证工作。在此之后应用层协议所传送的数据都会被加密，从而保证通信的私密性。

而**OpenSSL**是一个开放源代码的软件库包，应用程序可以使用这个包来进行加密安全通信，避免窃听，同时确认另一端连接者的身份。这个包广泛被应用在互联网的网页服务器上。

试想一下，我们所依赖的，所信任的加密算法出现安全漏洞，这将会是多严重的事情！

工具下载：git clone https://github.com/drwetter/testssl.sh.git

### 心脏出血漏洞(CVE-2014-0160)

2014年4月8日，[OpenSSL](https://so.csdn.net/so/search?q=OpenSSL&spm=1001.2101.3001.7020)的大漏洞曝光。这个漏洞被曝光的黑客命名为心脏出血(heartbleed)，意思是“心脏流血”——代表着最致命的内伤。利用该漏洞，黑客坐在自己家里电脑前，就可以实时获取到约30%https开头网址的用户登录账号密码，包括大批网银、购物网站、电子邮件等。

关于心脏出血漏洞详情——> [OpenSSL心脏出血漏洞分析(CVE-2014-0160)](http://blog.nsfocus.net/openssl-bleed/ "OpenSSL心脏出血漏洞分析(CVE-2014-0160)")

```
./testssl.sh -H 121.35.110.110
```


![](https://img-blog.csdnimg.cn/20190603143719237.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### OpenSSL CCS注入漏洞(CVE-2014-0224)

在OpenSSL建立握手连接的阶段，会发送一种名为ChangeCipherSpec（CCS）的请求，在发送该请求时，攻击者可以通过中间人攻击来劫持服务端与客户端之间的加密通信。

利用该问题，攻击者能够解析加密的链接并将其解密，读取或对通信数据进行操作。但该问题如想成功利用必须服务端域客户端双方均存在该问题。

*   漏洞危害：攻击者可以发起中间人攻击并利用此漏洞篡改或监听SSL加密传输的数据。
*   加固建议：升级openssl

```
./testssl.sh -I 121.35.110.110
```


![](https://img-blog.csdnimg.cn/20190603143315748.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### OpenSSL FREAK Attack漏洞(CVE-2015-0204)

2015年3月3日，研究人员宣发现一个新的SSL / TLS漏洞叫FREAK Attack漏洞(CVE-2015-0204)。它允许攻击者拦截脆弱的客户端和服务器之间的HTTPS连接，并迫使他们使用弱密码，于是攻击者可以破解窃取或操纵敏感数据。

*   漏洞危害：攻击者可拦截受影响的客户端与服务器之间的 HTTPS 连接，并强制其使用弱加密。当 TLS/SSL 客户端使用较弱的密钥交换方法时，攻击者可破解正在使用的密钥。攻击者使用破解的密钥，可在通信期间解密窃取数据，甚至恶意操作敏感信息。
*   加固建议：升级openssl。

关于OpenSSL FREAK Attack漏洞详情——> [OpenSSL FREAK Attack漏洞(CVE-2015-0204)检测方法及修复建议](https://www.secpulse.com/archives/5193.html "OpenSSL FREAK Attack漏洞(CVE-2015-0204)检测方法及修复建议")

```
./testssl.sh -F 121.35.110.110
```


![](https://img-blog.csdnimg.cn/20190603143517715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ****TLS/SSL协议RC4算法漏洞****（CVE-2013-2566）

*   漏洞危害：SSL/TLS内使用的RC4算法存在单字节偏差安全漏洞，可允许远程攻击者通过分析统计使用大量相同明文的大量会话，利用此漏洞恢复纯文本信息。
*   加固建议：关闭RC4算法

```
./testssl.sh -4 121.35.110.110
```


![](https://img-blog.csdnimg.cn/20190614134144830.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ****SSLv3 POODLE********攻击信息泄露漏洞****（CVE-2014-3566）

*   漏洞危害：可以让攻击者获取SSL通信中的部分信息明文，如cookie,session等
*   加固建议：禁用SSLv3

```
./testssl.sh -O 121.35.110.110
```


![](https://img-blog.csdnimg.cn/20190614140435577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

还有很多的关于SSL协议的漏洞，比如：SSL Padding Oracle漏洞等等

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[SSL和TLS漏洞验证](https://www.0dayhack.com/post-749.html "SSL和TLS漏洞验证")

相关文章：[HTTPS协议工作原理(SSL数字证书)](https://blog.csdn.net/qq_36119192/article/details/84395154 "HTTPS协议工作原理(SSL数字证书)")