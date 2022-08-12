  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

Web基础安全培训：[红队培训之Web基础安全](https://www.xie-sec.com/detail/p_62492582e4b04e8d902b2360/6 "红队培训之Web基础安全")​​​​​​​

本文是对Web中最常见漏洞的一个小结，既然是Web漏洞，那自然而然不能忽略了OWASP Top10了。最新版的OWASP Top10还是2017年公布的。如下：

1.  注入
2.  失效的身份认证
3.  敏感信息泄露
4.  XML 外部实体（XXE）
5.  失效的访问控制
6.  安全配置错误
7.  跨站脚本（XSS）
8.  不安全的反序列化
9.  使用含有已知漏洞的组件
10.  不足的日志记录和监控

![](https://img-blog.csdnimg.cn/2020080918531469.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

详情：[OWASP Top 10 2017](https://www.owasp.org/images/d/dc/OWASP_Top_10_2017_%E4%B8%AD%E6%96%87%E7%89%88v1.3.pdf "OWASP Top 10 2017")

以下是个人根据漏洞分类进行总结：

**注入类漏洞**：

*   [SQL注入漏洞详解](https://blog.csdn.net/qq_36119192/article/details/81987834 "SQL注入漏洞详解")
*   [XXE(XML外部实体注入)](https://blog.csdn.net/qq_36119192/article/details/84993356 "XXE(XML外部实体注入)") 、[XXE漏洞利用](https://blog.csdn.net/qq_36119192/article/details/103866581 "XXE漏洞利用")
*   [CRLF注入](https://blog.csdn.net/qq_36119192/article/details/90904769 "CRLF注入")
*   [命令执行和代码执行漏洞](https://blog.csdn.net/qq_36119192/article/details/84848441 "命令执行和代码执行漏洞")

注入类漏洞是应用违背了“数据与代码分离原则”导致的结果。它有两个条件：一是用户能够控制数据的输入，二是代码拼凑了用户输入的数据，把数据当成代码执行了。在对抗注入攻击时，只需要牢记“数据与代码分离原则”，在拼凑的地方进行安全检查。

**文件类漏洞：**

*   [文件上传漏洞](https://blog.csdn.net/qq_36119192/article/details/82848056 "文件上传漏洞")
*   [文件解析漏洞](https://blog.csdn.net/qq_36119192/article/details/82834063 "文件解析漏洞")
*   [文件包含漏洞](https://blog.csdn.net/qq_36119192/article/details/82823685 "文件包含漏洞")
*   [目录浏览(目录遍历)漏洞和任意文件读取/下载漏洞](https://blog.csdn.net/qq_36119192/article/details/86496362 "目录浏览(目录遍历)漏洞和任意文件读取/下载漏洞")

**跨站类漏洞：**

*   [SSRF(服务端请求伪造)漏洞](https://blog.csdn.net/qq_36119192/article/details/84980510 "SSRF(服务端请求伪造)漏洞")
*   [CORS跨域资源共享漏洞](https://blog.csdn.net/qq_36119192/article/details/86511786 "CORS跨域资源共享漏洞")
*   [JSONP跨域资源共享的安全问题](https://blog.csdn.net/qq_36119192/article/details/87248938 "JSONP跨域资源共享的安全问题")
*   [XSS(跨站脚本攻击)详解](https://blog.csdn.net/qq_36119192/article/details/82469035 "XSS(跨站脚本攻击)详解")
*   [CSRF(跨站请求伪造)](https://blog.csdn.net/qq_36119192/article/details/82820115 "CSRF(跨站请求伪造)")

**配置错误漏洞：**

*   [未授权访问漏洞总结](https://blog.csdn.net/qq_36119192/article/details/97619201 "未授权访问漏洞总结")
*   [不安全的HTTP方法](https://blog.csdn.net/qq_36119192/article/details/89785356 "不安全的HTTP方法")
*   [Host头部攻击](https://blog.csdn.net/qq_36119192/article/details/89137300 "Host头部攻击")
*   [CRLF注入](https://blog.csdn.net/qq_36119192/article/details/90904769 "CRLF注入")
*   [目录浏览(目录遍历)漏洞和任意文件读取/下载漏洞](https://blog.csdn.net/qq_36119192/article/details/86496362 "目录浏览(目录遍历)漏洞和任意文件读取/下载漏洞")
*   [HTTP响应头相关漏洞](https://xie1997.blog.csdn.net/article/details/84482870 "HTTP响应头相关漏洞")

**反序列化漏洞：**

*   [PHP反序列化漏洞](https://blog.csdn.net/qq_36119192/article/details/102777681 "PHP反序列化漏洞")
*   [JAVA反序列化漏洞](https://blog.csdn.net/qq_36119192/article/details/84504405 "JAVA反序列化漏洞")

**逻辑类漏洞：**

*   [注册、登录、密码修改页面渗透测试经验小结](https://blog.csdn.net/qq_36119192/article/details/89066061 "注册、登录、密码修改页面渗透测试经验小结")
*   [条件竞争漏洞](https://blog.csdn.net/qq_36119192/article/details/102944493 "条件竞争漏洞")
*   [HTTP参数污染(HPP)漏洞](https://blog.csdn.net/qq_36119192/article/details/93765890 "HTTP参数污染(HPP)漏洞")
*   [逻辑漏洞之越权访问漏洞](https://blog.csdn.net/qq_36119192/article/details/86540786 "逻辑漏洞之越权访问漏洞")

**Web服务器漏洞：**

*   [Web服务器漏洞和安全(Apache/Tomcat/IIS/Nginx/Weblogic)](https://xie1997.blog.csdn.net/article/details/90742551 "Web服务器漏洞和安全(Apache/Tomcat/IIS/Nginx/Weblogic)")

**第三方库漏洞：**

*   [JQuery跨站脚本漏洞](https://blog.csdn.net/qq_36119192/article/details/89811603 "JQuery跨站脚本漏洞")
*   [OpenSSL相关漏洞](https://blog.csdn.net/qq_36119192/article/details/90714681 "OpenSSL相关漏洞")
*   [Struts2漏洞](https://blog.csdn.net/qq_36119192/article/details/87273304 "Struts2漏洞")

Web基础安全培训：[红队培训之Web基础安全](https://www.xie-sec.com/detail/p_62492582e4b04e8d902b2360/6 "红队培训之Web基础安全")