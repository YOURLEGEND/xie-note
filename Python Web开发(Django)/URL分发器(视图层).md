**目录**

[视图](#t0)

[URL映射](#t1)

[path函数](#t2)

[URL中传入参数](#t3)

[普通传入参数](#t4)

[变量形式传入参数](#t5)

[​URL中包含另一个urls模块](#t6)

[URL命名、URL反转、应用命名空间](#t7)

* * *

视图
--

视图一般都写在 app  的 **views.py**  中。并且视图的第一个参数永远都是 request  （一个HttpRequest）对象。这个[对象存储](https://so.csdn.net/so/search?q=%E5%AF%B9%E8%B1%A1%E5%AD%98%E5%82%A8&spm=1001.2101.3001.7020)了浏览器请求过来的所有信息，包括携带的参数以及一些头部信息等。在视图中，一般是完成逻辑相关的操作。比如这个请求是添加一篇博客，那么可以通过 request 来接收到这些数据，然后存储到数据库中，最后再把执行的结果返回给浏览器。视图函数的返回结果必须是 HttpResponseBase  对象或者其子类的对象。示例代码如下：

```
from django.http import HttpResponse      
def first_page(request):      
    return HttpResponse("Hello,word!Django")
```


URL映射
-----

![](https://img-blog.csdnimg.cn/20190217142404419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

视图写完后，要与URL进行映射，也即用户在浏览器中输入什么 url  的时候可以请求到这个视图函数。在用户输入了某个 url  ，请求到我们的网站的时候， django  会从项目的 urls.py  文件中寻找对应的视图。在 urls.py  文件中有一个 urlpatterns  变量，以后 django  就会从这个变量中读取所有的匹配规则。匹配规则需要使用 django.urls.path  函数进行包裹，这个函数会根据传入的参数返回 URLPattern  或者是 URLResolver  的对象。示例代码如下：

```
from django.contrib import admin      
from django.urls import path      
from first_app import views       
urlpatterns = [      
    path('admin/', admin.site.urls),      
    path('',views.first_page)      
]
```


![](https://img-blog.csdnimg.cn/20190217142642933.png)

### **path函数**

> path(route,view,kwargs,name)

*   `route：` 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 `urlpatterns` 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。这些准则不会匹配 GET 和 POST 参数或域名。例如，URLconf 在处理请求 `https://www.example.com/myapp/` 时，它会尝试匹配 `myapp/` 。处理请求 `https://www.example.com/myapp/?page=3` 时，也只会尝试匹配 `myapp/`。
*   `view：`当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 [`HttpRequest`](../ref/request-response.html#django.http.HttpRequest) 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。
*   `kwargs：`任意个关键字参数可以作为一个字典传递给目标视图函数
*   `name：`为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。

URL中传入参数
--------

### 普通传入参数

有时候， url  中包含了一些参数需要动态调整。比如简书某篇文章的详情页的url，是 https://www.jianshu.com/p/a5aab9c4978e  后面的 a5aab9c4978e  就是这篇文章的 id  ，那么简书的文章详情页面的url就可以写成 https://www.jianshu.com/p/<id>  ，其中id就是文章的id。那么如何在 django  中实现这种需求呢。这时候我们可以在 path  函数中，使用尖括号的形式来定义一个参数。比如我现在想要获取一本书籍的详细信息，那么应该在 url  中指定这个参数。示例代码  
如下：

```
from django.contrib import admin      
from django.urls import path       
urlpatterns = [      
    path('admin/', admin.site.urls),      
    path('book/<book_id>/',views.book_list)      
]
```


而我们的 views.py  中的代码如下：

```
def book_list(request,book_id):      
    text = "您输入的书籍的id是：%s" % book_id      
    return HttpResponse(text)
```


![](https://img-blog.csdnimg.cn/20190217141304246.png)

注：我们 urls.py中path函数的变量，也就是 <book\_id>中的book\_id 一定要和 views.py中函数接收的参数book\_id是一致的  

### 变量形式传入参数

当然，也可以通过查询字符串的方式传递一个参数过去。这样就不用<>了，示例代码如下：

```
urlpatterns = [      
    path('admin/', admin.site.urls),      
    path('bookid/',views.book_detail)      
]
```


而在 views.py  中的函数也不用接收另外的参数了，代码如下：

```
def book_detail(request):      
    book_id = request.GET.get("id")      
    text = "您输入的书籍id是：%s" % book_id      
    return HttpResponse(text)
```


以后在访问的时候就是通过 /bookid?id=123  即可将参数传递过去。

![](https://img-blog.csdnimg.cn/20190217141533744.png)  
URL中包含另一个urls模块
------------------------------------------------------------------------

在我们的项目中，不可能只有一个 app  ，如果把所有的 app  的 views  中的视图都放在 urls.py  中进行映射，肯定会让代码显得非常乱。因此 django  给我们提供了一个方法，可以在 app  内部包含自己的 url  匹配规则，而在项目的 urls.py  中再统一包含这个 app  的 urls  。

比如，现在我们的项目有三个app

*   front：管理前端的应用
*   cmd：管理后端的应用
*   first\_app：处理逻辑的应用

![](https://img-blog.csdnimg.cn/20190217153915993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们可以在我们每个app内部，建立 urls.py文件。

比如，在cms中，我们建立的urls.py如下

```
from django.urls import path      
from . import views       
urlpatterns=[      
    path("",views.index),      
    path("login/",views.login)      
]
```


views.py文件如下 

```
from django.http import HttpResponse       
def index(request):      
    return HttpResponse("CMS首页")      
def login(request):      
    return HttpResponse("CMS登录页面")
```


然后我们在整个项目的 urls.py文件中可以如下：

```
# first_project/urls.py文件：      
from django.contrib import admin      
from django.urls import path,include      
urlpatterns = [      
    path('admin/', admin.site.urls),      
    path("",include("front.urls")),      
    path('cms/',include("cms.urls"))      
]
```


然后以后我们访问cms下的内容的话，在cms的urls.py中，默认是在cms/ 路径下的。 

![](https://img-blog.csdnimg.cn/20190217154753271.png)![](https://img-blog.csdnimg.cn/20190217154806146.png)

URL命名、URL反转、应用命名空间
------------------

**为什么需要给URL命令？**

因为 url 是经常变化的，如果在代码中写死的话，后续可能会经常改代码，给 url 取个名字，以后使用 url 的时候就使用它的名字进行反转就可以了。

给url命名如下：

```
urlpattern=[      
    path("login/"，views.login,name="login")   #将该url命名为login      
]
```


那么，我们怎么利用这个url的名字得到url的绝对路径呢？

我们可以进行**url反转**，将url名字转换成url的绝对路径

```
login_url = reverse('login')   #URL反转，将url名字反转成url的绝对路径
```


 **为什么需要应用命名空间?**

比如，我们在 front 应用中 login/ 页面命名为login，在cms的 login/页面也命名为login，那么，在反转url的时候就会出错了。

比如我们在cms项目中的urls.py中给应用命名如下

```
from django.urls import path      
from . import views       
app_name="cms"       #给应用命名为cms      
urlpatterns=[      
    path("",views.index),      
    path("login/",views.login)      
]
```


那么，我们在反转的时候，只需要在名字前面指定我们的应用名字就行了

```
login_url = reverse('cms:login')  #指定cms应用下的名字为login的url
```


本文的代码：链接: [https://pan.baidu.com/s/1t1naj5rK1JZg5d66PRvDEg](https://pan.baidu.com/s/1t1naj5rK1JZg5d66PRvDEg) 提取码: 1hd8