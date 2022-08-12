**目录**

[GCC](#t0)

[gcc的组成](#t1)

[查看是否安装了gcc](#t2)

[gcc的使用](#t3)

* * *

GCC
---

我们都知道，在Windows系统下，由C语言或C++编写的程序，需要编译成exe后才能在Windows系统上运行。那么，在Linux上也一样，C语言写的程序需要编译以后才可以运行。在Windows上，编译工具有VS系列。那么在Linux上，编译可以使用[gcc](https://so.csdn.net/so/search?q=gcc&spm=1001.2101.3001.7020)来完成。

**GCC**（GNU Compiler Collection，GNU编译器套件）是由GNU开发的编程语言译器。GNU编译器套件包括C、C++、 Objective-C、 Fortran、Java、Ada和Go语言前端，也包括了这些语言的库（如libstdc++，libgcj等）GCC的初衷是为GNU操作系统专门编写的一款编译器。大部分 Linux 发行版中都会默认安装GCC。GCC 编译器通常以 `gcc` 命令的形式在终端（Shell）中使用，它有很多选项。我们在安装软件使用 make 指令进行编译的时候，make命令通过借助 Makefile 里面编写的编译规范会自动化的调用 gcc 、ld 以及运行某些需要的程序进行编译。

### gcc的组成

GCC是由许多组件组成的。如下列出了 GCC 的各个部分，但它们也并不总是出现 的。有些部分是和语言相关的，所以如果没有安装某种特定语言，系统中就不会出现相关的文件。

| 部分 | 描述 |
| --- | --- |
| c++ | gcc 的一个版木，默认语言设置为 [C++](http://c.biancheng.net/cplus/)，而且在连接的时候自动包含标准 C++ 库。这和 g++ 一样 |
| ccl | 实际的C编译程序 |
| cclplus | 实际的 C++ 编泽程序 |
| collect2 | 在不使用 GNU 连接程序的系统上，有必要运行 collect2 来产生特定的全局初始化代码（例如 C++ 的构造函数和析构函数） |
| configure | GCC 源代码树根目录中的一个脚木。用于设置配置值和创建GCC 编译程序必需的 make 程序的描述文件 |
| crt0.o | 这个初始化和结束代码是为每个系统定制的，而且也被编译进该文件，该文件然后会被连接到每个可执行文件中来执行必要的启动和终止程序 |
| cygwin1.dll | Windows 的共享库提供的 API，模拟 UNIX 系统调用 |
| f77 | 该驱动程序可用于编译 Fortran |
| f771 | 实际的 Fortran 编译程序 |
| g++ | gcc 的一个版木，默认语言设置为 C++，而且在连接的时候自动包含标准 C++ 库。这和 c++ 一样 |
| gcc | 该驱动程序等同于执行编译程序和连接程序以产生需要的输出 |
| gcj | 该驱动程序用于编译 [Java](http://c.biancheng.net/java/) |
| gnat1 | 实际的 Ada 编译程序 |
| gnatbind | 一种工具，用于执行 Ada 语言绑定 |
| gnatlink | 一种工具，用于执行 Ada 语言连接 |
| jc1 | 实际的 Java 编译程序 |
| libgcc | 该库包含的例程被作为编泽程序的一部分，是因为它们可被连接到实际的可执行程序中。 它们是特殊的例程，连接到可执行程序，来执行基木的任务，例如浮点运算。这些库中的例程通常都是平台相关的 |
| libgcj | 运行时库包含所有的核心 Java 类 |
| libobjc | 对所有 Objective-C 程序都必须的运行时库 |
| libstdc++ | 运行时库，包括定义为标准语言一部分的所有的 C++ 类和函数 |

### 查看是否安装了gcc

```
gcc --version  #安装好了gcc，就可以使用此命令查看版本      
cc  --version  #如果已经安装好GCC，并链接到名为 cc 的默认C编译器，就会看到编译器的版本号和版权信息       
如果没有安装，可以使用以下命令安装gcc      
yum -y install gcc
```


###  ![](https://img-blog.csdnimg.cn/2020022423023983.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### gcc的使用

例如，我们有一个C语言文件，如下。我们使用 gcc 文件名 编译，编译后生成 a.out 文件。

```
gcc main.c
```


![](https://img-blog.csdnimg.cn/20200224232142956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果不想使用默认的文件名，那么可以通过 -o 参数来自定义文件名

```
gcc main.c -o xie
```


![](https://img-blog.csdnimg.cn/20200224232549596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

为什么不同版本的操作系统使用gcc编译好的程序不能使用？
----------------------------

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中