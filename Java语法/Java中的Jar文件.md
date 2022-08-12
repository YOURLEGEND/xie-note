**目录**

[Jar文件](#t0)

[使用java命令打包jar文件](#t1)

[使用IDEA打包Jar文件](#t2)

[运行jar包](#t3)

[如何在项目中导入外部 jar 文件](#t4)

* * *

Jar文件
-----

Jar(Java Archive File)，是 Java 的一种文档格式。Jar 文件非常类似ZIP 文件——准确的说，它就是 ZIP 文件，所以叫它文件包。JAR 文件与 ZIP 文件唯一的区别就是在 JAR 文件的内容中，包含了一个 META-INF/MANIFEST.MF 文件，这个文件是在生成 JAR 文件的时候自动创建的。jar文件是一种封装，使用jar文件的人不需要知道jar文件中有多少个.class文件，每个文件中的功能与作用，只需要提供的接口即可。

*   jar包：通常是开发时要引用通用类，打成包便于存放管理。
*   war包：做好一个web应用后，通常是网站，打成包部署到容器中。
*   ear包：企业级应用，通常是EJB打成ear包。

### 使用java命令打包jar文件

新建test.java文件，然后将其编译成class文件

![](https://img-blog.csdnimg.cn/20200719165625823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

新建src目录，将生成的test.class文件移动到src目录中去

![](https://img-blog.csdnimg.cn/20200719165706472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

新建一个文件，名字可以任意起。我这里就叫manifest，内容如下。注意这里的冒号后面要有空格，然后每行需要有换行符。这个文件用于指定加载的主类

```
Manifest-Version: 1.0      
Main-Class: test
```


![](https://img-blog.csdnimg.cn/20200719165733971.png)

然后执行以下命令进行打包成jar文件,

```
jar -cvfm test.jar manifest -C src/ .
```


*   jar后面的-m选项会将第2步新建的文件合并到 jar包中的 META-INF/MANIFEST.MF，也就是更新清单配置文件。
*   \-C 后面指定要打包的目录， 目录后面的 . 代表目录下所有文件

![](https://img-blog.csdnimg.cn/20200719165816625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用IDEA打包Jar文件

使用IDEA创建了一个 Jar\_test 项目后，我们随便创建了四个类。其中，Main.java是主类。

![](https://img-blog.csdnimg.cn/20200630224116175.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后右键——>打开模块设置

![](https://img-blog.csdnimg.cn/20200630222653709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200630222715965.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击 Artifacts——>JAR——>From modules with dependencies

![](https://img-blog.csdnimg.cn/20200630221646364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后选择项目模块和这个项目的主类

![](https://img-blog.csdnimg.cn/20200630222802850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

点击 生成——> Build Artifacts

![](https://img-blog.csdnimg.cn/2020063022290759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200630222934392.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在 out\\artifacts\\ 目录下就会生成 Jar\_test.jar 文件

![](https://img-blog.csdnimg.cn/20200630223041905.png)

将Jar\_test.jar文件解压缩后内容如下：

![](https://img-blog.csdnimg.cn/20200630224215951.png)

那几个类都被编译成class文件了。

![](https://img-blog.csdnimg.cn/2020063022420894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 运行jar包

我们经常看到会使用 java -cp xx.jar 和 java  -jar  xx.jar 命令运行jar包。 

**java -cp**

java -cp 和 -classpath 一样，是指定类运行所依赖其他类的路径，通常是类库和jar包，需要全路径到jar包，多个jar包之间连接符：window上分号 “;”  、Linux下使用 “:”。

```
windows环境：      
    java -cp .;d:\work\other.jar;d:\work\my.jar packname.mainclassname        
linux环境：      
    java -cp .:/hone/myuser/work/other.jar:/hone/myuser/work/my.jar packname.mainclassname        
表达式支持通配符，例如：      
    java -cp .;c:\work\my.jar;c:\work\*.jar packname.mainclassname       
    java -cp .:/home/myuser/work/lib/my.jar:/home/myuser/work/dependency_jars/*.jar packname.mainclassname
```


**java -jar**

执行该命令时，会用到目录 META-INF\\MANIFEST.MF 文件，在该文件中，有一个叫Main-Class的参数，它说明了java -jar命令执行的类。java -jar方式不可以指定附加依赖jar包。

**备注：**

*   打包时指定了主类，可以直接用java -jar {xxx.jar}。
*   打包时没有指定主类，可以用 java -cp {xxx.jar} {主类名称的绝对路径}，如：java -cp ysoserial-0.0.6-SNAPSHOT-BETA-all.jar ysoserial.exploit.JRMPListener 1099
*   要引用其他的 jar 包，可以用 java  -cp   {$CLASSPATH}:{xxxx.jar}  {主类名称的绝对路径}。其中 -classpath 指定需要引入的类。

### 如何在项目中导入外部 jar 文件

ctrl+shirt+alt+s 组合键打开，然后 Modules——>Dependencies，然后右边的 + 号

![](https://img-blog.csdnimg.cn/20200721233820152.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后选择第1个JARs or directories，然后选择jar文件的路径，确定即可。

![](https://img-blog.csdnimg.cn/20200721233943401.png)

![](https://img-blog.csdnimg.cn/20200721234100214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200721234216746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)