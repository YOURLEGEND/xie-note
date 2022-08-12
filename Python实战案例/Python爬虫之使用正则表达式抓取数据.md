**目录**

[匹配标签](#t0)

[匹配title标签](#t1)

[a标签](#t2)

[table标签](#t3)

[匹配标签里面的属性](#t4)

[匹配a标签里面的URL](#t5)

[匹配img标签里的 src](#t6)

* * *

相关文章：[Linux中的正则表达式](https://blog.csdn.net/qq_36119192/article/details/82989691#%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)

                 [Python中的正则表达式](https://blog.csdn.net/qq_36119192/article/details/83751695#flags)

**实例：**

**匹配标签**
--------

### **匹配title标签**

匹配网页的 <title></title> 标签，也就是网页的标题。  .\*？ 就是匹配1个或多个字符，也就是这里不能是空的。当加入括号的话，就是代表取值了 (.\*?)

```
import re      
import requests       
resp=requests.get("http://www.baidu.com")      
resp.encoding="utf-8"  #设置编码格式为utf-8      
html=resp.text         
title=re.findall(r'<title>.*?</title>',html)  #匹配 <title></title>      
for t in title:      
    print(t)      1
title_value=re.findall(r'<title>(.*?)</title>',html)  #匹配 <title></title>里面的内容      1
for t in title_value:      1
    print(t)      1
#####################################################################      1
<title>百度一下，你就知道</title>      1
百度一下，你就知道
```


### a标签

匹配<a  href="" ></a> ，并且获取a标签里面的内容

```
import re      
import requests       
resp=requests.get("http://www.baidu.com")      
resp.encoding="utf-8"  #设置编码格式为utf-8      
html=resp.text         
urls = re.findall(r"<a.*?>.*?<\/a>", html)   #匹配所有的a标签      
for u in urls:      1
    print(u)       1
texts = re.findall(r"<a.*?>(.*?)</a>", html)   #获取超链接<a>和</a>之间内容      1
for t in texts:      1
    print(t)      1
#######################################################################################      1
<a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a>      1
<a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a>      1
<a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a>      1
<a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a>      2
<a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a>      2
<a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a>      2
<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>      2
<a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a>      2
<a href=http://home.baidu.com>关于百度</a>      2
<a href=http://ir.baidu.com>About Baidu</a>      2
<a href=http://www.baidu.com/duty/>使用百度前必读</a>      2
<a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>      2
新闻      2
hao123      3
地图      3
视频      3
贴吧      3
登录      3
登录      3
更多产品      3
关于百度      3
About Baidu      3
使用百度前必读      3
意见反馈
```


### table标签

抓取 <table></table> 表格中的内容。

假设现在有这么一个网页

```
<html>      
<table class="table">          
            <tr>      
                <th>姓名</th>      
                <th>性别</th>      
            </tr>      
            <tr>      
                <td>小谢</td>      
                <td>男</td>      1
            </tr>      1
            <tr>      1
                <td>小红</td>      1
                <td>女</td>      1
            </tr>      1
</table>      1
</html>
```


匹配代码

```
import re      
import requests       
resp=requests.get("http://127.0.0.1/1.html")      
resp.encoding="utf-8"  #设置编码格式为utf-8      
html=resp.text         
#匹配table标签      
tables=re.findall(r"<table.*?>.*?<\/table>",html,re.M|re.S)      1
for table in tables:      1
    print(table)       1
#匹配<tr></tr>之间的内容      1
trs=re.findall(r"<tr>(.*?)</tr>",html,re.S|re.M) #因为<tr>标签大多数不是在同一行，所以要加 re.S和re.M多行匹配      1
for tr in trs:      1
    print(tr)       1
#匹配<th></th>之间的内容      1
for row in trs:      2
    ths=re.findall(r"<th>(.*?)</th>",row,re.S|re.M)      2
    for th in ths:      2
        print(th)       2
#匹配<td></td>之间的内容      2
for row in trs:      2
    tds=re.findall(r"<td>(.*?)</td>",row,re.S|re.M)      2
    for td in tds:      2
        print(td)      2
##################################################################################      3
<table class="table">          3
            <tr>      3
                <th>姓名</th>      3
                <th>性别</th>      3
            </tr>      3
            <tr>      3
                <td><B>小谢</B></td>      3
                <td>男<br/></td>      3
            </tr>      3
            <tr>      4
                <td><B>小红</B></td>      4
                <td>女<br/></td>      4
            </tr>      4
</table>       4
                <th>姓名</th>      4
                <th>性别</th>       4
                <td>小谢</td>      5
                <td>男</td>       5
                <td>小红</td>      5
                <td>女</td>       5
姓名      5
性别       5
小谢      6
男      6
小红      6
女
```


匹配标签里面的属性
---------

### 匹配a标签里面的URL

假如现在有网页

```
<html>      
	<a href="http://www.baidu.com">百度一下，你就知道</a>      
	<a href="http://www.mi.com">小米官网</a>      
</html>
```

```
import re      
import requests       
resp=requests.get("http://127.0.0.1/1.html")      
resp.encoding="utf-8"  #设置编码格式为utf-8      
html=resp.text         
urls=re.findall(r"(?<=href=\").+?(?=\")|(?<=href=\').+?(?=\')",html,re.I|re.S|re.M)  #匹配 href=""      
for url in urls:      1
    print(url)      1
###################################################################################      1
http://www.baidu.com      1
http://www.mi.com
```


### 匹配img标签里的 src

加入现在有网页

```
<html>      
	<img src="http://t1.27270.com/uploads/tu/201811/310/f3e9db6b68.jpg"  name="美女"/>      
	<img src="http://t1.27270.com/uploads/tu/201811/229/ea7fda100e.jpg" />      
</html>
```


匹配代码：

```
import re      
import requests      
resp=requests.get("http://127.0.0.1/1.html")      
resp.encoding="utf-8"  #设置编码格式为utf-8      
html=resp.text         
srcs=re.findall(r'src="(.*?)"',html,re.I|re.S|re.M)      
for src in srcs:      
    print(src)      1
##################################################################      1
http://t1.27270.com/uploads/tu/201811/310/f3e9db6b68.jpg      1
http://t1.27270.com/uploads/tu/201811/229/ea7fda100e.jpg       1
#假如要获取图片的名字，也就是上面的 f3e9db6b68.jpg 或者  ea7fda100e.jpg       1
import re      1
import requests      1
resp=requests.get("http://127.0.0.1/1.html")      2
resp.encoding="utf-8"  #设置编码格式为utf-8      2
html=resp.text         2
srcs=re.findall(r'src="(.*?)"',html,re.I|re.S|re.M)      2
for src in srcs:      2
    name=src.split("/")[-1]      2
    print(name)      2
##################################################################      2
f3e9db6b68.jpg      2
ea7fda100e.jpg
```


 
-

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-148)[网络爬虫](https://edu.csdn.net/skill/python/python-3-148)[正则表达式](https://edu.csdn.net/skill/python/python-3-148)84853 人正在系统学习中