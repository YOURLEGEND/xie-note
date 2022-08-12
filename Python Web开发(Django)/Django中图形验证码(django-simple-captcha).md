django-simple-captcha
---------------------

在网站开发的登录页面中，经常会需要使用到图形验证码来验证。在Django中，django-simple-captcha库包提供了图形验证码的使用。

下面我们来讲讲如何使用django-simple-captcha包来图形验证，并且点击图片刷新验证码。

**django-simple-captcha的安装**

```
pip install django-simple-captcha
```


**在settings.py文件中注册captcha**

```
INSTALLED_APPS = [      
    'captcha'      
]
```


**在settings.py文件中设置图形验证码的样式**

```
#字母验证码      
CAPTCHA_IMAGE_SIZE = (80, 45)   # 设置 captcha 图片大小      
CAPTCHA_LENGTH = 4   # 字符个数      
CAPTCHA_TIMEOUT = 1   # 超时(minutes)       
#加减乘除验证码      
CAPTCHA_OUTPUT_FORMAT = '%(image)s %(text_field)s %(hidden_field)s '      
CAPTCHA_NOISE_FUNCTIONS = ('captcha.helpers.noise_null',       1
     'captcha.helpers.noise_arcs', # 线      1
     'captcha.helpers.noise_dots', # 点      1
)      1
CAPTCHA_CHALLENGE_FUNCT = 'captcha.helpers.random_char_challenge'      1
CAPTCHA_CHALLENGE_FUNCT = 'captcha.helpers.math_challenge'      1
CAPTCHA_TIMEOUT = 1
```


**执行数据迁移，在数据表中生成captcha\_captchastore表**

```
python manage.py migrate
```


**在urls.py中添加路由**

```
urlpatterns = [      
    path('captcha/', include('captcha.urls')),       # 图片验证码 路由      
    path('refresh_captcha/', views.refresh_captcha),    # 刷新验证码，ajax      
]
```


下面是源代码。 

**urls.py文件**

```
from django.urls import path      
from django.conf.urls import include      
from App.views import IndexView      
from App import views       
urlpatterns = [      
    path('captcha/', include('captcha.urls')),      
    path('refresh_captcha/',views.refresh_captcha),      
    path('',IndexView.as_view()),      1
]
```


**views.py文件**

```
from django.shortcuts import render      
from django.views.generic import View      
from captcha.models import CaptchaStore      
from captcha.helpers import  captcha_image_url      
from django.http import HttpResponse      
import json       
# 创建验证码      
def captcha():      1
    hashkey = CaptchaStore.generate_key()   #验证码答案      1
    image_url = captcha_image_url(hashkey)  #验证码地址      1
    captcha = {'hashkey': hashkey, 'image_url': image_url}      1
    return captcha      1
#刷新验证码      1
def refresh_captcha(request):      1
    return HttpResponse(json.dumps(captcha()), content_type='application/json')      1
# 验证验证码      1
def jarge_captcha(captchaStr, captchaHashkey):      1
    if captchaStr and captchaHashkey:      2
        try:      2
            # 获取根据hashkey获取数据库中的response值      2
            get_captcha = CaptchaStore.objects.get(hashkey=captchaHashkey)      2
            if get_captcha.response == captchaStr.lower():     # 如果验证码匹配      2
                return True      2
        except:      2
            return False      2
    else:      2
        return False      2
class IndexView(View):      3
    def get(self, request):      3
        hashkey = CaptchaStore.generate_key()  # 验证码答案      3
        image_url = captcha_image_url(hashkey)  # 验证码地址      3
        captcha = {'hashkey': hashkey, 'image_url': image_url}      3
        return render(request, "login.html", locals())      3
    def post(self,request):      3
        capt=request.POST.get("captcha",None)         #用户提交的验证码      3
        key=request.POST.get("hashkey",None)          #验证码答案      3
        if jarge_captcha(capt,key):      3
            return  HttpResponse("验证码正确")      4
        else:      4
            return HttpResponse("验证码错误")
```


**login.html文件**，这里使用 js 动态刷新图形验证码用到了jquery和bootstrap的js，所以，我们提前将jquery和bootstrap放在了static文件夹下。关于如何加载静态文件，传送门——>[Django加载静态文件](https://blog.csdn.net/qq_36119192/article/details/87604921#%E5%8A%A0%E8%BD%BD%E9%9D%99%E6%80%81%E6%96%87%E4%BB%B6)

```
{% load static %}      
<!DOCTYPE html>      
<html lang="en">      
<head>      
    <meta charset="UTF-8">      
    <title>Title</title>      
    <script src="{% static 'bower_components/jquery/dist/jquery.min.js' %}"></script>      
    <script src="{% static 'bower_components/bootstrap/dist/js/bootstrap.min.js'%}"></script>      
</head>      1
<body>      1
    <form action="/" method="post">      1
        <a href="#" class="captcha">      1
            <img src="{{ captcha.image_url }}" alt="点击切换" id="id_captcha" >      1
        </a> <br>      1
        <input type="text" name="captcha" placeholder="验证码"> <br>      1
        <input value="{{ captcha.hashkey }}" name="hashkey" type="hidden" id="id_captcha_0">      1
        <button type="submit" class="btn btn-primary btn-block ">提交</button>      1
    </form>      1
<script>      2
        <!-- 动态刷新验证码js -->      2
        $(document).ready(function(){      2
            $('.captcha').click(function () {      2
                $.getJSON("refresh_captcha/", function (result) {      2
                    $('#id_captcha').attr('src', result['image_url']);      2
                    $('#id_captcha_0').val(result['hashkey'])      2
                });      2
            });      2
        });      2
</script>      3
</body>      3
</html>
```


![](https://img-blog.csdnimg.cn/20190228175212811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[https://blog.csdn.net/qq\_37648632/article/details/83149803](https://blog.csdn.net/qq_37648632/article/details/83149803)

本文所有代码：[https://github.com/xie1997/Django\_captcha](https://github.com/xie1997/Django_captcha)