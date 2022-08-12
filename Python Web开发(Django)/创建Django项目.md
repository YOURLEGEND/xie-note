**目录**

[创建一个Django项目](#t0)

[项目结构](#t1)

* * *

创建一个Django项目
------------

**创建项目**：

```
django-admin startproject   项目名称
```


**创建应用app**，一个项目类似于是一个架子，但是真正起作用的还是 应用app

进入创建的项目的目录下，然后执行：

```
python  manage.py  startapp  app名称
```


**运行程序**：进入项目目录下，也就是manage.py的同级目录

```
#默认是8000端口      
python manage.py runserver       
#我们也可以自己指定端口      
python manage.py runserver  8888
```


![](https://img-blog.csdnimg.cn/20190217111607316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

浏览器访问链接：[http://127.0.0.1:8888/](http://127.0.0.1:8888/) ，如下，说明程序运行成功

![](https://img-blog.csdnimg.cn/20190217162917337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果我们想显示的是中文的页面，我们可以修改项目下的settings.py文件中的下面这几项

```
LANGUAGE_CODE = 'zh-Hans'      
TIME_ZONE = 'Asia/Shanghai'       
USE_TZ = False
```


**project和app的关系：**  
app  是 django  项目的组成部分。一个 app  代表项目中的一个模块，所有 URL  请求的响应都是由 app  来处理。比如豆瓣，里面有图书，电影，音乐，同城等许许多多的模块，如果站在 django  的角度来看，图书，电影这些模块就是 app  ，图书，电影这些 app  共同组成豆瓣这个项目。因此这里要有一个概念， django  项目由许多 app  组成，一个 app  可以被用到其他项目，django  也能拥有不同的 app

### 项目结构

在项目目录下，有一个同名的目录，还有一个app名字的目录。还有两个文件 **db.sqlite3** 和 **manage.py**。

![](https://img-blog.csdnimg.cn/20190217124643274.png)

项目目录下同名目录下的子文件

![](https://img-blog.csdnimg.cn/20190217125443734.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   \_\_init\_\_.py：一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包
*   manage.py  ：以后和项目交互基本上都是基于这个文件。一般都是在终端输入 python  manage.py \[子命令\]  。可以输入 python manage.py help  看下能做什么事情。除非你知道你自己在做什么，一般情况下不应该编辑这个文件。
*   settings.py  ：本项目的设置项，以后所有和项目相关的配置都是放在这个里面。
*   urls.py  ：这个文件是用来配置URL路由的。比如访问 http://127.0.0.1/news/  是访问新闻列表页，这些东西就需要在这个文件中完成。
*   wsgi.py  ：项目与 WSGI  协议兼容的 web  服务器入口，部署的时候需要用到的，一般情况下也是不需要修改的  
     

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-137)[Web应用开发](https://edu.csdn.net/skill/python/python-3-137) [Django](https://edu.csdn.net/skill/python/python-3-137)84853 人正在系统学习中