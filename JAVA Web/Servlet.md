**目录**

[新建一个Servlet](#t0)

[后台处理流程](#t1)

[删除Servlet](#t2)

[JSP和Servlet的联系和区别](#t3)

[MVC架构](#t4)

* * *

**Servlet**（Server [Applet](https://so.csdn.net/so/search?q=Applet&spm=1001.2101.3001.7020)）是Java Servlet的简称，称为小服务程序或服务连接器，是用Java编写的服务器端程序，具有独立于平台和协议的特性，主要功能在于交互式地浏览和生成数据，生成动态Web内容。

狭义的[Servlet](https://so.csdn.net/so/search?q=Servlet&spm=1001.2101.3001.7020)是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。Servlet运行于支持Java的应用服务器中。从原理上讲，Servlet可以响应任何类型的请求，但绝大多数情况下Servlet只用来扩展基于HTTP协议的Web服务器。

### 新建一个Servlet

我们新建一个 HelloServlet，它会自动生成内容，默认继承于 HttpServlet。

![](https://img-blog.csdnimg.cn/20190512104906596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

它会自动在 web.xml 中加入其配置信息

![](https://img-blog.csdnimg.cn/20190512151307262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们以 GET 方式访问该 Servlet

![](https://img-blog.csdnimg.cn/20190512102948294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

发现页面返回的是该 Servlet 里面 doGet 函数的内容。如果我们是以POST方式访问该 Servlet，则返回的是该Servlet里 doPost函数的内容。

![](https://img-blog.csdnimg.cn/20190512103043192.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

创建 Servlet 的时候，除了doGet() 和 doPost() 函数，默认还会创建 init() 函数和 destory() 函数。

*   init() : 初始化，当第一次请求到来时，创建 Servlet 对象，并调用 init() 函数进行初始化。
*   destroy(): 当tomcat退出时被调用

注：HelloServlet只创建一个实例，init() 只调用一次。所以，Servlet 类里一般不保存成员变量，如果有成员变量，也是用来保存一些全局的公共配置

### 后台处理流程

1.  接收浏览器 URL 的请求，访问 /servlet/HelloServlet
2.  在配置文件 web.xml 中的<servlet-mapping>里查找，确定由 HelloServlet 来处理
3.  从<servlet> 里查找，确定对应的类是my.HelloServlet

### 删除Servlet

*   删除 HelloServlet 类
*   删除 WEB-INF\\classes\\ 下的 HelloServlet.class文件
*   在 web.xml 里删除相关配置（否则tomcat在启动时会提示错误)

### JSP和Servlet的联系和区别

*   JSP是前端的页面，是在HTML中插入了JAVA代码，而Servlet是后端用纯JAVA写的处理流程。
*   JSP更擅长于页面表示，而Servlet更擅长于逻辑控制。
*   Servlet中没有内置对象，JSP中的内置对象都是必须通过HttpServletRequest对象，HttpServletResponse对象以及HttpServlet对象得到。
*   JSP的本质就是Servlet，JSP是 Servlet 的一种简化，使用JSP只需要完成程序员需要输出到客户端的内容。 而Servlet则是个完整的Java类。这个类的Service方法用于生成对客户端的响应，Servlet在Java代码中通过HttpServletResponse对象动态输出HTML内容到JSP中。

 **Servlet和JSP最主要的不同点在于**： Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。 而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件。 JSP侧重于视图，Servlet主要用于控制逻辑。 Servlet更多的是类似于一个Controller，用来做控制。

**JSP和Servlet各自的缺点：**

*   Servlet能够很好地组织业务逻辑代码，但是在Java源文件中通过字符串拼接的方式生成动态HTML内容会导致代码维护困难、可读性差
*   JSP虽然规避了Servlet在生成HTML内容方面的劣势，但是在HTML中混入大量、复杂的业务逻辑同样也是不可取的

### MVC架构

既然JSP和Servlet都有自身的适用环境，那么能否扬长避短，让它们发挥各自的优势呢？答案是肯定的——[MVC](https://so.csdn.net/so/search?q=MVC&spm=1001.2101.3001.7020)(Model-View-Controller)模式非常适合解决这一问题。

MVC模式（Model-View-Controller）是软件工程中的一种软件[架构](https://so.csdn.net/so/search?q=%E6%9E%B6%E6%9E%84&spm=1001.2101.3001.7020)模式，即 模型(Model)—视图(View)—控制器(Controller)。常见的MVC框架有：Struts、Spring、SpringMVC等。

把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）：

*   模型（Model）：业务功能编写（例如算法实现）、数据库设计以及数据存取操作实现。模型是应用程序的主体部分。模型代表了业务数据和业务执行逻辑。当数据发生变化时，负责通知视图部分。一个模型可以同时为多个视图提供数据，由于同一个模型可以被多个视图重用，所以提高了应用的可重用性。
*   视图（View）：负责界面显示，使用的开发语言为jsp、js、html+css等。视图是用户看到并与之交互的页面，视图向客户显示相关的业务数据，并能接收用户的输入。视图层并不执行实际的业务逻辑，也不改变数据模型。它能接收模型发出的数据更新请求，从而对用户界面进行同步更新；
*   控制器（Controller）：负责转发请求，对请求进行处理。主要用于前端web请求的分支，调度后台的业务逻辑执行，可以通过继承Struts的Action实现。当web用户单击web页面中的提交按钮来发送html表单时，控制器接收请求并调用相应的模型组件去处理请求，然后调用相应的视图来显示模型返回的数据；

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS8yMjljZjlmZjViMTcyOWVhZjQwOGZhYzU2MjM4ZWViM19iLnBuZw?x-oss-process=image/format,png)

**MVC开发模式的数据走向**

*   Web浏览器发送HTTP请求到服务端，被Controller(Servlet)控制器获取并进行处理（例如参数解析、请求转发）
*   Controller(Servlet)控制器调用核心业务逻辑——模型(Model)部分，获得结果
*   Controller(Servlet)控制器将Model返回的处理结果交给 View（JSP）
*   View视图层将动态生成的HTML内容返回到浏览器显示

MVC模式在Web开发中的好处是非常明显，它规避了JSP与Servlet各自的短板，Servlet只负责业务逻辑而不会动态生成HTML代码；JSP中也不会充斥着大量的业务代码。这大大提高了代码的可读性和可维护性。

参考文章：[jsp和servlet的区别和联系](https://www.cnblogs.com/sanyouge/p/7325656.html)