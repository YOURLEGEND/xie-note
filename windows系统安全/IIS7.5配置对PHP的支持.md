> 以下环境是 Windows server2008R2 IIS7.5

一般情况下，windows server系统默认是仅支持[IIS](https://so.csdn.net/so/search?q=IIS&spm=1001.2101.3001.7020)+asp 或 IIS+aspx 的 搭配的，但是有时候我们的网站程序是php的。所以，我们就需要配置 IIS 对 PHP 的支持。

怎么安装 IIS 的话，我不说了，今天主要讲怎么配置 IIS 对PHP的支持。

我们首先需要下载PHP的压缩包，这里我们用的是php-5.2.5 64位的版本。而且里面的配置文件我也已经修改好了，大家直接下载用就行了。 

链接: [百度网盘 请输入提取码](https://pan.baidu.com/s/1AFG63Ca7M4PrcL0DT_6G0Q "百度网盘 请输入提取码")   提取码: 8i9i 

把他下载好，解压缩到C盘下，然后修改文件夹名为 php。

![](https://img-blog.csdnimg.cn/20181115112125506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开我们的服务器管理，双击处理程序映射。

![](https://img-blog.csdnimg.cn/2018111511223218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后添加模块映射

![](https://img-blog.csdnimg.cn/2018111511232996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

填入如下的内容

![](https://img-blog.csdnimg.cn/20181115112549327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后点击确定，会提示我们是否添加，我们选择确定就好了。

然后在默认文档中加入 index.php

![](https://img-blog.csdnimg.cn/20181115112723377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181115112744310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

接着在我们的网站根目录下新建 index.php 文件 ，内容为如下

```
<?php phpinfo(); ?>
```


访问，看到如下，说明配置支持PHP成功

![](https://img-blog.csdnimg.cn/20181115112850597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)