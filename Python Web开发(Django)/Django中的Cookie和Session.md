**目录**

[HttpRequest和HttpResponse对象](#t0)

[Django中Cookie的设置](#t1)

[Django中Session的设置](#t2) 

[Django中Cookie+Session的设置](#t3)

* * *

关于Cookie和[Session](https://so.csdn.net/so/search?q=Session&spm=1001.2101.3001.7020)，传送门——> [Cookie、Session和Token的区别](https://blog.csdn.net/qq_36119192/article/details/84977902)

HttpRequest和HttpResponse对象
--------------------------

Django使用请求和响应对象来通过系统传递状态。

当请求页面时，Django创建一个[`HttpRequest`](#django.http.HttpRequest)包含有关请求的元数据的对象。然后Django加载适当的视图，将[`HttpRequest`](#django.http.HttpRequest)第一个参数传递给view函数。每个视图都负责返回一个[`HttpResponse`](#django.http.HttpResponse)对象。

### Django中Cookie的设置

**服务端响应设置Cookie**

```
#普通设置      
HttpResponse.set_cookie(key,value=''，max_age = None，expires = None，path ='/'，domain = None，secure = None，httponly = False，samesite = None)       
#加盐   普通cookie是明文传输的，可以直接在客户端直接打开，所以需要加盐，解盐之后才能查看      
HttpResponse.set_signed_cookie（key，value =''，salt='盐值' ,max_age = None，expires = None，path ='/'，domain = None，secure = None，httponly = False，samesite = None）
```


*   max\_age=1 ：cookie生效的时间，单位是秒
*   expires:具体过期日期  
*   path='/'：指定那个url可以访问到cookie；‘/’是所有； path='/'
*   4domain=None（None代表当前域名）：指定那个域名以及它下面的二级域名（子域名）可以访问这个cookie
*   secure=False：https安全相关
*   httponly=False：限制只能通过http传输，JS无法在传输中获取和修改

**客户端获取Cookie**

```
#普通获取      
HttpRequest.COOKIES['key']       
#获取加盐的cookie      
HttpRequest.get_signed_cookie（key，default = RAISE_ERROR，salt =''，max_age = None）：返回已签名cookie的cookie值，default是设置默认值(当没有获取到cookie的时候)，salt盐值提供加密保护
```


**客户端删除Cookie**

```
HttpResponse.delete_cookie（key，path ='/'，domain = None） #删除cookie中的key值，path是指定路径，domain是指定域
```


### Django中Session的设置 

在用session之前，确保你的数据库同步了。即确保你的数据库中有 django\_session 这张表。

**设置session**

```
request.session['username']='xie'
```


**获取session** 

```
username=request.session.get('username','anybody')  #如果没有获取到值的话，则设置默认值anybody
```


**删除session** 

```
del request.session['username']
```


**其他方法** 

```
# 所有 键、值、键值对       
request.session.keys()      
request.session.values()      
request.session.items()      
request.session.iterkeys()      
request.session.itervalues()      
request.session.iteritems()       1
# 用户session的随机字符串      1
request.session.session_key       1
# 将所有Session失效日期小于当前日期的数据删除      1
request.session.clear_expired()       1
# 检查 用户session的随机字符串 在数据库中是否      1
request.session.exists("session_key")       1
# 删除当前用户的所有Session数据      2
request.session.delete("session_key")       2
# 注销使用      2
request.session.clear()       2
#设置超时时间，set_expiry(value)方法是在最后一次刷新开始计算      2
request.session.set_expiry(value)
```


### Django中Cookie+Session的设置

cookie看似解决了HTTP（短连接、无状态）的会话保持问题，但把全部用户数据保存在客户端，存在安全隐患，于是cookie+session出现了！我们可以把关于用户的数据保存在服务端，在客户端cookie里加一个sessionID（随机字符串），以此来验证用户的身份。

**cookie+session的工作流程：**

1.  当用户成功登录网站时，服务端生成了用户的登录信息session，并且该登录信息对应着一个session\_id
2.  服务端把 session\_id放在cookie中，发给浏览器(自动过程，无需我们设置)
3.  客户端下次访问服务端时，服务端取出该session\_id对应的登录信息，来判断用户的登录状态

我们现在模拟一个注册登录的页面，假设用户名不能重复，我们将用户成功登录后的状态写入session中。当客户端访问需要权限的页面时，然后服务器再从此次session会话中取出客户端的状态来比较，如果存在，则曾经登录过。

**#urls.py**

```
urlpatterns = [      
    path('', views.Register.as_view()),      
    path('login/',views.Login.as_view()),      
    path('index/',views.index),      
    path('logout/',views.logout)      
]
```


**#views.py**

```
from django.shortcuts import render      
from django import forms      
from django.http import HttpResponseRedirect,HttpResponse      
from django.views.generic import View      
from app.models import User       
#定义一个form表单      
class UserForm(forms.Form):      
    username=forms.CharField(label='用户名')      1
    password=forms.CharField(label='密码',widget=forms.PasswordInput)       1
#注册      1
class Register(View):      1
    def get(self,request):      1
        form=UserForm()      1
        return render(request,"register.html",context={"form":form})      1
    def post(self,request):      1
        form=UserForm(request.POST)      1
        if form.is_valid():      2
            username=form.cleaned_data.get('username')      2
            password=form.cleaned_data.get('password')      2
            User.objects.create(username=username,password=password)  #写入数据库中      2
            return HttpResponseRedirect('/login')         #重定向到登录页面      2
        else:      2
            form=UserForm()      2
            return render(request,"register.html",context={"form":form})      2
#登录      2
class Login(View):      2
    def get(self,request):      3
        form=UserForm()      3
        return render(request,"login.html",context={"form":form})      3
    def post(self,request):      3
        form=UserForm(request.POST)      3
        if form.is_valid():      3
            username=form.cleaned_data.get('username')      3
            password=form.cleaned_data.get('password')      3
            users=User.objects.filter(username=username,password=password)  #查询数据库      3
            if users:      3
                response=HttpResponseRedirect("/index")      4
                #如果登录成功，将登录状态is_login设置为True，该此时服务端会自动向客户端返回的Cookie中加入此session_id      4
                request.session['is_login']=True      4
                request.session['username']=username      4
                #设置cookie ,自己可以随便设置字段和值      4
                response.set_cookie("id","1")      4
                response.set_cookie("age","20")      4
                return  response      4
            else:      4
                return HttpResponseRedirect("/login")      4
        else:      5
            form=UserForm()      5
            return render(request,"register.html",context={"form":form})      5
def index(request):      5
    if request.session.get('is_login',None):      5
         username=request.session.get('username',None)      5
         content={'username':username}      5
         return render(request,"index.html",context=content)      5
    else:      5
        form=UserForm      5
        return render(request, "login.html", context={"form": form})       6
def logout(request):      6
    response=HttpResponse('注销成功！')      6
    try:      6
        del request.session['username']            #删除Session      6
    except Exception as e:      6
        print(e)      6
    finally:      6
        return response
```


![](https://img-blog.csdnimg.cn/20190227153014376.png)

![](https://img-blog.csdnimg.cn/20190227153026750.png)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-137)[Web应用开发](https://edu.csdn.net/skill/python/python-3-137) [Django](https://edu.csdn.net/skill/python/python-3-137)84853 人正在系统学习中