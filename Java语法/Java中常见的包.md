**目录**

[JDK自带的包](#t0)

[第三方包](#t1)

* * *

### JDK自带的包

JAVA提供了强大的应用程序接口，即JAVA类库。他包含大量已经设计好的工具类，帮助程序员进行[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)处理、绘图、数学计算和网络应用等方面的工作。下面简单介绍JAVA核心类库中的基础包

JDK1.8.0中默认有以下的包

![](https://img-blog.csdnimg.cn/20181218150435262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   **java.applet**：java.applet包是用来实现运行于Internet浏览器中的JAVA APPLET的工具类库。它包含于产生Applet的类和用于Applet通信的类。Applet类称为小应用程序类，通常所说的Applet程序必须集成该类，Applet是一种专门化的面板，需要嵌入到HTML网页中，由与JAVA语言兼容的浏览器执行。
*   **java.awt：**java.awt包是JAVA语言用来构建图形用户界面（GUI）的类库，包括许多界面元素和资源。java.awt包提供JAVA语言中的图形类、组成类、容器类、排列类、几何类、事件类和工具类等。
*   **java.io：**java.io包提供了一系列用来读写文件或其他的输入输出流。其中有基本输入/输出类、缓存流类、比如数组字符串流类、数据流类、文件流类、管道类、流连接类和异常类等
*   **java.lang：**在所有的JAVA API类库中，java.lang包是最重要的，它提供了JAVA语言的核心类库，包含了运行JAVA程序必不可少的系统类，如：基本数据类型、基本数学函数、字符串处理、线程管理和异常处理类等。运行JAVA程序时，系统会自动加载java.lang包，即这个包的加载是默认的
*   **java.math**：java.math包提供了对数学方面操作的类
*   **java.net：**java.net包包含一些与网络相关的类和接口，以方便应用程序在网络上传输信息。如主机名解析类、实现套接字通信的Socket类和ServerSocket类、资源定位器（URL）类等
*   **java.sql：**java.sql包提供了对数据库进行操作的类
*   **java.time：**java.time包是在JDK8新引入的，提供了用于日期、时间、实例和周期的主要类
*   **java.util：**java.util包提供了JAVA语言中的一些低级的实用工具，如数据结构类、日期类、随机数类、属性类、观测类和异常类等

### 第三方包

**apache commons collections**：Apache Commons Collections 是一个扩展了Java标准库里Collection结构的第三方基础库，它提供了很多强有力的数据结构类型并且实现了各种集合工具类。作为 Apache 开源项目的重要组件，Commons Collections被广泛应用于各种Java应用的开发。