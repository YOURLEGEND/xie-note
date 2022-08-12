**目录**

[Weblogic反序列化漏洞](#t0 "Weblogic反序列化漏洞")

[Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）](#t1 "Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）")

[Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）](#t2 "Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）")

[Weblogic反序列化漏洞（CVE-2019-2725）](#t3 "Weblogic反序列化漏洞（CVE-2019-2725）")

[JBOSS反序列化漏洞](#t4 "JBOSS反序列化漏洞")

[JBoss JMXInvokerServlet 反序列化漏洞(CVE-2015-7501)](#t5 "JBoss JMXInvokerServlet 反序列化漏洞(CVE-2015-7501)")

[JBoss 4.x JBossMQ JMS 反序列化漏洞（CVE-2017-7504）](#t6 "JBoss 4.x JBossMQ JMS 反序列化漏洞（CVE-2017-7504）")

[JBoss 5.x/6.x 反序列化漏洞复现（CVE-2017-12149）](#t7 "JBoss 5.x/6.x 反序列化漏洞复现（CVE-2017-12149）")

[RMI反序列化漏洞](#t8 "RMI反序列化漏洞")

[Fastjson反序列化漏洞](#t9 "Fastjson反序列化漏洞")

* * *

Weblogic反序列化漏洞
--------------

### Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）

[Weblogic](https://so.csdn.net/so/search?q=Weblogic&spm=1001.2101.3001.7020)的WLS Security组件对外提供webservice服务，其中使用了XMLDecoder来解析用户传入的XML数据，在解析的过程中出现反序列化漏洞，导致可执行任意命令。

这个漏洞利用工具是：java反序列终极化工具.jar

我们先启动靶机环境，访问：[http://192.168.10.13:7001/](http://192.168.10.13:7001/ "http://192.168.10.13:7001/")

![](https://img-blog.csdnimg.cn/20190817155812634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开我们的 java反序列终极化工具.jar工具，选择Weblogic，输入目标主机的链接，点击目标信息，获取信息。等五六秒的样子，如果能出来目标主机的信息，则说明目标主机存在漏洞。如果没漏洞，会直接提示不存在漏洞，我们等待他的响应即可。

![](https://img-blog.csdnimg.cn/20191018105043897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行系统命令

![](https://img-blog.csdnimg.cn/20191018105643208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）](https://vulhub.org/#/environments/weblogic/CVE-2017-10271/ "Weblogic < 10.3.6 'wls-wsat' XMLDecoder 反序列化漏洞（CVE-2017-10271）") 

### Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）

Oracle 2018年4月补丁中，修复了Weblogic Server WLS Core Components中出现的一个[反序列化](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)漏洞（CVE-2018-2628），该漏洞通过t3协议触发，可导致未授权的用户在远程服务器执行任意命令。

相关文章：[Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）](https://vulhub.org/#/environments/weblogic/CVE-2018-2628/ "Weblogic WLS Core Components 反序列化命令执行漏洞（CVE-2018-2628）")

### Weblogic反序列化漏洞（CVE-2019-2725）

**影响版本：**

*   Oracle WebLogic Server 10.\*
*   Oracle WebLogic Server 12.1.3

这个漏洞利用工具是：Weblogic反序列化漏洞利用工具.jar

相关文章：[Weblogic反序列化远程代码执行漏洞（CVE-2019-2725）分析报告](https://www.freebuf.com/articles/web/202203.html "Weblogic反序列化远程代码执行漏洞（CVE-2019-2725）分析报告")

RMI反序列化漏洞
---------

RMI是Remote Method Invocation远程方法调用的简称，是J2SE的一部分，能够让程序员开发出基于JAVA的分布式应用。一个RMI对象是一个远程JAVA对象，可以从另一个JAVA虚拟机上（甚至跨过网络）调用它的方法，可以像调用本地JAVA对象的方法一样调用远程对象的方法，使分布在不同的JVM中的对象的外表和行为都像本地对象一样。对于任何一个以对象为参数的RMI接口，你都可以发一个自己构建的对象，迫使服务器端将这个对象按任何一个存在于class path中的可序列化类来反序列化。

1099 端口是Java RMI的默认端口，RMI默认使用序列化来完成所有的交互，所以这是非常常见的漏洞。如果该端口暴露在公网上，且使用了Apache Commons Collections的漏洞版本，就可以在该服务器上执行相关命令。

漏洞利用工具：RMI反序列化利用工具.jar

输入目标的 ip 和RMI的端口，然后 cmd 中输入命令，点击 Exeuate

![](https://img-blog.csdnimg.cn/20191018104624616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Fastjson反序列化漏洞
--------------

Fastjson是一个java编写的高性能功能非常完善的JSON库，应用范围非常广，Fastjson用于将Java Bean序列化为JSON字符串，也可以从JSON字符串反序列化到JavaBean。2017年3月15日，Fastjson官方主动爆出 Fastjson在1.2.24及之前版本存在远程代码执行高危安全漏洞。攻击者可以通过此漏洞远程执行恶意代码来入侵服务器。

受影响的Fastjson版本：Fastjson<=1.2.24

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)