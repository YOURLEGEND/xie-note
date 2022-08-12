**目录**

[JSP工作原理](#t0)

[新建一个JSP页面](#t1)

[import库文件](#t2)

[JSP指令](#t3)

[JSP的运行](#t4)

* * *

JSP工作原理
-------

jsp是前端页面

我们知道，Java Web应用的后缀文件是 .jsp。那么 .jsp 文件是如何工作的呢？

首先，.jsp 文件是在 WebRoot目录下

![](https://img-blog.csdnimg.cn/20190512093737559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们访问该Java Web应用，[http://127.0.0.1:8080/my\_first\_web/](http://127.0.0.1:8080/my_first_web/)  默认是访问的 index.jsp 文件。即：[http://127.0.0.1:8080/my\_first\_web/index.jsp](http://127.0.0.1:8080/my_first_web/index.jsp)

![](https://img-blog.csdnimg.cn/20190512093817440.png)

那么，当我们的浏览器访问的是 .jsp 文件，是如何工作的呢？

**JSP**:  可以认为是嵌入了Java代码 (<%   %>) 的HTML页面，在Jsp中的Java代码可以调用Java API、Tomcat API或者以及自己写的类。

![](https://img-blog.csdnimg.cn/20190512093935669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   浏览器接收到的是纯HTML，不含JAVA代码
*   所有的嵌入代码段 <% %> 均由服务器端先处理和替换，从上到下依次执行，多段代码就可以像一个函数里的多个语句
*   JSP文件中的<%  %>里面的是Java代码，要符合Java的语法，在一个JSP页面里，可以有多段<% %>代码

### 新建一个JSP页面

我们新建一个 test.jsp 页面

![](https://img-blog.csdnimg.cn/20190512095132176.png)

该页面默认会有内容，我们在该页面加入以下代码

![](https://img-blog.csdnimg.cn/20190512095327931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

该代码的文件意思是 得到服务器的时间，并显示出来

![](https://img-blog.csdnimg.cn/20190512095442215.png)

### import库文件

由于代码里使用了 java.text.SimpleDateFormat ，所以要 import 这个库。

```
<%@ page import="java.text.*" %>      
<%@ page import="java.io.*" %>
```


可以写多行，也可以一行内用逗号分开，例如

```
<%@ page import="java.text.*, java.io.*" %>
```


### JSP指令

把 %@ 开头的称为JSP指令(directive)  
**例如：**

```
page指令：    <%@ page … %>      
include指令： <%@ include ... %>      
taglib指令：  <%@ taglib ... %>
```


其中，page指令比较常用，其参数常有：

```
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
```


### JSP的运行

在一个JSP页面里，可以有多段<% %>代码，它们可以分散来写，穿插在<html>里面。然后Tomcat在处理java代码的时候，会从上到下依次执行。

**JSP表达式**

当需要输出一个表达式的值时，可以采用<%= %>简化写法，JSP表达式里面不加分号

```
<p> 今天是 <% out.print(strNow); %> ，欢迎登录！ </p>      
<p> 今天是 <%= strNow %> ，欢迎登录！ </p>
```
