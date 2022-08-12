**目录**

[Web服务器](#t0)

[中间件](#t1)

[容器](#t2)

[总结](#t3)

[Web开发的选择](#t4)

* * *

我们经常会被Web服务器、Web容器和Web中间件这三个概念搞混。因为我们常见的很多网站要么是由[IIS](https://so.csdn.net/so/search?q=IIS&spm=1001.2101.3001.7020)搭建，要么是由Apache、Tomcat、Ngnix搭建。所以，我们会把他们都叫成是Web服务器，因为他们都提供了Web服务，可以让我们通过http协议访问到。但是又有很多地方会说Tomcat是Servlet容器，而又有很多地方会说Tomcat是Web中间件！那么，他们到底是Web服务器还是容器，还是Web中间件呢？？？

怀着这个问题，我查遍了网上所有的资料，终于把这几个概念好好的整理了一下。

Web服务器
------

广义的**web服务器**（Web Server）：提供web服务的软件或主机，即Web服务器软件或装有Web服务器软件的计算机。例如：IIS、[apache](https://so.csdn.net/so/search?q=apache&spm=1001.2101.3001.7020)、nginx等。Web服务器可以处理 HTTP 协议，响应针对静态页面或图片的请求，进行页面跳转，或者把动态请求委托其它程序（它的扩展、某种语言的解释引擎(php)、Web容器）。

所以，我们可以把 IIS、Apache、Nginx、Tomcat等都称为Web服务器，因为他们都提供了web服务。

中间件
---

**中间件**（Middleware）是提供系统软件和应用软件之间连接的软件，以便于软件各部件之间的沟通。中间件处在操作系统和更高一级应用程序之间。他充当的功能是：将应用程序运行环境与操作系统隔离，从而实现应用程序开发者不必为更多系统问题忧虑，而直接关注该应用程序在解决问题上的能力 。容器就是中间件的一种。

而**Web中间件**就是提供web应用软件和系统软件连接的软件的一个总称。

**我们来看看J2EE提出的背景：**

1、企业级应用框架的需求：在许多企业级应用中，例如数据库连接、邮件服务、事务处理等都是一些通用企业需求模块，这些模块如果每次都由开发人员来完成的话，将会造成开发周期长和代码可靠性差等问题。于是许多大公司开发了自己的通用模块服务。这些服务性的软件系列统称为中间件。

2、 为了通用必须要提出规范，不然无法达到通用：在上面的需求基础之上，许多公司都开发了自己的中间件，但其与用户的沟通都各有不同，从而导致用户无法将各个公司不同的中间件组装在一块为自己服务。从而产生瓶颈。于是提出标准的概念。其实**J2EE**就是基于JAVA技术的一系列标准。

并且中间件技术已经不局限于应用服务器、数据库服务器。围绕中间件，在商业中间件及信息化市场主要存在微软阵营、Java阵营、开源阵营。

所以说我们可以把Tomcat、Weblogic、Jboss等叫做Web中间件。

相关文章：[Web中间件](https://blog.csdn.net/qq_36119192/article/details/84261392)

容器
--

**容器**：容器是中间件的一种，作为操作系统和应用程序之间的桥梁，给处于其中的应用程序组件提供一个环境，使应用程序直接跟容器中的环境变量交互，而不必关注其它系统问题。例如：tomcat（[servlet](https://so.csdn.net/so/search?q=servlet&spm=1001.2101.3001.7020)容器），Jboss(EJB容器)。这些容器提供的接口严格遵守J2EE规范中的web application 标准。

**web容器**：我们把遵守J2EE规范标准的web服务器就叫做J2EE中的web容器。处理 web服务的容器，例如tomcat（servlet容器），IIS(asp容器)。web容器用于给处于其中的应用程序组件（ASP、JSP）提供一个环境，是中间件的一个组成部分，它实现了对动态语言的解析。比如tomcat可以解析jsp，是因为其内部有一个servlet容器。

**EJB容器**(Enterprise java bean )：更具有行业领域特色。他提供给运行在其中的组件EJB各种管理功能。只要满足J2EE规范的EJB放入该容器，马上就会被容器进行高效率的管理。并且可以通过现成的接口来获得系统级别的服务。例如邮件服务、事务管理。

WEB容器和EJB容器在原理上是大体相同的，更多的区别是被隔离的外界环境。WEB容器更多的是跟基于HTTP的请求打交道。而 EJB容器不是。它是更多的跟数据库、其它服务打交道。但他们都是把与外界的交互实现从而减轻应用程序的负担。例如servlet不用关心http的细 节，直接引用环境变量session,request,response就行、EJB不用关心数据库连接速度、各种事务控制，直接由容器来完成。

所以说，我们也可以把 IIS(asp容器)、Tomcat(servlet容器)、Jboss(EJB容器)等叫做Web容器

总结
--

通过上面的解释，不知道你有没有搞清楚这几者之间的区别！其实，对于这些名词，也并没有一个十分清楚的概念说这个就是属于这个，而不能属于那个。他们之间很多是包含关系。

```
web服务器：IIS、Apache、Nginx、Tomcat、Jboss、Jetty、Weblogic、Webshere、Glasshfish、Lighttpd等      
web中间件：Tomcat、Jboss、Jetty、Weblogic、Webshere、Glasshfish等      
web容器：IIS(asp容器)、Tomcat(servlet容器)、Jboss(EJB容器)
```


所以应该是  **web服务器>web中间件>web容器**

Web开发的选择
--------

*   一般本地开发的话，小项目，或者是个人开发建议的话，php使用apache、asp使用IIS、jsp使用tomcat
*   大型的项目就用 jboss 或 webloigc
*   大项目或者商业项目一般采用：weblgoic/webshere，其他的还有 jboss、glasshfish 等

相关文章：[Web中间件](https://blog.csdn.net/qq_36119192/article/details/84261392)

  
  
 

文章知识点与官方知识档案匹配，可进一步学习相关知识

[云原生入门技能树](https://edu.csdn.net/skill/cloud_native/cloud_native-3a407b55039e4e29b802e5047caf881b)[容器编排(生产环境 k8s)](https://edu.csdn.net/skill/cloud_native/cloud_native-3a407b55039e4e29b802e5047caf881b)[kubelet,kubectl,kubeadm三件套](https://edu.csdn.net/skill/cloud_native/cloud_native-3a407b55039e4e29b802e5047caf881b)3204 人正在系统学习中