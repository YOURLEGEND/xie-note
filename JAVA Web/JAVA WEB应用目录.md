**目录**

[Tomcat目录](#t0)

[MyEclipse目录](#t1)

[部署启动应用](#t2)

* * *

Tomcat目录
--------

首先，官方下载 tomcat 安装包，解压。得到下面的目录

![](https://img-blog.csdnimg.cn/20181119233838246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**bin** 目录是tomcat启动和关闭目录。startup.bat 启动tomcat 、shutdown.bat 关闭tomcat

![](https://img-blog.csdnimg.cn/20181119233923878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

webapps 目录存放着我们的网页文件，自带有其他几个文件夹。我们可以新建 myHome文件夹，然后在里面放入我们自己的网页文件 index.jsp

![](https://img-blog.csdnimg.cn/20181119234358806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在 myHome 文件夹里面，我们需要新建一个 WEB-INF 文件夹，它是java web应用的安全目录，就是浏览器无法访问其中的内容，只有通过服务器端的代码才能访问其中的内容。这里不明白也没有关系，在webapps文件夹中有一个examples文件夹，它里面有WEB-INF 文件夹，直接把它复制到myHome文件夹中就可以了

![](https://img-blog.csdnimg.cn/20181119234534312.png)

那么，**WEB-INF** 里面有哪些东西呢？

![](https://img-blog.csdnimg.cn/20190108092616168.png)

*   web.xml： 整个项目布署的配置文件。
*   classes： 我们整个项目由 .java 编译好的 .class 文件
*   lib： 我们整个项目用到的外部文件库，如 jar 包

那么，我们访问 [http://127.0.0.1:8080/myHome/index.jsp](http://127.0.0.1:8080/myHome/index.jsp) 就能访问我们myHome里面的文件了

![](https://img-blog.csdnimg.cn/20190108092058976.png)

MyEclipse目录
-----------

我们新建一个 my\_first\_web 的 Web Project。

![](https://img-blog.csdnimg.cn/20190512092550505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   src目录存放着我们的 java源代码
*   JRE System Library里面是JRE系统库，也就是java运行环境的库
*   Java EE 6 Libraries里面是Java EE6的库文件
*   WebRoot是web的根目录，也就是输出目录，其包含 META-INF 和 WEB-INF 子目录。html网页文件，css文件，js文件以及jsp文件都放在WebRoot目录下。

META-INF文件夹相当于一个信息包，目录中的文件和目录获得Java 2平台的认可与解释，用来配置应用程序、扩展程序、类加载器和服务。其中的 MANIFEST.MF文件，在用jar打包时自动生成。

WEB-INF 下的 classes 目录存放着我们写的 java 源代码编译好的 .class 文件

WEB-INF 下的 lib 库存放着我们这个项目所需要的库文件

### **部署启动应用**

将我们的应用部署到tomcat

![](https://img-blog.csdnimg.cn/20190108102241187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190512092420703.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这时，会自动的在tomcat的webapps目录下也新建一个myweb文件夹。并且[myeclipse](https://so.csdn.net/so/search?q=myeclipse&spm=1001.2101.3001.7020)下的WebRoot目录和tomcat的myweb目录是实时同步的，我们在Webroot目录下的所有修改都会自动同步到tomcat的myweb目录下。

启动应用

![](https://img-blog.csdnimg.cn/20190512092755306.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问

![](https://img-blog.csdnimg.cn/20190512092711800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Tomcat、JSP和Servlet](https://blog.csdn.net/qq_36119192/article/details/84279392)