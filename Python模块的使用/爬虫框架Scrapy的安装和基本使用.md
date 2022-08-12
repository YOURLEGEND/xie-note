**目录**

[Scrapy的安装](#t0)

[创建并运行项目](#t1)

[第一步：执行以下命令创建一个项目](#t2)

[第二步：在 spiders 目录下执行以下命令创建爬虫文件](#t3)

[第三步：编写爬虫文件](#t4) 

[第四步：运行爬虫](#t5)

[Scrapy的处理流程](#t6)

* * *

Scrapy的安装
---------

**Windows系统的安装**

Scrapy依赖于以下四个库文件，安装依赖文件如下：

1.  第一个依赖库是lxml，安装命令：pip3 install lxml
2.  第二个依赖库是pyOpenSSL，[点击下载wheel文件](https://pypi.org/project/pyOpenSSL/#downloads) ，安装命令：pip3 install pyOpenSSL-19.1.0-py2.py3-none-any.whl
3.  第三个依赖库是Twisted，[点击下载](http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted) ，安装步骤是：pip3 install Twisted-18.7.0-cp37-cp37m-win\_amd64.whl
4.  第四个依赖库是pywin32，[点击下载](https://sourceforge.net/projects/pywin32/files/pywin32/Build%20221/) ，双击下载的exe文件进行安装
    

安装完上面四个依赖库文件之后，执行命令安装Scrapy：pip3 install scrapy

![](https://img-blog.csdnimg.cn/20191231082036337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Linux系统的安装**

```
pip install --default-timeout=100 scrapy
```


创建并运行项目
-------

### **第一步：执行以下命令创建一个项目**

```
scrapy startproject project
```


![](https://img-blog.csdnimg.cn/20191231082440233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**创建项目的结构目录如下**

*   spiders：放置爬虫的目录，我们可以在该目录下创建爬虫的文件
*   spiders/\_\_init\_\_.py：初始文件，无需修改
*   \_\_init\_\_.py：初始文件，无需修改
*   items.py：用来定义数据的，使用相当于dict，Items代码模板（继承类），Items类代码模板，需要继承Scrapy库提供的Item的类，对于一般的例子，不需要用户编写这个文件
*   middlewares.py：Middlewares代码模板（继续类），用户希望扩展middlewares的功能，需要把这些功能写到这个文件中，相当于钩子，可以对爬取前后做预处理，如修改请求header，url过滤等
*   pipelines.py：用来处理数据的，Pipelines代码模板（继承类），处理spider模块分析好的结构化数据，如保存入库等
*   settings.py：Scrapy爬虫的配置文件，如果希望优化爬虫功能。需要修改settings.py文件中对应的配置项
*   scrapy.cfg：部署Scrapy爬虫的配置文件

![](https://img-blog.csdnimg.cn/20191231102106276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以将settings.py中的内容修改为如下，修改User\_agent头和设置为不遵守robots.txt规则

![](https://img-blog.csdnimg.cn/20200102175244336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **第二步：在 spiders 目录下执行以下命令创建爬虫文件**

```
scrapy genspider Test www.baidu.com
```


![](https://img-blog.csdnimg.cn/201912311028347.png)

然后会在 spiders 目录下创建爬虫文件Test.py，文件会有默认内容

![](https://img-blog.csdnimg.cn/20191231102929641.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Test.py文件解释如下**

```
# -*- coding: utf-8 -*-      
import scrapy      
class TestSpider(scrapy.Spider):     #这个类必须是继承scrapy.Spider类的子类      
    name = 'Test'                    #当前爬虫的名字叫demo，运行的时候指定的就是这个名字      
    allowed_domains = ['www.baidu.com']   #最开始用户提交给命令行的域名，指的这个爬虫在爬取网站的时候，只能爬取这个域名以下的相关文件      
    start_urls = ['http://www.baidu.com/'] #以列表包含的一个或多个url，就是scrapy框架所要爬取页面的初始页面      
    def parse(self, response):   #解析页面的空方法，他可以解析从网络中爬取的内容，并且形成字典类型，同时还能够对网络中爬取的内容发现其中隐含的新的需要爬取的URL      
        pass
```


### **第三步：编写爬虫文件** 

编辑spiders下的Test.py爬虫文件，根据自己的需要编辑这个文件

```
# -*- coding: utf-8 -*-      
import scrapy       
class TestSpider(scrapy.Spider):      
    name = 'Test'      
    allowed_domains = ['www.baidu.com']      
    start_urls = ['http://www.baidu.com/']       
    def parse(self, response):      1
        print(response)
```


### **第四步：运行爬虫**

**方法一：进入项目目录，执行以下命令运行**

```
scrapy crawl Test
```


![](https://img-blog.csdnimg.cn/20191231110743611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**方法二：在项目最外层目录创建main.py文件，文件内容如下，然后执行点击运行就可以执行了**

```
from scrapy import cmdline          #导入cmdline模块,可以实现控制终端命令行。      
cmdline.execute(['scrapy','crawl','Test'])    #用execute（）方法，输入运行scrapy的命令。
```


![](https://img-blog.csdnimg.cn/20191231110912724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Scrapy的处理流程
-----------

![](https://img-blog.csdnimg.cn/20200101113637586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)