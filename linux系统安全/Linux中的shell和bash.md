**目录**

[shell](#t0)

[shell script](#t1) 

[sh](#t2)

[bash](#t3)

[Dash](#t4)

[tty 、pty 和 pts](#t5) 

* * *

学安全的我们，经常会听到说获得某服务器的shell，就是指获得某个服务器的操作权限。我们学习linux时，经常会遇到bash，bash也是指的是某个服务器的权限。那么，这两者有什么区别和联系呢？

shell
-----

 Shell 俗称壳（用来区别于核），是指“为使用者提供操作界面”的软件（命令解析器）。它类似于DOS下的command.com和后来的cmd.exe。它接收用户命令，然后调用相应的应用程序。

我们先来看一些Linux系统的结构

![](https://img-blog.csdnimg.cn/20181129225709122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

位于最内层的是硬件，然后是Linux系统内核。shell介于用户和系统内核之间。

**那么shell的功能是什么呢？**

shell用来接收我们用户的输入，并且解释我们的命令。然后将其传给系统内核，内核再调用硬件来操作。

### shell script 

我们很容易知道“Shell Script”指的是针对shell所写的脚本。我们将一些shell规定的语法与命令，再搭配正则表达式、管道命令与数据流重定向等功能，写成一个纯文本文件以达到我们想要的处理目的，再配以“.sh”的扩展名，这便是“Shell script”了。

简言之，我们只有通过Shell 这个工具来解释我们的命令等请求，我们才能成功实现与计算机的交流，同时再搭配Shell script可以批量处理命令的“程序”，我们就可以与计算机更优雅的交流哟！

我们可能会问：既然shell是解释命令的工具，那么这个工具可不可以多样化呢？不同的解释工具可不可以遵从不同的规则呢？   
这是必然的咯，何况是像Linux这种开源的好东西，怎么会缺乏多样性呢？！   
所以，我们就不难理解Linux中的shell有多种类型了吧，这其中最常用的几种是 **Bourne shell（sh）**、**Bourne Again shell(bash)** 和 **Debian Almquist Shell（dash）**。其中三种shell各有优缺点：

### sh

**sh**(Bourne shell)是UNIX最初使用的shell，并且在每种UNIX上都可以使用。Bourne shell在shell编程方面相当优秀，但在处理与用户的交互方面做得不如其他几种shell。

### bash

**bash** (Bourne Again shell)，它是Linux操作系统缺省的shell，是Bourne shell的扩展，简称**Bash**，与Bourne shell完全向后兼容，并且在Bourne shell的基础上增加、增强了很多特性。Bash放在/bin/bash 中，它有许多特色，可以提供如命令补全、命令编辑和命令历史表等功能，有灵活和强大的编程接口，同时又有很友好的用户界面。 而且在Redhat系列的Linux 操作系统中的 /bin/sh 是 /bin/bash 的符号链接。所以，用 sh执行脚本和bash 执行脚本，效果是一样的。所以我们通常会在脚本第一行写  #!/bin/bash  ，意思就是用 /bin/bash 去执行这个脚本。

![](https://img-blog.csdnimg.cn/20181129230731366.png)

### Dash

虽然bash好用，功能也多。但是鉴于bash过于复杂，有人把bash从NetBSD移植到Debian Linux并更名为 **dash** (Debian Almquist Shell)，并建议将/bin/sh指向它，以获得更快的脚本执行速度。Dash Shell 比Bash Shell小的多，符合POSIX标准。也就是若脚本第一行为“#!/bin/sh”，则我们使用命令：sh  script\_name.sh  时是调用的dash去解释脚本；Debian下默认使用的还是bash，只不过sh指向的是dash

![](https://img-blog.csdnimg.cn/20181129231219243.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### tty 、pty 和 pts 

这里再多说一点，说说我们经常看到的 tty 和 pts 。

**tty**就是linux里面终端的意思，你每一个可以输入shell进行解析的当前框，就是终端。

**pty**就是虚拟终端。

**pts**是终端的一个实例化。比如，下图

我们这里打开了三个终端，第一个终端tty实例化为 pts/0，第二个终端tty实例化为 pts/1 ,第三个终端tty实例化为 pts/2 ，如果我们现在用Xshell远程连接，那么Xshell终端tty实例化为 pts/3。

你在每个终端输入的命令，都是属于该 pts 下面的。

![](https://img-blog.csdnimg.cn/20190701161621415.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190701161646499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190701161701993.png)

![](https://img-blog.csdnimg.cn/20190701161753980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果我们的 pts/0 想在 pts/3 的屏幕上输入文字

```
write pts/3的用户名  pts/3      
ctrl+d #结束
```


![](https://img-blog.csdnimg.cn/2019070313360651.png)

![](https://img-blog.csdnimg.cn/20190703133616659.png)

如果我们想把pts/3的终端给踢掉

```
pkill -kill -t pts/3
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中