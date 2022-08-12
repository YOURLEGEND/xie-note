**目录**

[Django](#t0)

[MVC模型](#t1)

[Django的MTV模型](#t2)

[Django的安装](#t3)

[虚拟环境](#t4)

* * *

Django
------

**Django**是一个开放源代码的Web应用框架，由Python写成，其网站的特点是高质量，可维护，可扩展，Django的web项目可在短时间内完成。

### MVC模型

Django是一个基于**[MVC](https://baike.baidu.com/item/MVC)**构造的框架，即**模型M**，**视图V** 和 **控制器C。**它是一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，将业务逻辑聚集到一个部件里面，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。MVC被独特的发展起来用于映射传统的输入、处理和输出功能在一个逻辑的图形化用户界面的结构中。MVC 模式同时提供了对 HTML、CSS 和 JavaScript 的完全控制。

*   Model（模型）：是应用程序中用于处理应用程序数据逻辑的部分，通常模型对象负责在数据库中存取数据。
*   View（视图）：是应用程序中处理数据显示的部分，通常视图是依据模型数据创建的
*   Controller（控制器）：是应用程序中处理用户交互的部分，通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据

MVC开始是存在于桌面程序中的，M是指业务模型，V是指[用户界面](https://baike.baidu.com/item/%E7%94%A8%E6%88%B7%E7%95%8C%E9%9D%A2)，C则是控制器，使用MVC的目的是将M和V的实现代码分离，从而使同一个程序可以使用不同的表现形式。比如一批统计数据可以分别用[柱状图](https://baike.baidu.com/item/%E6%9F%B1%E7%8A%B6%E5%9B%BE)、[饼图](https://baike.baidu.com/item/%E9%A5%BC%E5%9B%BE)来表示。C存在的目的则是确保M和V的同步，一旦M改变，V应该同步更新

![](https://img-blog.csdnimg.cn/2019021519040592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Django的MTV模型

而在Django中，**控制器(Control)**接受用户输入的部分由框架自行处理，所以 Django 里更关注的是**模型（Model）、模板(Template**)和**视图（Views）**，称为 **MTV模式**。它们各自的职责如下：

<table><tbody><tr><td style="vertical-align:top;"><p>层次</p></td><td style="vertical-align:top;"><p>职责</p></td></tr><tr><td style="vertical-align:top;"><p>模型（Model），即数据存取层</p></td><td style="vertical-align:top;"><p>处理与数据相关的所有事务： 如何存取、如何验证有效性、包含哪些行为以及数据之间的关系等。</p></td></tr><tr><td style="vertical-align:top;"><p>模板(Template)，即表现层</p></td><td style="vertical-align:top;"><p>处理与表现相关的决定：负责如何把页面展示给用户(html)。</p></td></tr><tr><td style="vertical-align:top;"><p>视图（View），即业务逻辑层</p></td><td style="vertical-align:top;"><p>存取模型及调取恰当模板的相关逻辑。模型与模板的桥梁</p></td></tr></tbody></table>

![](https://img-blog.csdnimg.cn/20190215190430185.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Django处理用户的请求**

1.  Web服务器（中间件）收到一个http请求
2.  Django在URLconf里查找对应的视图(View)函数来处理http请求
3.  视图函数调用相应的数据模型来存取数据、调用相应的模板向用户展示页面
4.  视图函数处理结束后返回一个http的响应给Web服务器
5.  Web服务器将响应发送给客户端

![](https://img-blog.csdn.net/20130907173600843)

### Django的安装

**Windows环境**

python版本是3.7，直接pip安装Django2.1.7最新版本

```
pip  install   Django==2.1.7
```


安装完之后，查看是否安装成功：

```
import   django      
django.get_version()
```


![](https://img-blog.csdnimg.cn/20190215154410539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 虚拟环境

**为什么需要虚拟环境？**  
到目前位置，我们所有的第三方包安装都是直接通过 pip install xx  的方式进行安装的，这样安装会将那个包安装到你的系统级的 Python  环境中。但是这样有一个问题，就是如果你现在用 Django 1.10.x  写了个网站，然后你的领导跟你说，之前有一个旧项目是用 Django 0.9  开发的，让你来维护，但是 Django 1.10  不再兼容 Django 0.9  的一些语法了。这时候就会碰到一个问题，我如何在我的电脑中同时拥有 Django 1.10  和 Django 0.9  两套环境呢？这时候我们就可以通过虚拟环境来解决这个问题。  
**虚拟环境原理介绍**：  
虚拟环境相当于一个抽屉，在这个抽屉中安装的任何软件包都不会影响到其他抽屉。并且在项目中，我可以指定这个项目的虚拟环境来配合我的项目。比如我们现在有一个项目是基于 Django1.10.x  版本，又有一个项目是基于 Django 0.9.x  的版本，那么这时候就可以创建两个虚拟环境，在这两个虚拟环境中分别安装 Django 1.10.x  和 Django 0.9.x  来适配我们的项目

**安装 virtualenv**

virtualenv  是用来创建虚拟环境的软件工具，我们可以通过 pip  或者 pip3  来安装：

```
pip install virtualenv
```


接着我们可以安装 **virtualenvwrapper** 软件包来方便的管理虚拟环境

```
类Unix下： pip install virtualenvwrapper      
windows下：pip install virtualenvwrapper-win
```


**virtualenvwrapper  基本使用**

我们在虚拟环境下做的所有事情，比如说安装某个包都只是在当前虚拟环境起作用

```
创建虚拟环境： mkvirtualenv my_env      
切换到my_env虚拟环境下： workon  my_env      
退出当前虚拟环境：deactivate      
删除某个虚拟环境：rmvirtualenv my_env      
列出所有虚拟环境：lsvirtualenv      
进入到虚拟环境所在的目录：cdvirtualenv
```


参考视频：[https://study.163.com/course/courseMain.htm?courseId=1005543039](https://study.163.com/course/courseMain.htm?courseId=1005543039)            

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-137)[Web应用开发](https://edu.csdn.net/skill/python/python-3-137) [Django](https://edu.csdn.net/skill/python/python-3-137)84853 人正在系统学习中