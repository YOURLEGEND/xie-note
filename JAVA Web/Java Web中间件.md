**目录**

[中间件](#t0)

[常见的web中间件有哪些](#t1)

[Tomcat](#t2)

[Weblogic](#t3)

[Jboss](#t4)

[Jetty](#t5)

[Webshere](#t6)

[Glasshfish](#t7)

* * *

中间件
===

我们经常会看到中间件，但是，一直好奇的是，中间件到底是什么？

**中间件**（英语：Middleware）是提供系统软件和应用软件之间连接的软件，以便于软件各部件之间的沟通。中间件处在操作系统和更高一级应用程序之间。他充当的功能是：将应用程序运行环境与操作系统隔离，从而实现应用程序开发者不必为更多系统问题忧虑，而直接关注该应用程序在解决问题上的能力 。容器就是中间件的一种。

也就是说，关于中间件，我们可以理解为：是一类能够为一种或多种应用程序合作互通、资源共享，同时还能够为该应用程序提供相关的服务的软件。（注意：中间件是一类软件的总称，不是单独的一个软件）

我们经常管web中间件叫做web服务器或者web容器

正常情况下一次web的访问顺序是：web浏览器---服务器(硬件)---web容器---web应用服务器---数据库服务器。

常见的web中间件有哪些
------------

![](https://img-blog.csdnimg.cn/20181216201920161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Tomcat

**Tomcat** 是Apache 软件基金会（Apache Software Foundation）的Jakarta 项目中的一个核心项目，由Apache、Sun 和其他一些公司及个人共同开发而成。因为Tomcat 技术先进、性能稳定，而且免费，因而深受Java 爱好者的喜爱并得到了部分软件开发商的认可，成为目前比较流行的**Java Web 应用服务器(Servlet 容器)**。实际上Tomcat 部分是Apache 服务器的扩展，但它是独立运行的，所以当你运行tomcat 时，它实际上作为一个与Apache 独立的进程单独运行的。Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。Tomcat默认使用 **8080** 号端口

相关文章：[Linux中Tomcat的安装和部署](https://blog.csdn.net/qq_36119192/article/details/82797114)    

                  [Tomcat容器、JSP和Servlet](https://blog.csdn.net/qq_36119192/article/details/84279392)

                  [Windows中tomcat目录详解](https://blog.csdn.net/qq_36119192/article/details/84262247)

### Weblogic

**WebLogic** 是美国Oracle公司出品的一个application server，确切的说是一个基于JAVAEE架构的中间件，[WebLogic](https://so.csdn.net/so/search?q=WebLogic&spm=1001.2101.3001.7020)是用于开发、集成、部署和管理大型分布式Web应用、网络应用和数据库应用的Java应用服务器。将Java的动态功能和Java Enterprise标准的安全性引入大型网络应用的开发、集成、部署和管理之中。Weblogic默认端口是 **7001**。

### Jboss

**Jboss** 是一个基于Java EE的开放源代码的应用服务器。 它不但是**Servlet容器**，而且也是**EJB容器**，从而受到企业级开发人员的欢迎，从而弥补了Tomcat只是一个Servlet容器的缺憾。JBoss是一个管理EJB的容器和服务器。但JBoss核心服务不包括支持 servlet/JSP 的WEB容器，一般与 Tomcat 或 Jetty 绑定使用。Jboss默认端口号是**8080**。

相关文章：[JBOSS的安装](https://blog.csdn.net/qq_36119192/article/details/82797114#JBoss%E7%9A%84%E5%AE%89%E8%A3%85)

### Jetty

**Jetty** 是一个开源的**servlet容器**，它为基于Java的web容器，例如JSP和servlet提供运行环境。Jetty是使用JAVA编写的，它的API以一组JAR包的形式发布。开发人员可以将Jetty容器实例化成一个对象，可以迅速为一些独立运行（stand-alone）的Java应用提供网络和web连接。

### Webshere

**WebShere** 是 IBM 的软件平台。它包含了编写、运行和监视全天候的工业强度的随需应变 Web 应用程序和跨平台、跨产品解决方案所需要的整个中间件基础设施，如服务器、服务和工具。WebSphere 提供了可靠、灵活和健壮的软件。WebSphere 是一个模块化的平台，基于业界支持的开放标准。可以通过受信任和持久的接口，将现有资产插入 WebSphere，可以继续扩展环境。WebSphere 可以在许多平台上运行，包括 Intel、Linux 和 z/OS。Webshere默认端口号是 **9080**。

### Glasshfish

**GlassFish** 是一款强健的商业兼容应用服务器，达到产品级质量，可免费用于开发、部署和重新分发。开发者可以免费获得源代码，还可以对代码进行更改

相关文章：[web服务器、Web中间件和Web容器的区别](https://blog.csdn.net/qq_36119192/article/details/84501439)