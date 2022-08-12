原本Mac电脑就已经安装了1.8.0\_311的java，现在有需求还需要安装Java 11。![在这里插入图片描述](https://img-blog.csdnimg.cn/55ec14e618e24e42bd001b76ffe612c5.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/08ff112054344c219f68bc7fd9a2f236.png)  
下载地址：https://download.csdn.net/download/u010025294/12863527  
下载后安装，安装完成后。再次查看 /Library/Java/JavaVirtualMachines目录。如下图所示，可以看到多了jdk-11.0.8.jdk

```
cd  /Library/Java/JavaVirtualMachines ls -lh`

*   1
*   2
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/3865ee0b14ca4db199bd67c4d90a00d0.png)  
然后需要修改.zshrc配置文件了。

首先使用如下命令查看已安装的Java的Home目录，如下图：

```
/usr/libexec/java_home -V`

*   1
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/6abedef09370437fbaac071546a37c91.png)  
然后编辑.zshrc配置文件，如下：

```
#java8 export JAVA_8_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_311.jdk/Contents/Home" alias jdk8='export JAVA_HOME=$JAVA_8_HOME' #java11 export JAVA_11_HOME="/Library/Java/JavaVirtualMachines/jdk-11.0.8.jdk/Contents/Home" alias jdk11='export JAVA_HOME=$JAVA_11_HOME' # 默认使用jdk8 export JAVA_HOME=$JAVA_8_HOME`

*   1
*   2
*   3
*   4
*   5
*   6
*   7
*   8
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/535a0ed03b8a4ee19adbe60dd92917c8.png)

编辑完成后，执行如下命令刷新.zshrc配置文件。然后就可以使用jdk8和jdk11进行切换了。

```
source ~/.zshrc`

*   1
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/cd65e774639643c49a59842b6d779541.png)

修改Jar Launcher默认的java版本
-----------------------

上面修改java默认的版本只适用于命令行下的java版本，双击打开jar应用程序不适用。原因是双击jar启动的时候，系统默认指定的jdk版本是根据 /usr/libexec/java\_home 得到的。  
如下图，执行如下命令可以看到默认的版本是java 11。

```
/usr/libexec/java_home -V`

*   1
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/4c73a602bdef44ecbc9a6844754a1187.png)

解决方法是进入/Library/Java/JavaVirtualMachines/ 目录，打开除了jdk1.8之外的其他jdk目录下面的Contents目录，编辑Info.plist  
![在这里插入图片描述](https://img-blog.csdnimg.cn/8a65b80793b04e38b9f1d66f7cf7c185.png)  
保存后再次执行查询命令可以看到默认的版本是java 8了。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/c08cd52564bf41b2aff0d09ea4c4b981.png)  
参考：https://www.jianshu.com/p/6273e7ab0c56