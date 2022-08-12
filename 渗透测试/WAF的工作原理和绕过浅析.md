**目录**

[WAF](#t0 "WAF")

[WAF的判断](#t1 "WAF的判断")

[WAF的工作原理](#t2 "WAF的工作原理")

[基于规则库匹配的WAF](#t3 "基于规则库匹配的WAF")

[WAF的绕过](#t4 "WAF的绕过")

[域名转换为ip](#t5 "域名转换为ip")

[WAF解析HTTP请求阶段绕过](#t6 "WAF解析HTTP请求阶段绕过")

[分块编码(Transfer-Encoding)绕过WAF](#t7 "分块编码(Transfer-Encoding)绕过WAF")

[其他](#t8 "其他")

[WAF匹配规则阶段绕过](#t9 "WAF匹配规则阶段绕过")

[利用溢量数据绕过WAF](#t10 "利用溢量数据绕过WAF")

[其他](#t11 "其他")

[结语](#t12 "结语")

* * *

WAF
---

在实际的[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)过程中，经常会碰到网站存在WAF的情况。网站存在WAF，意味着我们不能使用安全工具对网站进行测试，因为一旦触碰了WAF的规则，轻则丢弃报文，重则拉黑IP。所以，我们需要手动进行WAF的绕过，而绕过WAF前肯定需要对WAF 的工作原理有一定的理解。本文主要从绕过WAF过程中需要注意的角色和点出发，尝试理解它们的运作，构建一个简单的知识框架。

首先，WAF分为非嵌入型WAF和嵌入型WAF，非嵌入型WAF指的是硬件型WAF、云WAF、软件型WAF之类的；而嵌入型WAF指的是网站内置的WAF。非嵌入型WAF对Web流量的解析完全是靠自身的，而嵌入型WAF拿到的Web数据是已经被解析加工好的。所以非嵌入型的受攻击机面还涉及到其他层面，而嵌入型WAF从Web容器模块型WAF、代码层WAF往下走，其对抗畸形报文、扫操作绕过的能力越来越强。当然，在部署维护成本方面，也是越高的。

![](https://img-blog.csdnimg.cn/20191115082725412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### WAF的判断

那么，如何判断一个网站是否存在WAF呢？可以手动也可以使用sqlmap进行测试。比如现在有一个网站：http://www.test.com

**手动检测有没有WAF：**

手动在域名后面加一个不存在的id参数，并且加上很明显的sql语句，查看服务器的回应。

```
http://www.test.com?id=1 union select 1,2,3
```


![](https://img-blog.csdnimg.cn/20191219162543732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Sqlmap检测有没有WAF：**

如果网站存在WAF，sqlmap会有提示。

```
python2 sqlmap.py --batch  --identify-waf --random-agent -u "http://www.test.com"
```


![](https://img-blog.csdnimg.cn/20191212150628456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**WAF的工作原理**
------------

WAF(Web Application Firewall) 可以用来屏蔽常见的网站漏洞攻击，如SQL注入，XML注入、XSS等。WAF针对的是应用层而非网络层的入侵，从技术角度应该称之为Web IPS。

WAF的主要难点是对入侵的检测能力，尤其是对Web服务入侵的检测，WAF最大的挑战是识别率。对于已知的攻击方式，可以谈识别率，但是对于未知的攻击手段，WAF是检测不到的。

### 基于规则库匹配的WAF

目前市面上大多数的WAF都是基于规则的WAF。即WAF对接数据收到的包进行正则匹配过滤，如果正则匹配到与现有漏洞知识库的攻击代码相同，则认为这个恶意代码，从而对于进行阻断。所以，对于基于规则匹配的WAF，需要每天都及时更新最新的漏洞库。

对于这种WAF，它的工作过程是这样的：**解析HTTP请求——>匹配规则——>防御动作——>记录日志** 

具体实现如下：

1.  解析http请求：协议解析模块
2.  匹配规则：规则检测模块，匹配规则库
3.  防御动作：return 403 或者跳转到自定义界面，或者不返回任何数据，或者拉黑IP
4.  日志记录：记录到elk中

**WAF的绕过**
==========

从WAF工作的过程我们可以看到，要想绕过WAF，我们只有在 **WAF解析HTTP请求** 或 **WAF匹配规则** 两个地方进行绕过。因为第三、四步是WAF匹配到攻击之后的操作，这时候WAF已经检测到攻击了。

**域名转换为ip**
-----------

有些WAF设置的是针对域名的防护，在有些时候，我们可以尝试将域名改成ip地址有可以绕过WAF的防护。

WAF解析HTTP请求阶段绕过
---------------

要想在WAF解析HTTP请求阶段绕过，首先，我们得了解HTTP报文的各个字段的含义和服务器对HTTP报文各字段的解析。传送门：[一次HTTP请求的过程](https://blog.csdn.net/qq_36119192/article/details/82942185#%E4%B8%80%E6%AC%A1HTTP%E8%AF%B7%E6%B1%82%E7%9A%84%E8%BF%87%E7%A8%8B "一次HTTP请求的过程")

### 分块编码(Transfer-Encoding)绕过WAF

有关于HTTP数据的分块编码：[HTTP协议之分段传输与分块编码](https://blog.csdn.net/qq_36119192/article/details/88757306 "HTTP协议之分段传输与分块编码")

这种绕过方法利用的是WAF在解析HTTP协议的过程，既然WAF连我们的攻击代码都没有解析完全，那么第二步的正则匹配也就匹配不到我们的攻击代码了，自然就可以绕过了。

相关文章：[在HTTP协议层面绕过WAF](https://www.freebuf.com/news/193659.html "在HTTP协议层面绕过WAF")

                  [利用分块传输吊打所有WAF](https://www.freebuf.com/articles/web/194351.html "利用分块传输吊打所有WAF")

                  [\[技术\]编写Burp分块传输插件绕WAF](http://www.hackliu.com/?p=454 "[技术]编写Burp分块传输插件绕WAF")

                  [HTTP 协议中的 Transfer-Encoding](https://imququ.com/post/transfer-encoding-header-in-http.html "HTTP 协议中的 Transfer-Encoding")

### 其他

其他的关于通过构造HTTP协议来绕过WAF，需要攻击者对 HTTP协议的各字段构造，各容器对于HTTP协议的解析 都很熟悉。笔者对于HTTP协议也还是停留在比较浅显的表面，要想更深入的了解HTTP协议，可以去查看官方文档：RFC2616。

WAF匹配规则阶段绕过
-----------

### 利用溢量数据绕过WAF

这种绕过方法利用的是通过提交非常大的数据，由于数据量过大，超过了WAF的正则匹配字符，我们的恶意代码就不经过WAF的正则匹配了，因此我们的恶意代码就进行绕过了。

传送门——> [绕过网站WAF(图片绕过)](https://blog.csdn.net/qq_36119192/article/details/84876046 "绕过网站WAF(图片绕过)")

### 

###   
其他

其他的关于通过绕过WAF匹配规则来绕过WAF的手段，需要攻击者构造畸形数据来躲避WAF的正则匹配规则，这需要攻击者发散思维，想出尽可能多的奇淫技巧。

结语
==

总的来说，绕WAF可以从两个大的层面来考虑。一是通过HTTP协议层面，二是通过WAF对数据包的正则匹配层面。通过对这两个层面细致的研究，还可以发现更多的绕WAF的方法。笔者这里只是浅析了绕WAF的思想，至于其他绕WAF的奇淫技巧，需要大家自己发散思维。

未完待续，以后遇到好的绕WAF方面的文章，会继续更新。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[HTTP协议](https://blog.csdn.net/qq_36119192/article/details/82942185#%E4%B8%80%E6%AC%A1HTTP%E8%AF%B7%E6%B1%82%E7%9A%84%E8%BF%87%E7%A8%8B "HTTP协议")

                  [HTTP协议之分段传输与分块编码](https://blog.csdn.net/qq_36119192/article/details/88757306 "HTTP协议之分段传输与分块编码")

                  [技术讨论 | 在HTTP协议层面绕过WAF](https://www.freebuf.com/news/193659.html "技术讨论 | 在HTTP协议层面绕过WAF")

                 [利用分块传输吊打所有WAF](https://www.freebuf.com/articles/web/194351.html "利用分块传输吊打所有WAF")

                 [绕过网站WAF(图片绕过)](https://blog.csdn.net/qq_36119192/article/details/84876046 "绕过网站WAF(图片绕过)")

                 [\[技术\]编写Burp分块传输插件绕WAF](http://www.hackliu.com/?p=454 "[技术]编写Burp分块传输插件绕WAF")

                 [对过WAF的一些认知](https://www.anquanke.com/post/id/177044 "对过WAF的一些认知")

                [WAF攻防研究之四个层次Bypass WAF](https://weibo.com/ttarticle/p/show?id=2309404007261092631700&sudaref=www.google.com.hk&display=0&retcode=6102 "WAF攻防研究之四个层次Bypass WAF")