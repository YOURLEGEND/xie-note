**目录**

[对于文件来说](#t0)

[对于目录来说](#t1)

[利用touch命令修改时间](#t2) 

* * *

在Linux下，对于一个文件有很多个[时间戳](https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E6%88%B3&spm=1001.2101.3001.7020)属性，文件的创建时间，文件的修改时间，文件的读取时间。我们用stat来查看文件的相关时间属性。

### 对于文件来说

如下，我们使用stat命令查看文件的时间属性，一共有这么几个时间属性

Access：atime，文件的最近访问时间，只要读取该文件，Access时间就会更新

Modify：mtime，文件的内容最近修改的时间，当文件被写的时候，Modify就会更新

Change：ctime，文件属性最近修改的时间，当文件的目录被修改，或者文件的所有者，权限等被修改时，Change时间就会更新

![](https://img-blog.csdnimg.cn/20190711222655589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 对于目录来说

![](https://img-blog.csdnimg.cn/20190711223444481.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

进入目录，其属性不变。在目录中创建文件，Modify和Change同时变化。在目录中删除文件，Modify、Change同时变化。

### 利用touch命令修改时间 

修改Access和Change时间：touch -a 1.txt

![](https://img-blog.csdnimg.cn/20190711224022853.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改Modify和Change的时间：touch -m 1.txt

![](https://img-blog.csdnimg.cn/20190711224951273.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

指定日期修改Access和Modify时间：touch  -d "08:08:08" 1.txt

![](https://img-blog.csdnimg.cn/20190711224223503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改文件的所有时间：touch -c 1.txt

![](https://img-blog.csdnimg.cn/20190711224804991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将文件2的时间设置成文件1的时间：touch -r  1.txt  2.txt

![](https://img-blog.csdnimg.cn/20190711224612963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-fc9a7ad4c2104078a430dac6988be2b3)[Linux入门](https://edu.csdn.net/skill/gml/gml-fc9a7ad4c2104078a430dac6988be2b3)[如何查看文件全部内容](https://edu.csdn.net/skill/gml/gml-fc9a7ad4c2104078a430dac6988be2b3)10949 人正在系统学习中