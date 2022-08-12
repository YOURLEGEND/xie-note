我们常见的HTTP请求方法是GET、POST和HEAD。但是，其实除了这两个之外，HTTP还有一些其他的请求方法。

**WebDAV** （Web-based Distributed Authoring and Versioning） 一种基于 HTTP 1.1协议的[通信协议](https://so.csdn.net/so/search?q=%E9%80%9A%E4%BF%A1%E5%8D%8F%E8%AE%AE&spm=1001.2101.3001.7020)。它扩展了HTTP 1.1，在GET、POST、HEAD等几个HTTP标准方法以外添加了一些新的方法，使应用程序可对Web Server直接读写，并支持写文件锁定(Locking)及解锁(Unlock)，还可以支持文件的版本控制。

WebDAV虽然方便了网站管理员对网站的管理，但是也带来了新的安全风险！

*   PUT：由于PUT方法自身不带验证机制，利用PUT方法可以向服务器上传文件，所以恶意攻击者可以上传木马等恶意文件。
*   DELETE：利用DELETE方法可以删除服务器上特定的资源文件，造成恶意攻击。
*   OPTIONS：将会造成服务器信息暴露，如中间件版本、支持的HTTP方法等。
*   TRACE：可以回显服务器收到的请求，主要用于测试或诊断，一般都会存在反射型跨站漏洞

以下是WebDAV支持的HTTP请求方法。

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


我们可以将请求方法设置为OPTIONS，来查看服务器支持的请求方法。

如下，我们查看一下CSDN支持的请求方法，从返回包我们可以看出支持GET、POST、OPTIONS。这是安全的。

![](https://img-blog.csdnimg.cn/20190503103620248.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是有些网站开启了WebDAV，并且管理员配置不当，导致支持危险的HTTP方法，如下。该网站除了支持GET、POST、OPTIONS、HEAD之外，还支持 PUT、DELETE请求方法。

![](https://img-blog.csdnimg.cn/20190503103905174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**风险等级**：低风险(具体风险视通过不安全的HTTP请求能获得哪些信息)

**修订建议：**如果服务器不需要支持WebDAV，请务必禁用它，或禁止不必要的HTTP 方法，只留下GET、POST方法！

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)