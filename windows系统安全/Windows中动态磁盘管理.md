**目录**

[动态磁盘](#t0 "动态磁盘")

[基本磁盘和动态磁盘的转换](#t1 "基本磁盘和动态磁盘的转换")

[简单卷](#t2 "简单卷")

[跨区卷](#t3 "跨区卷")

[带区卷](#t4 "带区卷")

[镜像卷](#t5 "镜像卷")

[RAID-5卷](#t6 "RAID-5卷")

* * *

相关文章：[硬盘分区形式(MBR、GPT)、系统引导、文件系统、Inode和Block](https://blog.csdn.net/qq_36119192/article/details/82153000 "硬盘分区形式(MBR、GPT)、系统引导、文件系统、Inode和Block")

动态磁盘
----

Windows 2000起引入了基本磁盘和动态磁盘的概念，并且把它们添加到Windows系统管理员的工具之中。

无论是基本磁盘还是动态磁盘，你都可以使用任何文件系统，包括FAT和NTFS。而且你可以在动态磁盘改变卷而不需要重启系统。你可以把一个基本磁盘转换成动态磁盘。但是你必须了解这并不是一个双向的过程。一旦你从基本磁盘变成了动态磁盘，除非你重新创建卷，或者使用一些磁盘工具（例如分区助手），否则你不能将它转变回去。

如同名字所暗示的那样，基本磁盘是IT专业人士最熟悉的类型。例如，基本磁盘包含了基本分区，扩展分区和逻辑分区。动态磁盘支持任何新功能和新术语。

下面是你可以在一个动态磁盘中创建的各种不同卷类型：

*   简单卷
*   跨区卷
*   带区卷
*   镜像卷
*   RAID-5卷

### 基本磁盘和动态磁盘的转换

基本磁盘转换为动态磁盘：在转换磁盘之前，必须先关闭该磁盘运行的所有程序，然后右键磁盘，转换到动态磁盘即可。转换为动态磁盘后， 基本磁盘的分区将被转换为简单卷

![](https://img-blog.csdn.net/20181017211244924?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

动态磁盘转换到基本磁盘： 动态磁盘上尚未创建卷才能转换 ，动态磁盘上的所有卷都删除后，动态磁盘将自动转换为基本磁盘

![](https://img-blog.csdn.net/20181017211106520?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

###  简单卷

单个磁盘上的空间：一个区域/连续的或不连续的多个区域

可以被扩展 (NTFS文件系统)：没有存放当前使用的操作系统，或者不是通过简单卷启动计算机都可扩展成更大的简单卷、跨区卷或镜像卷

![](https://img-blog.csdn.net/2018101721270170?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 跨区卷

由两块或多块硬盘上的存储空间组成，每块磁盘可以提供不同的磁盘空间，可以对跨区卷进行扩展

![](https://img-blog.csdn.net/20181017212608808?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 带区卷

由两块或两块以上硬盘上的存储空间组成，每块硬盘所提供的空间大小必须相同、读写效率高、连续分割数据，并行读/写到多个磁盘 

![](https://img-blog.csdn.net/20181017212843725?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 镜像卷

由两块硬盘上的存储空间组成吗，每块磁盘提供相同大小的空间，支持容错，磁盘空间利用率50%

![](https://img-blog.csdn.net/20181017222311792?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### RAID-5卷

至少需要三块硬盘，每块硬盘必须提供相同的磁盘空间，提供容错功能，磁盘空间利用率（n-1）/n，n为磁盘数量 

![](https://img-blog.csdn.net/20181017222832571?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181017222542887?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11176 人正在系统学习中