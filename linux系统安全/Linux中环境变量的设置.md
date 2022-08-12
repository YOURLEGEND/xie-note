**目录**

[环境变量](#t0)

[Shell定义的环境变量](#t1)

[查看环境变量](#t2)

[Linux下环境变量的设置](#t3)

* * *

环境变量
----

在Windows 系统下，很多软件安装都需要配置[环境变量](https://so.csdn.net/so/search?q=%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F&spm=1001.2101.3001.7020)，比如 安装 jdk ，如果不配置环境变量，在非软件安装的目录下运行javac 命令，将会报告找不到文件，类似的错误。

在Linux系统下，如果你下载并安装应用程序，很有可能在键入它的名称的时候出现 “command  not found ” 的提示内容。 如果每次都到安装目录文件夹内，找到可执行文件来进行操作就太繁琐了。 这涉及到环境变量path的设置问题，而path 的设置也是在Linux下定制环境变量的一个组成部分

那么什么是环境变量？简单说，就是指定一个目录，运行软件的时候，相关的程序将会按照该目录寻找相关文件。 设置变量对于一般人最实用的功能就是： 不用拷贝某些dll文件到系统目录中了，而path 这一系统变量就是系统搜索dll文件的一系列路径

Shell定义的环境变量
------------

Shell在开始执行的时候就已经定义了一些与系统工作环境有关的变量，用户还可以重新定义这些变量。   
常用的shell环境变量有以下几种。 

*   HOME：用于保存用户主目录的完全路径名。 
*   PATH：用于保存用冒号分隔的目录路径名，shell将按PATH变量中给出的顺序搜索这些目录，找到的第一个与命令名称一致的可执行文件将被执行。 
*   SHELL：当前用户使用的Shell 
*   UID：当前用户的UID 
*   LOGNAME：当前用户的登录名 
*   HOSTNAME：主机名称

![](https://img-blog.csdn.net/20181008194513398?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看环境变量
------

Linux中set 、env 和 export 都可以查看环境变量

*   set命令显示当前shell的变量，包括当前用户的变量,set主要用来设置sh的参数与选项
*   env命令显示当前用户的变量，env用来在构建的环境中运行命令
*   export命令显示当前导出成用户变量的shell变量

每个shell都有自己特有的变量（set）显示的变量，这个和用户变量是不同的，当前用户变量和你用什么shell无关，不管你用什么shell都在，比如HOME,SHELL等这些变量，

但shell自己的变量不同，比如BASH\_ARGC， BASH等，这些变量只有set才会显示，是bash特有的

export不加参数的时候，显示哪些变量被导出成了用户变量，因为一个shell自己的变量可以通过 export “导出”变成一个用户变量。

Linux下环境变量的设置
-------------

如果想将一个路径加入到 $PATH 中，可以有几种方法

比如我想将　/tmp/xie  路径加入到 $PATH 变量中

**1、控制台中设置 (只对当前shell有效)**

PATH=$PATH:/tmp/xie

![](https://img-blog.csdn.net/20181008192838766?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**2、修改当前家目录下的 .bashrc 文件 (只对当前用户有效)**

vim   ~/.bashrc  ，在最末尾加入下面的 ，然后  source  .bashrc  或者  .  .bashrc

export  PATH=$PATH:/tmp/xie

![](https://img-blog.csdn.net/20181008193442855?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**3、修改/etc/bashrc 文件 (针对所有用户有效)**

vim  /etc/bashrc ，在最末尾加入下面的，然后  source  /etc/bashrc  或者  .  /etc/bashrc

export  PATH=$PATH:/tmp/xie

![](https://img-blog.csdn.net/20181008194022976?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中