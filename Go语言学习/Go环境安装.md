**目录**

[下载安装go安装包](#t0)

[创建go工作目录](#t1)

[配置环境变量](#t2)

[创建第一个Go项目](#t3)

* * *

> 以下介绍在Windows下安装Go语言环境

### **下载安装go安装包**

先去下载Go安装包：

*   Go官网下载地址：[https://golang.org/dl/](https://golang.org/dl/)
*   Go官方镜像站（推荐）：[https://golang.google.cn/dl/](https://golang.google.cn/dl/)

下载完之后双击安装即可，安装过程没什么介绍的，直接一直下一步即可。看到下面的就说明安装完成了

![](https://img-blog.csdnimg.cn/20200710100350738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **创建go工作目录**

首先去创建一个目录，用于存放Go项目的存放路径，我的为：E:\\Go ，然后在该目录下创建如下三个文件夹：

*   bin：用于存放编译后生成的可执行文件
*   pkg：用于存放编译后生成的归档文件
*   src：用于存放源码文件

![](https://img-blog.csdnimg.cn/20200710100852773.png)

在进行Go语言开发的时候，我们的代码总是会保存在 `$GOPATH/src` 目录下。在工程经过`go build`、`go install`或`go get`等指令后，会将下载的第三方包源代码文件放在`$GOPATH/src`目录下， 产生的二进制可执行文件放在 `$GOPATH/bin`目录下，生成的中间缓存文件会被保存在 `$GOPATH/pkg` 下。

如果我们使用版本管理工具（Version Control System，VCS。常用如Git）来管理我们的项目代码时，我们只需要添加`$GOPATH/src`目录的源代码即可。`bin` 和 `pkg` 目录的内容无需版本控制。

### **配置环境变量**

然后去环境变量里面添加下面这个

![](https://img-blog.csdnimg.cn/20200710100709200.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在cmd命令行输入 go，显示如下说明安装好了go环境

![](https://img-blog.csdnimg.cn/20200710101317215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 创建第一个Go项目

```
go build   #编译生成       
go build -o test.exe  #编译生成指定的文件       
go install  #表示安装的意思，它先编译源代码得到可执行文件，然后将可执行文件移动到GOPATH的bin目录下。因为我们的环境变量中配置了GOPATH下的bin目录，所以我们就可以在任意地方直接执行可执行文件了。
```


在 E:\\Go\\src 下创建一个hello文件夹，在hello文件夹下创建名为main.go的文件

![](https://img-blog.csdnimg.cn/20200710102004335.png)

main.go文件内容如下

![](https://img-blog.csdnimg.cn/20200710102022191.png)

然后cmd切换到hello目录下，执行 go build 命令编译生成，将会生成一个 hello.exe文件。

![](https://img-blog.csdnimg.cn/20200710102122879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

也可以使用 go build  -o  test.exe 来指定生成exe文件的名字

![](https://img-blog.csdnimg.cn/20200710102234849.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[https://www.cnblogs.com/wqzn/p/11730052.html](https://www.cnblogs.com/wqzn/p/11730052.html)