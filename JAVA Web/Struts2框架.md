Struts2
-------

**Struts2**是一个基于[MVC](https://so.csdn.net/so/search?q=MVC&spm=1001.2101.3001.7020)设计模式的Web应用框架，它本质上相当于一个servlet，在MVC设计模式中，Struts2作为控制器(Controller)来建立模型与视图进行数据交互。Struts 2以WebWork为核心，采用拦截器的机制(拦截.action的请求)来处理用户的请求，这样的设计也使得业务逻辑控制器能够与Servlet API完全脱离开。

### 添加Struts2框架支持

右键项目——>MyEclipse——>Add [Struts](https://so.csdn.net/so/search?q=Struts&spm=1001.2101.3001.7020) Capabilities

![](https://img-blog.csdnimg.cn/20190512170848378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190512170908354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

取消勾选 MyEclipse Libraries，然后勾选 User Libraries，点 Finish

![](https://img-blog.csdnimg.cn/20190512165435439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

则项目中自动添加了以下东西：

(1)  web.xml，web.xml里添加了一个filter，该filter拦截了 \*.action请求，交由struts2[框架](https://so.csdn.net/so/search?q=%E6%A1%86%E6%9E%B6&spm=1001.2101.3001.7020)处理

![](https://img-blog.csdnimg.cn/20190512165555123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

(2) 在src目录下，多了一个 struts.xml配置文件

![](https://img-blog.csdnimg.cn/20190512165652590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们再将struts相关的jar包复制到 /WEB-INF/lib 目录下，即可。

![](https://img-blog.csdnimg.cn/20190512165841403.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

未完待续。。