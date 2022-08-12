**目录**

[JSP](#t0)

[Tomcat、JSP和Servlet](#t1) 

* * *

JSP
---

**JSP**全名为Java Server Pages，其根本是一个简化的[Servlet](https://so.csdn.net/so/search?q=Servlet&spm=1001.2101.3001.7020)设计。JSP技术有点类似ASP技术，它是在传统的HTML网页中插入Java程序段和JSP标记，从而形成JSP文件，后缀名为(\*.jsp)。 用JSP开发的Web应用是跨平台的，既能在Linux下运行，也能在其他操作系统上运行。

它实现了Html语法中的java扩展（以 <%, %>形式）。JSP与Servlet一样，是在服务器端执行的。通常返回给客户端的就是一个纯HTML文本，因此客户端只要有浏览器就能浏览。

JSP其实就是在HTML中插入了JAVA代码，JAVA代码插在 <%  %> 标签中

示例： index.jsp

```
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>      
<%      
String path = request.getContextPath();      
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";      
%>      
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">      
<html>      
  <head>      
    <base href="<%=basePath%>">       1
    <title>My JSP 'index.jsp' starting page</title>       1
	<meta http-equiv="pragma" content="no-cache">      1
	<meta http-equiv="cache-control" content="no-cache">      1
	<meta http-equiv="expires" content="0">          1
	<meta http-equiv="keywords" content="keyword1,keyword2,keyword3">      1
	<meta http-equiv="description" content="This is my page">      1
  </head>      1
  <body>      2
    <% out.println("Hello,World!"); %>         #页面输出，java语法      2
  </body>      2
</html>
```


![](https://img-blog.csdnimg.cn/2018112008574570.png)

**JSP页面的执行过程**

*    浏览器接收到的是纯HTML，不含JAVA代码
*   所有的嵌入代码段 <%  %> 均由服务器端处理
*   <% %>里面的是Java代码，要符合Java的语法

![](https://img-blog.csdnimg.cn/2018112009080817.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Tomcat、JSP和Servlet 
-------------------

首先，JSP 是在HTML中插入了JAVA代码，用于前端页面的显示！

那么，我们后端是如何处理的呢？后端我们是利用 Servlet 来处理前端的数据和逻辑，当我们新建一个Servlet的时候，其实就是新建一个 .java　文件，然后运行的时候，JDK会把  .java文件编译成 .class 文件，用于处理前端的请求数据和逻辑。

Servlet是Java提供的用于开发Web服务器应用程序的一个组件，运行在服务器端，由Servlet容器所管理，用于生成动态的内容。Servlet是平台独立的Java类，编写一个Servlet，实际上就是按照Servlet规范编写一个Java类

而Tomcat是servlet的容器，Servlet运行在Tomcat容器当中(如docker镜像运行在docker容器当中)，Servlet容器有很多，如Tomcat、Jboss、Weblogic 等

相关文章：[Windows中tomcat目录详解](https://blog.csdn.net/qq_36119192/article/details/84262247)  
 

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-20cad95081b4475aaac1c1ebf7af0253)[深入研究容器](https://edu.csdn.net/skill/java/java-20cad95081b4475aaac1c1ebf7af0253)[Collection的功能方法](https://edu.csdn.net/skill/java/java-20cad95081b4475aaac1c1ebf7af0253)44190 人正在系统学习中