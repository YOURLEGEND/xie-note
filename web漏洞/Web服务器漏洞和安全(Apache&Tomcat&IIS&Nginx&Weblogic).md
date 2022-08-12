**目录**

[Apache](#t0 "Apache")

[Apache出现过的漏洞](#t1 "Apache出现过的漏洞")

[Tomcat](#t2 "Tomcat")

[Tomcat出现过的漏洞](#t3 "Tomcat出现过的漏洞")

[Nginx](#t4 "Nginx")

[Nginx出现过的漏洞](#t5 "Nginx出现过的漏洞")

[IIS](#t6 "IIS")

[IIS出现过的漏洞](#t7 "IIS出现过的漏洞")

[Weblogic](#t8 "Weblogic")

[Weblogic出现过的漏洞](#t9 "Weblogic出现过的漏洞")

[Weblogic常识](#t10 "Weblogic常识")

[JBoss](#t11 "JBoss")

[JBoss出现过的漏洞](#t12 "JBoss出现过的漏洞")

[JBoss常识](#t13 "JBoss常识")

* * *

![](https://img-blog.csdnimg.cn/20200103113324246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Web服务器是Web应用的载体，如果这个载体出现安全问题，那么运行在其中的web应用程序的安全也无法得到保障。Web服务器安全，考虑的是应用部署时的运行环境安全，这个运行环境包括Web Server、脚本语言解释器、中间件等。

无论是哪种web服务器，都应该做到最小权限原则，即启动该web服务器的用户不应该是 root/Administrator 用户。并且需要保护好Web服务器的日志文件。

*   启动Web服务器时，应该以单独的用户身份运行，通常单独为Web服务器建立一个 User/Group。
*   保护好Web服务器的日志文件。一般来说，攻击者入侵成功后，做的第一件事就是清除入侵痕迹，修改、删除日志文件，因为Web服务器的日志文件应当妥善保管，比如实时发送到远程的日志服务器上。

Apache
------

[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) HTTP Server（以下简称Apache）是Apache软件基金会的一个开放源码的网页服务器，可以在大多数计算机操作系统中运行，是最流行的Web服务器软件之一。虽然近年来Nginx和Lighttpd等Web Server的市场份额增长得很快，但Apache仍然是这个领域中独一无二的巨头，互联网上大多数的Web应用依然运行在Apache上。

纵观Apache的漏洞史，它曾经出现过很多高危漏洞，但这些高危漏洞，大部分是由于Apache 的Module造成的，Apache核心的高危漏洞几乎没有。Apache有很多官方的与非官方的Module，默认启动的Module出现过高危漏洞的非常少，大多数的高危漏洞集中在第三方Module上。

*   检查Apache 的Module安装情况，根据最小权限原则，应该尽可能的减少不必要的Module。对于要使用的Module，则应该检查其对应版本是否存在已知的漏洞。

**Apache的安全加固**：

*   一是Apache Web Server本身是否安全，比如是否存在安全漏洞；
*   二是Apache Web Server是否提供了可使用的安全功能，这部分主要是检查Apache的配置是否得当，在安全性、可用性、稳定性之间取得平衡

### Apache出现过的漏洞

*   CVE-2017-15715：文件解析漏洞
*   CVE-2011-3192：畸形Range选项处理远程拒绝服务漏洞

参考文章：[Apache漏洞利用与安全加固实例分析](https://www.cnblogs.com/pengcc/p/4097456.html "Apache漏洞利用与安全加固实例分析")

Tomcat
------

**Tomcat漏洞预防：**

1、删除webapps目录中的docs、examples、host-manager、manager等正式环境用不着的目录，这一步就可以解决大部分漏洞。有的网站没删除manager页面，并且管理员弱口令，导致直接Getshell。传送门——>[Tomcat管理页面弱口令Getshell](https://blog.csdn.net/qq_36119192/article/details/90446112 "Tomcat管理页面弱口令Getshell")

2、解决“**slow http denial of service attack**”漏洞，缓慢的拒绝服务攻击漏洞。slow http denial of service attack漏洞是利用http post的时候，指定一个非常大的content-length，然后以很低的速度发包，比如10-100s发一个字节，让这种连接不断开，这样当客户端连接多了后，占用了webserver的所有可用连接，从而导制DOS，属于一种打拒绝服务攻击。

**解决方法：**

打开server.xml找到

```
<Connector port="8080" protocol="HTTP/1.1"      
　　connectionTimeout="20000"      
　　redirectPort="8443" />
```


将其中的connectionTimeout="20000"改为1000，其单位为ms。

### Tomcat出现过的漏洞

*   Tomcat后台弱口令上传war包(Tomcat管理弱口令页面Getshell)
*   Tomcat 样例目录session操控漏洞
*   CNNVD-201604-459：远程代码执行漏洞
*   CVE-2016-1240：本地提权漏洞
*   CVE-2017-12615：Tomcat的PUT的上传漏洞
*   CVE-2017-12616：信息泄露漏洞
*   CVE-2018-1305：安全绕过漏洞

相关文章：[Tomcat漏洞集合](https://blog.csdn.net/qq_36119192/article/details/86566722 "Tomcat漏洞集合")

Nginx
-----

**Nginx**是一个高性能的HTTP和反向代理服务器，该程序由俄罗斯Rambler.ru 站点开发，[Nginx](https://so.csdn.net/so/search?q=Nginx&spm=1001.2101.3001.7020)因为性能稳定、低系统资源消耗而闻名。默认监听端口： tcp / 80

### Nginx出现过的漏洞

*   [Nginx畸形解析漏洞](https://blog.csdn.net/qq_36119192/article/details/82834063#Nginx%E4%B8%ADphp%E9%85%8D%E7%BD%AE%E9%94%99%E8%AF%AF%E5%AF%BC%E8%87%B4%E7%9A%84%E8%A7%A3%E6%9E%90%E6%BC%8F%E6%B4%9E "Nginx畸形解析漏洞")
*   [Nginx%00空字节代码解析漏洞](https://blog.csdn.net/qq_36119192/article/details/82834063#%2500%E7%A9%BA%E5%AD%97%E8%8A%82%E4%BB%A3%E7%A0%81%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E "Nginx%00空字节代码解析漏洞")
*   [CVE-2013-4547(%20%00)](https://blog.csdn.net/qq_36119192/article/details/82834063#%2500%E7%A9%BA%E5%AD%97%E8%8A%82%E4%BB%A3%E7%A0%81%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E "CVE-2013-4547(%20%00)")

IIS
---

[IIS](https://so.csdn.net/so/search?q=IIS&spm=1001.2101.3001.7020)是Windows Server中自带的一个Web服务器。

### IIS出现过的漏洞

*   [IIS目录解析漏洞(/test.asp/1.jpg)](https://blog.csdn.net/qq_36119192/article/details/82834063#%E7%AC%AC%E4%B8%80%E7%A7%8D%20%E7%9B%AE%E5%BD%95%E8%A7%A3%E6%9E%90%E6%BC%8F%E6%B4%9E "IIS目录解析漏洞(/test.asp/1.jpg)")
*   [IIS文件名解析漏洞(test.asp;.jpg)](https://blog.csdn.net/qq_36119192/article/details/82834063#%E7%AC%AC%E4%BA%8C%E7%A7%8D%20%E6%96%87%E4%BB%B6%E5%90%8D%E8%A7%A3%E6%9E%90%E6%BC%8F%E6%B4%9E "IIS文件名解析漏洞(test.asp;.jpg)")
*   [IIS畸形解析漏洞](https://blog.csdn.net/qq_36119192/article/details/82834063#%E7%AC%AC%E4%B8%89%E7%A7%8D%20%E7%95%B8%E5%BD%A2%E8%A7%A3%E6%9E%90%E6%BC%8F%E6%B4%9E "IIS畸形解析漏洞")

Weblogic
--------

### Weblogic出现过的漏洞

#控制台路径泄露

*   Weakpassword  

#SSRF：

*   CVE-2014-4210   

#JAVA反序列化：

*   CVE-2015-4852  
*   CVE-2016-0638  
*   CVE-2016-3510   
*   CVE-2017-3248   
*   [Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）](https://blog.csdn.net/qq_36119192/article/details/90716180#Weblogic%20%3C%2010.3.6%20'%20rel= "Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）")
*   CVE-2018-2893   
*   CVE-2019-2725
*   CVE-2019-2729

#任意文件上传

*   CVE-2018-2894   

#XMLDecoder反序列化：

*   [Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）](https://blog.csdn.net/qq_36119192/article/details/90716180#Weblogic%20%3C%2010.3.6%20'%20rel= "Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）")
*   CVE-2017-3506

### Weblogic常识

http为7001端口，https为7002端口

**Google关键字**：[WebLogic](https://so.csdn.net/so/search?q=WebLogic&spm=1001.2101.3001.7020) Server AdministrationConsole inurl:console

**默认的用户名密码**

*   用户名密码均为：weblogic
*   用户名密码均为：system
*   用户名密码均为：portaladmin
*   用户名密码均为：guest

**上传地方**：workshop> Deployments> Web应用程序> 部署新的 Web 应用程序模块>上传war的webshell。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy8zeHhpY1hObFRYTGliVWwwcWpuMDlITlh4ODFzWFY3VFhzRjVJZ0hFTFdJTGpJZ2ljcDdqRmQ2YU12ekxCTDk1ejdtQzhKRzRKZXg0aWNlWjhCUXhoenB5akEvNjQw?x-oss-process=image/format,png)

相关文章：[Weblogic反序列化漏洞](https://blog.csdn.net/qq_36119192/article/details/90716180#Weblogic%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E "Weblogic反序列化漏洞")

                  [Weblogic漏洞——从入门到放弃](https://www.freebuf.com/column/197339.html "Weblogic漏洞——从入门到放弃")

JBoss
-----

上传地方：Deployments>Manage Deployments>Add Content

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X2pwZy8zeHhpY1hObFRYTGliVWwwcWpuMDlITlh4ODFzWFY3VFhzMzBpYlg3TEZpYmVlaWN1UXpHRnZCZXhDa0pramlibHNEck1LdXBRNlRYZkl0enR3MlJoaWJpYkhqbVZBLzY0MA?x-oss-process=image/format,png)

Enable后即可发布

shell地址：http://xx.xx.xx.xx:8080/magerx/test.jsp

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[JBOSS反序列化漏洞](https://blog.csdn.net/qq_36119192/article/details/90716180#JBOSS%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E6%BC%8F%E6%B4%9E "JBOSS反序列化漏洞")

参考文章：白帽子讲Web安全