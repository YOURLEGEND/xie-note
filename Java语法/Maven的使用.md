**目录**

[Maven](#t0)

[Maven项目目录结构](#t1)

[下载配置Maven环境](#t2)

[建立Maven本地仓库](#t3)

[使用Maven创建Maven项目](#t4)

[使用IDEA创建Maven项目](#t5)

* * *

Maven
-----

Maven是Apache下的一个纯Java开发的开源项目，Maven是专门用于构建和管理Java相关项目的工具，其可以对 Java 项目进行构建、依赖管理。Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。Maven也可以用于构建和管理各种项目，例如：C#、Ruby、Scala和其他语言编写的项目。

### Maven项目目录结构

Maven提倡使用一个共同的标准目录结构，Maven使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。

| 目录 | 目的 |
| --- | --- |
| ${basedir} | 存放pom.xml和所有的子目录 |
| ${basedir}/src/main/java | 项目的java源代码 |
| ${basedir}/src/main/resources | 项目的资源，比如说property文件，springmvc.xml |
| ${basedir}/src/test/java | 项目的测试类，比如说Junit代码 |
| ${basedir}/src/test/resources | 测试用的资源 |
| ${basedir}/src/main/webapp/WEB-INF | web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面 |
| ${basedir}/target | 打包输出目录 |
| ${basedir}/target/classes | 编译输出目录 |
| ${basedir}/target/test-classes | 测试编译输出目录 |
| Test.java | Maven只会自动运行符合该命名规则的测试类 |
| ~/.m2/repository | Maven默认的本地仓库目录位置 |

使用Maven管理的Java 项目都有着相同的项目结构

*   有一个pom.xml 用于维护当前项目都用了哪些jar包
*   所有的java代码都放在 src/main/java 下面
*   所有的测试代码都放在src/test/java 下面 

![](https://img-blog.csdnimg.cn/20200703232245104.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 下载配置Maven环境

访问官方：[http://maven.apache.org/download.cgi](http://maven.apache.org/download.cgi)  下载Maven包，不需要安装，直接放在D盘即可。然后将 D:\\apache-maven-3.6.3\\bin 目录添加到环境变量。

查看是否配置成功，直接在cmd窗口输入命令：mvn -v

![](https://img-blog.csdnimg.cn/20200702232341187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 建立Maven本地仓库

所谓的仓库就是用于存放项目需要的jar包的，maven采用一个仓库，多个项目的方式，让多个项目共享一个仓库里的相同jar包。

**修改仓库默认位置**

仓库默认位置在 apache-maven-3.6.3\\conf\\settings.xml，在52行指定了仓库的位置：${user.home}/.m2/repository

对应于：C:\\Users\\xx\\.m2\\repository 。我们在settings.xml配置文件第55行加入下面代码，指定仓库位置

```
<localRepository>d:/maven/repository</localRepository>
```


![](https://img-blog.csdnimg.cn/20200702233548577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**配置Maven默认下载路径**

maven 默认从maven官方提供的服务器下载jar包，而官方服务器在国外，网速很慢，而且容易卡断。 为了便于快速下载相关jar包，可以使用国内maven 阿里云的下载地址。

配置Maven阿里云下载路径如下：

打开文件：apache-maven-3.6.3\\conf\\settings.xml

在mirrors里新加一个阿里云的镜像地址：

```
<mirror>      
            <id>alimaven</id>      
            <mirrorOf>central</mirrorOf>      
            <name>aliyun maven</name>      
            <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>      
</mirror>
```


![](https://img-blog.csdnimg.cn/20200703231336372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**使用现成的Maven库包**

下载链接：[https://download.how2j.cn/1748/repository.rar](https://download.how2j.cn/1748/repository.rar)

将其下载解压在 d:/maven/repository 目录下，这样就不需要自己下载maven仓库里面的一些库包了。

### 使用Maven创建Maven项目

执行以下命令创建Maven项目

```
mvn archetype:generate -DgroupId=com.company -DartifactId=maven_project -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```


*       archetype:generate 表示创建个项目
*       -DgroupId 项目包名: com.how2java
*       -DartifactId 项目名称: j2se
*       -DarchetypeArtifactId 项目类型: maven-archetype-quickstart
*       -DinteractiveMode:false 表示前面参数都给了，就不用一个一个地输入了 

![](https://img-blog.csdnimg.cn/20200703215700192.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200703215714837.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

创建的 maven\_project 项目目录结构

![](https://img-blog.csdnimg.cn/20200703232343720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200703215903505.png)

App.java内容如下

![](https://img-blog.csdnimg.cn/20200703230412377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

运行package

先切换到 maven\_project目录下，再执行：mvn package

![](https://img-blog.csdnimg.cn/20200703231517974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

运行成功后，会在 maven\_project/target 目录下生成maven\_project-1.0-SNAPSHOT.jar文件。

![](https://img-blog.csdnimg.cn/20200703231632715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

编译执行

```
java -cp target/maven_project-1.0-SNAPSHOT.jar com.company.App
```


![](https://img-blog.csdnimg.cn/20200703231821702.png)

### 使用IDEA创建Maven项目

**IDEA中配置Maven**

完成了上面的Maven环境配置以及本地仓库配置后，进行IDEA设置：  
File——>Settings——>Build 、Execute、Deployment->Build Tools->Maven

*   修改 Maven home directory：G:\\apache-maven-3.6.3
*   修改 User settings file：G:\\apache-maven-3.6.3\\conf\\settings.xml
*   Local repository 会被自动识别为 D:\\maven\\repository

设置完后，IDEA中的maven就准备好了，并且是使用本地库。

![](https://img-blog.csdnimg.cn/2020070323312484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**创建Maven项目**  
![](https://img-blog.csdnimg.cn/20200703233503294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200703234042440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200703233712423.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020070323411063.png)

至此，Maven项目Maven\_Project2就已经创建完成了 

![](https://img-blog.csdnimg.cn/20200703234246745.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[https://how2j.cn/k/maven/maven-introduction/1328.html](https://how2j.cn/k/maven/maven-introduction/1328.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-66561dccfcfc4dbba2ff57b11d548146)[Java概览](https://edu.csdn.net/skill/java/java-66561dccfcfc4dbba2ff57b11d548146)[Maven基础](https://edu.csdn.net/skill/java/java-66561dccfcfc4dbba2ff57b11d548146)44190 人正在系统学习中