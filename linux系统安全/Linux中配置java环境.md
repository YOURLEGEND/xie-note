本文所需的jdk1.8版本rpm包：链接: [https://pan.baidu.com/s/146rZKd0hP0851MBT68PnEw](https://pan.baidu.com/s/146rZKd0hP0851MBT68PnEw)  提取码: uqif

很多应用的运行需要有Java环境，如 tomcat 、amoeba 等。

首先查看自己的系统有没有安装java环境，查看命令： java  -version 

![](https://img-blog.csdn.net/20180927190936530?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这里显示我们的 java 版本是1.7.0 。有时候，我们的应用需要更高的java版本。所以，我们还得安装更高版本的java。

所以，现在就 安装 或者 升级 java 1.8.0 版本。

1、我们使用 rpm  -ivh  jdk-8u20-linux-x64.rpm  来安装java的rpm安装包

![](https://img-blog.csdn.net/20180927191244745?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

安装完之后，我们还需要去配置环境变量。

2、打开 /etc/profile 文件，在末尾加入下列几行，保存退出

```
export JAVA_HOME=/usr/java/jdk1.8.0_20      
export JAVA_BIN=/usr/java/jdk1.8.0_20/bin　　      
export PATH=$PATH:$JAVA_HOME/bin      
export CLASSPATH=:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar      
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```


3、 使配置文件生效： .  /etc/profile   或   source  /etc/profile

![](https://img-blog.csdn.net/20180927191837369?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，我们的 java 环境 已经配置好了。