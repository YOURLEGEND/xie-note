在做渗透的期间，JAVA Web类型的网站是见过最多的(大部分的国企，大型企业都是用的JAVA Web)，也是最难的，因为涉及到JAVA Web开发方面众多的[框架](https://so.csdn.net/so/search?q=%E6%A1%86%E6%9E%B6&spm=1001.2101.3001.7020)。由于对JAVA Web方面的知识不是很了解，所以特此整理一番。

JAVA Web网站最著名的框架型漏洞就是 **Structs2远程代码执行漏洞** 和 **反序列化漏洞** 了！

**JSP**：它是在传统的[网页](http://baike.baidu.com/item/%E7%BD%91%E9%A1%B5)HTML文件(\*.htm，\*.[html](http://baike.baidu.com/item/html))中插入Java[程序段](http://baike.baidu.com/item/%E7%A8%8B%E5%BA%8F%E6%AE%B5)和JSP标记(tag)，从而形成JSP文件，后缀名为(\*.jsp)。 用JSP开发的Web应用是[跨平台](http://baike.baidu.com/item/%E8%B7%A8%E5%B9%B3%E5%8F%B0)的，既能在Linux下运行，也能在其他操作系统上运行，因为它是运行在java虚拟机中的，前端页面的后缀是 .jsp

**Java Web常见开发框架模型：**

*   MVC框架模式
*   ORM框架模式

传送门：[Java常见开发框架](https://blog.csdn.net/qq_36119192/article/details/104331415)

**JAVA Web前后端开发语言：**

*   **前端**：HTML、CSS、JavaScript、Jquery、BootStrap、Ext.js 等
*   **后端**：Servlet、Struct2、Spring、Hibernate、Mybatis

**Java Web的常用开发工具：**

*   MyEclipse(收费软件)
*   IDEA(收费软件)

**Java Web常用容器：**

*   Tomcat 
*   Weblogic 
*   Jboss
*   WebSphere
*   Jetty

传送门：[Java Web中间件](https://blog.csdn.net/qq_36119192/article/details/84261392)

****前端：****

*   HTML：静态页面的编写
*   CSS：给页面装饰
*   JavaScript: 给页面增加动态的效果
*   JQuery: JavaScript框架包，它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作、事件处理、动画设计和Ajax交互
*   BootStrap: Bootstrap是基于HTML5和CSS3开发的，它在jQuery的基础上进行了更为个性化的完善，形成一套自己独有的网站风格，并兼容大部分jQuery插件。Bootstrap提供了优雅的HTML和CSS规范

****后端：****

*   Servlet：用Java编写的服务器端程序，主要功能在于交互式地浏览和修改数据，生成动态Web内容。
*   JSON: 一种轻量级的数据交换格式。它基于 [ECMAScript](https://baike.baidu.com/item/ECMAScript) (w3c制定的js规范)的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言
*   Struts2：基于MVC设计模式的Web应用框架，它本质上相当于一个servlet，在MVC设计模式中，Struts2作为控制器(Controller)来建立模型与视图的数据交互
*   Hibernate：Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，它将POJO与数据库表建立映射关系，是一个全自动的orm框架，hibernate可以自动生成SQL语句，自动执行，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库
*   Af-restful：一种软件架构风格，设计风格而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

未完待续。。