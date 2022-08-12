**目录**

[requests模拟登陆](#t0)

[Selenium自动化测试登陆](#t1)

* * *

> 环境：python3.7 windows

### requests模拟登陆

我们登陆DVWA的时候，看似只有一步：访问网站，输入用户名和密码，登陆成功则跳转到新页面。  
其实这中间分了三步。

![](https://img-blog.csdn.net/2018100712543250?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
1、访问网站的瞬间，浏览器会先向网站的登录页面发送GET请求，然后服务器会返回带有 token 和 cookies 的信息给浏览器

![](https://img-blog.csdn.net/20181007095659235?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2、浏览器收到服务器返回的信息后，从中提取token和cookie。  
   当我们输入用户名和密码登录页面的时候，这次请求信息包含了我们输入的用户名和密码还有之前提取到的cookie和token信息。  
如果输入的用户名和密码正确的话，服务器回复302重定向，将浏览器重定向到index.php页面。  
如果输入的用户名和密码错误的话，服务器回复302重定向，将浏览器重定向到login.php登录页面

![](https://img-blog.csdn.net/20181007100729804?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3、如果用户名和密码正常，浏览器发送GET请求，请求index.php页面  
     如果用户名和密码错误，浏览器发送GET请求，请求login.php页面

![](https://img-blog.csdn.net/20181007100809926?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以，我们用python模拟登录DVWA的时候，先模拟向服务器发送登录请求，然后提取出服务器返回的 token 和 cookie。再利用获得到的cookie和token结合我们的用户名和密码进行登录。

### 代码实现：

```
# -*- coding: utf-8 -*-      
"""      
Created on Sat Oct  6 17:01:15 2018      
@author: 小谢      
"""      
import urllib      
import requests      
from bs4 import BeautifulSoup       1
##第一步，先访问 http://127.0.0.1/login.php页面，获得服务器返回的cookie和token      1
def get_cookie_token():      1
    headers={'Host':'127.0.0.1',      1
             'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:55.0) Gecko/20100101 Firefox/55.0',      1
             'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',      1
             'Accept-Lanuage':'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',      1
             'Connection':'keep-alive',      1
             'Upgrade-Insecure-Requests':'1'}      1
    res=requests.get("http://127.0.0.1/login.php",headers=headers)      1
    cookies=res.cookies      2
    a=[(';'.join(['='.join(item)for item in cookies.items()]))]   ## a为列表，存储cookie和token      2
    html=res.text      2
    soup=BeautifulSoup(html,"html.parser")      2
    token=soup.form.contents[3]['value']      2
    a.append(token)      2
    return a       2
a=get_cookie_token()              ##a列表中存储了服务器返回的cookie和toke       2
##第二步模拟登陆      3
def Two():      3
    headers={'Host':'127.0.0.1',      3
             'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:55.0) Gecko/20100101 Firefox/55.0',      3
             'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',      3
             'Accept-Lanuage':'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',      3
             'Connection':'keep-alive',      3
             'Content-Length':'88',      3
             'Content-Type':'application/x-www-form-urlencoded',      3
             'Upgrade-Insecure-Requests':'1',      3
             'Cookie':a[0],      4
             'Referer':'http://127.0.0.1/login.php'}      4
    values={'username':'admin',      4
            'password':'password',      4
            'Login':'Login',      4
            'user_token':a[1]      4
        }      4
    data=urllib.parse.urlencode(values)      4
    resp=requests.post("http://127.0.0.1/login.php",data=data,headers=headers)      4
    return       4
Two()       5
#重定向到index.php      5
headers={'Host':'127.0.0.1',      5
         'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64; rv:55.0) Gecko/20100101 Firefox/55.0',      5
         'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',      5
         'Accept-Lanuage':'zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3',      5
         'Connection':'keep-alive',      5
         'Upgrade-Insecure-Requests':'1',      5
         'Cookie':a[0],      5
         'Referer':'http://127.0.0.1/login.php'}      6
response=requests.get("http://127.0.0.1/index.php",headers=headers)      6
print(response.text)
```


实际用python模拟的话，他会多发两个包，具体为什么多发包我也不是很清楚，但是最后效果是达到了。成功用python模拟登录了DVWA。

![](https://img-blog.csdn.net/20181007131416284?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Selenium自动化测试登陆
---------------

代码实现：

```
# -*- coding: utf-8 -*-      
"""      
Created on Sun Oct  7 16:35:22 2018      
@author: 小谢      
"""      
from selenium import webdriver      
import time       
driver=webdriver.Chrome("G:\Anaconda3-5.3.0\chromedriver.exe")      1
print("Login DVWA GO!")      1
driver.get("http://127.0.0.1/login.php")      1
print("get web in ")      1
time.sleep(3)      1
driver.find_element_by_name("username").send_keys("admin")   ##找到输入用户名的input框，并且输入用户名      1
driver.find_element_by_name("password").send_keys("password")  ##找到输入密码的input框，并且输入密码      1
driver.find_element_by_name("Login").click()      1
print("登录成功...")      1
time.sleep(10)                   ##登录成功后延时十秒退出      1
driver.close()
```
