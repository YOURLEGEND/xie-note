**目录**

[HTTP和HTTPS](#t0)

[Cookie、Session](#t1)

[HTTP请求方法(Get/Post)](#t2)

[一次HTTP请求的过程](#t3)

[一次请求(Request)和回复(Reply)的数据包](#t4)

[Request请求包](#t5)

[Reply回复包](#t6)

[HTTP认证](#t7)

[Basic基本认证](#t8)

[Token认证](#t9)

* * *

HTTP和HTTPS
----------

HTTP协议（HyperText Transfer Protocol，超文本传输协议）：是一种发布和接收 HTML页面的方法。

HTTPS（Hypertext Transfer Protocol over Secure Socket Layer）简单讲是HTTP的安全版，在HTTP下加入SSL层。

HTTP和HTTPS均是由TCP协议封装而来，在进行http协议和https协议时，需要进行 三次握手和四次挥手

SSL（Secure Sockets Layer 安全套接层）主要用于Web的安全传输协议，在传输层对网络连接进行加密，保障在Internet上数据传输的安全

![](https://img-blog.csdnimg.cn/20181123153848916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**HTTP三点注意事项：**

*   **HTTP是无连接**：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
*   **HTTP是媒体独立的**：这意味着，只要客户端和服务器知道如何处理的数据内容，任何类型的数据都可以通过HTTP发送。客户端以及服务器指定使用适合的MIME-type内容类型。
*   **HTTP是无状态**：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

![](https://img-blog.csdnimg.cn/20190323123053955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Cookie、Session
--------------

[Cookie、Session和Token的区别](https://blog.csdn.net/qq_36119192/article/details/84977902)

HTTP请求方法(Get/Post)
------------------

```
方法      描述      
GET       Get长度限制为1024，特别快，不安全，在URL里可见，URL提交参数以？分隔，多个参数用&连接，请求指定的页面信息，并返回实体主体。      
HEAD      类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头      
POST      长度一般无限制，由中间件限制，较慢，安全，URL里不可见。请求的参数在数据包的请求body中      
PUT       向指定资源位置上传其最新内容      
DELETE    请求服务器删除指定的页面。      
CONNECT   HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。      
OPTIONS   返回服务器针对特定资源所支持的HTTP请求方法。也可以利用向Web服务器发送'*'的请求来测试服务器的功能性。      
TRACE     回显服务器收到的请求，主要用于测试或诊断。
```


**常见的URL编码** 

```
空格        %20 或 %A0       
'           %27            
"           %22             
#           %23         
%           %25          
&           %26                
*           %2A      
+           %2B      
,           %2C      1
-           %2D      1
.           %2E      1
/           %2F      1
\           %5C      1
=           %3d      1
回车（\r）   %0d      1
换行 (\n)    %0a
```


**get和post的区别**

*   GET是从服务器上获取数据，POST是向服务器传送数据
*   GET请求参数显示，都显示在浏览器网址上，HTTP服务器根据该请求所包含URL中的参数来产生响应内容，即“Get”请求的参数是URL的一部分。 例如： `http://www.baidu.com/s?wd=Chinese`
*   POST请求参数在请求体当中，消息长度没有限制而且以隐式的方式进行发送，通常用来向HTTP服务器提交量比较大的数据（比如请求中包含许多参数或者文件上传操作等），请求的参数包含在“Content-Type”消息头里，指明该消息体的媒体类型和编码.

一次HTTP请求的过程
-----------

1.  当用户在浏览器的地址栏中输入一个URL并按回车键之后，浏览器会向HTTP服务器发送HTTP请求。HTTP请求主要分为“Get”和“Post”两种方法。
2.  当我们在浏览器输入URL [http://www.baidu.com](http://www.baidu.com/) 的时候，浏览器发送一个Request请求去获取 [http://www.baidu.com](http://www.baidu.com/) 的html文件，服务器把Response文件对象发送回给浏览器。
3.  浏览器分析Response中的 HTML，发现其中引用了很多其他文件，比如Images文件，CSS文件，JS文件。 浏览器会自动再次发送Request去获取图片，CSS文件，或者JS文件。
4.  当所有的文件都下载成功后，网页会根据HTML语法结构，完整的显示出来了。

一次请求(Request)和回复(Reply)的数据包
---------------------------

### **Request请求包**

```
GET /test/  HTTP/1.1      
Host: www.baidu.com      
Connection: keep-alive      
Pragma: no-cache      
Cache-Control: no-cache      
Upgrade-Insecure-Requests: 1      
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.101 Safari/537.36      
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8      
Referer: https://www.baidu.com/index.php      1
Accept-Encoding: gzip, deflate, br      1
Accept-Language: zh,zh-CN;q=0.8,ar;q=0.6,zh-TW;q=0.4      1
Cookie: BAIDUID=AE4D1DA6B2D6689BB8C557B3436893E3:FG=1;BIDUPSID=AE4D1DA6B2D6689BB8C557B3436893E3; PSTM=1501466227;
```


1\. GET              
   获取当前主机该路径下的数据  
   HTTP/1.1是http协议的版本号

2\. Host (主机和端口号)  
Host：对应网址URL中的Web名称和端口号，用于指定被请求资源的Internet主机和端口号，通常属于URL的一部分。默认是80端口，如果是8080端口的话，则为： www.baidu.com:8080

3\. Connection (链接类型)  
Connection：表示客户端与服务连接类型

当传输的数据较小时，可以一次传输完成时，Connection的值为close

当传输的数据较大时，不能一次性传输完成时，则数据需要分块传输，即把 Connection 的值设置为 keep-alive  
    Client 发起一个包含 Connection：keep-alive 的请求  
    Server收到请求后：  
          如果 Server 支持 keep-alive，回复一个包含 Connection：keep-alive 的响应，不关闭连接；  
          如果 Server 不支持 keep-alive，回复一个包含 Connection：close 的响应，关闭连接。  
    如果client收到包含 Connection:keep-alive 的响应，向同一个连接发送下一个请求，直到一方主动关闭连接。keep-alive在很多情况下能够重用连接，减少资源消耗，缩短响应时间，比如当浏览器需要多个文件时(比如一个HTML文件和相关的图形文件)，不需要每次都去请求建立连接。

4\. Upgrade-Insecure-Requests (升级为HTTPS请求)  
Upgrade-Insecure-Requests：升级不安全的请求，意思是会在加载 http 资源时自动替换成 https 请求，让浏览器不再显示https页面中的http请求警报。  
HTTPS 是以安全为目标的 HTTP 通道，所以在 HTTPS 承载的页面上不允许出现 HTTP 请求，一旦出现就是提示或报错。

5\. User-Agent (浏览器名称)

User-Agent：是客户浏览器的名称  
6\. Accept (接收的文件类型)  
Accept：指浏览器或其他客户端可以接受的MIME（Multipurpose Internet Mail Extensions（多用途互联网邮件扩展））文件类型，服务器可以根据它判断并返回适当的文件格式。  
举例：  
Accept: \*/\*： 表示什么都可以接收。  
Accept：image/gif： 表明客户端希望接受GIF图像格式的资源；  
Accept：text/html： 表明客户端希望接受html文本。  
Accept:   text/html, application/xhtml+xml;q=0.9, image/\*;q=0.8：表示浏览器支持的 MIME 类型分别是 html文本、xhtml和xml文档、所有的图像格式资源。  
q是权重系数，范围 0 =< q <= 1，q 值越大，请求越倾向于获得其“;”之前的类型表示的内容。若没有指定q值，则默认为1，按从左到右排序顺序；若被赋值为0，则用于表示浏览器不接受此内容类型。  
Text：用于标准化地表示的文本信息，文本消息可以是多种字符集和或者多种格式的；  
Application：用于传输应用程序数据或者二进制数据。

7\. Referer (页面跳转处)  
Referer：表明产生请求的网页来自于哪个URL，用户是从该 Referer页面访问到当前请求的页面。这个属性可以用来跟踪Web请求来自哪个页面，是从什么网站来的等。  
有时候遇到下载某网站图片，需要对应的referer，否则无法下载图片，那是因为人家做了防盗链，原理就是根据referer去判断是否是本网站的地址，如果不是，则拒绝，如果是，就可以下载；

8\. Accept-Encoding（文件编解码格式）  
Accept-Encoding：指出浏览器可以接受的编码方式。编码方式不同于文件格式，它是为了压缩文件并加速文件传递速度。浏览器在接收到Web响应之后先解码，然后再检查文件格式，许多情形下这可以减少大量的下载时间。  
举例：Accept-Encoding:gzip;q=1.0, identity; q=0.5, \*;q=0

如果有多个Encoding同时匹配, 按照q值顺序排列，本例中按顺序支持 gzip, identity压缩编码，支持gzip的浏览器会返回经过gzip编码的HTML页面。 如果请求消息中没有设置这个域服务器假定客户端对各种内容编码都可以接受

9\. Accept-Language（语言种类）  
Accept-Langeuage：指出浏览器可以接受的语言种类，如en或en-us指英语，zh或者zh-cn指中文，当服务器能够提供一种以上的语言版本时要用到

10\. Cookie （Cookie）  
Cookie：浏览器用这个属性向服务器发送Cookie。Cookie是在浏览器中寄存的小型数据体，它可以记载和服务器相关的用户信息，也可以用来实现会话功能，以后会详细讲。

11\. Accept-Charset（字符编码）  
Accept-Charset：指出浏览器可以接受的字符编码。  
举例：Accept-Charset：iso-8859-1、gb2312、utf-8、ISO8859-1：通常叫做Latin-1。Latin-1包括了书写所有西方欧洲语言不可缺少的附加字符，英文浏览器的默认值是ISO-8859-1.  
    gb2312：标准简体中文字符集;  
    utf-8：UNICODE 的一种变长字符编码，可以解决多种语言文本显示问题，从而实现应用国际化和本地化。  
如果在请求消息中没有设置这个域，缺省是任何字符集都可以接受。

12\. Content-Type (POST数据类型)  
Content-Type：表示POST请求里用来表示的内容类型。一般有 application/x-www-form-urlencoded，multipart/form-data，text/plain三种

13\. Pragma

pragma是http/1.1之前版本的历史遗留字段，仅作为与http的向后兼容而定义。

规范定义的唯一形式：Pragme:no-cache  。只用于客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。

14\. Cache-Control

如果所有的中间服务器都实现以 HTTP/1.1为标准，那么直接使用 Cache-Control:no-cache 即可，如果不是的话，就要包含两个字段

```
Cache-Control:no-cache      
Pragme:no-cache
```


15.  Fetch-Site系列

Chrome 意欲在下一个版本 76 实现 Fetch Metadata请求头提案，该提案允许浏览器在发起请求时带上请求的相关上下文，使得服务器端可以进行安全相关的校验。提案的请求头包括

*   请求目标 `Sec-Fetch-Dest`
*   请求模式 `Sec-Fetch-Mode`（跨域规则与浏览上下文）
*   是否跨域的 `Sec-Fetch-Site` 
*   是否用户触发的 `Sec-Fetch-User`。

### Reply回复包

```
HTTP/1.1 200 OK      
Date: Fri, 05 Oct 2018 01:35:48 GMT      
Server: Apache/2.4.23 (Win32) OpenSSL/1.0.2j PHP/5.4.45      
X-Powered-By: PHP/5.4.45      
Expires: Tue, 23 Jun 2009 12:00:00 GMT      
Cache-Control: no-cache, must-revalidate      
Pragma: no-cache      
Content-Length: 5212      
Connection: close      1
Content-Type: text/html;charset=utf-8
```


1. HTTP/1.1  200 OK

1.  HTTP/1.1  是http版本号  
2.  200 OK  是状态码 

*   200 页面正常
*   301、302 、304 重定向
*   400 http协议错误 、    401身份认证  、  403 禁止访问  、     404 页面不存在
*   500 页面的动态代码有错误         502 响应超时   503 IIS服务器程序池出错报这个  521网站反爬返回头

2. Date: Fri, 05 Oct 2018 01:35:48 GMT  

   服务器回复数据时的日期和时间

3\. Server: Apache/2.4.23 (Win32) OpenSSL/1.0.2j PHP/5.4.45

  服务器的Web服务器类型，中间件等

4\. Content-Length: 5212

  回复的数据包的长度  
5\. Connection: close

  链接类型，不支持长链接，所以关闭  
6\. Content-Type: text/html;charset=utf-8

  回复的数据类型，html格式的，utf8编码

HTTP认证
------

### Basic基本认证

BASIC认证概述  
当一个客户端向HTTP服务器进行数据请求时，如果客户端未被认证，则HTTP服务器将通过基本认证过程对客户端的用户名及密码进行验证，以决定用户是否合法。客户端在接收到HTTP服务器的身份认证要求后，会提示用户输入用户名及密码，然后将用户名及密码以BASE64加密，加密后的密文将附加于请求信息中， 如当用户名为anjuta，密码为：123456时，客户端将用户名和密码用“：”合并，并将合并后的字符串用BASE64加密为密文，并于每次请求数据时，将密文附加于请求头（Request Header）中。HTTP服务器在每次收到请求包后，根据协议取得客户端附加的用户信息（BASE64加密的用户名和密码），解开请求包，对用户名及密码进行验证，如果用户名及密码正确，则根据客户端请求，返回客户端所需要的数据;否则，返回错误代码或重新要求客户端提供用户名及密码。  
   
BASIC认证的过程  
基本认证步骤：

1.  客户端访问一个受http基本认证保护的资源。
2.  服务器返回401状态，要求客户端提供用户名和密码进行认证。（验证失败的时候，响应头会加上WWW-Authenticate: Basic realm="请求域"。）(401 Unauthorized   WWW-Authenticate： Basic realm="WallyWorld")
3.  客户端将输入的用户名密码用Base64进行编码后，采用非加密的明文方式传送给服务器。Authorization: Basic xxxxxxxxxx.
4.  服务器将Authorization头中的用户名密码解码并取出，进行验证，如果认证成功，则返回相应的资源。如果认证失败，则仍返回401状态，要求重新进行认证。

优点：  
基本认证的一个优点是基本上所有流行的网页浏览器都支持基本认证。基本认证很少在可公开访问的互联网网站上使用，有时候会在小的私有系统中使用（如路由器网页管理接口）。后来的机制HTTP摘要认证是为替代基本认证而开发的，允许密钥以相对安全的方式在不安全的通道上传输。  
   
缺点：  
虽然基本认证非常容易实现，但该方案建立在以下的假设的基础上，即：客户端和服务器主机之间的连接是安全可信的。特别是，如果没有使用SSL/TLS这样的传输层安全的协议，那么以明文传输的密钥和口令很容易被拦截。该方案也同样没有对服务器返回的信息提供保护。现存的浏览器保存认证信息直到标签页或浏览器被关闭，或者用户清除历史记录。HTTP没有为服务器提供一种方法指示客户端丢弃这些被缓存的密钥。这意味着服务器端在用户不关闭浏览器的情况下，并没有一种有效的方法来让用户登出。  
   
特记事项：  
1、Http是无状态的，同一个客户端对同一个realm内资源的每一个访问会被要求进行认证。  
2、客户端通常会缓存用户名和密码，并和authentication realm一起保存，所以，一般不需要你重新输入用户名和密码。  
3、以非加密的明文方式传输，虽然转换成了不易被人直接识别的字符串，但是无法防止用户名密码被恶意盗用。虽然用肉眼看不出来，但用程序很容易解密。

### Token认证

[Token认证机制](https://blog.csdn.net/qq_36119192/article/details/84977902#Token%E8%AE%A4%E8%AF%81%E6%9C%BA%E5%88%B6)

相关文章：[HTTP协议详解（真的很经典）](https://www.cnblogs.com/li0803/archive/2008/11/03/1324746.html?from=timeline)