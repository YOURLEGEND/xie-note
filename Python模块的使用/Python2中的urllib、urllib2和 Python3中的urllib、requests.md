**目录**

[Python2.x中](#t0)

[urllib和urllib2](#t1)

[常用方法和类](#t2)

[Python3.x中](#t3)

[urllib](#t4)

[requests](#t5)

* * *

Python2.x中
----------

### urllib和urllib2

[urllib](https://so.csdn.net/so/search?q=urllib&spm=1001.2101.3001.7020) 和 urllib2 是 python2.x 自带的模块，提供了一系列用于操作URL的功能。  
urllib 和 urllib2 都是接受URL请求的相关模块，但是urllib2可以接受一个Request类的实例来设置URL请求的 headers，urllib 仅可以接受URL。这意味着，urllib不可以伪装你的User Agent 字符串等。  
但是 urllib 提供 urlencode 方法用来对数据进行url编码，而 urllib2 没有，所以 urllib 经常和 urllib2 一起使用。

### 常用方法和类

urllib2.urlopen( url  \[, data\]  \[, timeout\] )  
urlopen方法是urllib2模块最常用也最简单的方法，它打开URL网址。调用urlopen函数对请求的url返回一个response对象。这个response类似于一个file对象，所以用 .read() 函数可以操作这个response对象

*   url 参数可以是一个字符串 url 或者是一个Request对象。url 必须是 http:// 的格式
*   对于可选参数 data，表示以 post 方式提交到 url 的数据。不填则为 get 方式提交
*   对于可选的参数 timeout，阻塞操作以秒为单位，如果没有指定，将使用设置的全局默认timeout值。

```
import urllib2       
#设置代理      
proxies={"http":"http://127.0.0.1:8080"}      
proxy_s=urllib2.ProxyHandler(proxies)           
opener=urllib2.build_opener(proxy_s)            
urllib2.install_opener(opener)       
#获取页面      1
response = urllib2.urlopen('https://www.baidu.com',timeout=3)  #超时时间3秒      1
html = response.read()
```


urllib2.urlopen( urllib2.Request   \[,timeout\])

*   urllib2.Request表示的是一个urllib2.Request对象
*   对于可选的参数 timeout，阻塞操作以秒为单位，如果没有指定，将使用设置的全局默认timeout值

urlopen方法也可通过建立了一个Request对象来明确指明想要获取的url。

```
import urllib2      
req = urllib2.Request('http://python.org/')      
response = urllib2.urlopen(req,timeout=3)      
the_page = response.read()
```


这里用到了urllib2.Request 类，对于上例，我们只通过了URL实例化了Request类的对象，其实Request类还有其他的参数 。

class urllib2.Request(url  \[, data\]  \[, headers\]  \[, origin\_req\_host\]  \[, unverifiable\])

*   url 参数可以是一个字符串 url 或者是一个Request对象。url 必须是 http:// 的格式
*   对于可选参数data，data 是一个字符串，指定额外的数据发送到服务器，当请求含有data参数时，HTTP的请求为POST，而不是GET。而data数据需要先进行URL编码处理，这就需要urllib.urlencode()函数了
*   可选参数headers是字典类型，头字典可以作为参数在request时直接传入，也可以把每个键和值作为参数调用add\_header()方法来添加。
*   可选参数origin\_req\_host是RFC2965定义的源交互的request-host。默认的取值是cookielib.request\_host(self)。这是由用户发起的原始请求的主机名或IP地址。例如，如果请求的是一个HTML文档中的图像，这应该是包含该图像的页面请求的request-host。
*   可选参数unverifiable代表请求是否是无法验证的，它也是由RFC2965定义的。默认值为false。一个无法验证的请求是，其用户的URL没有足够的权限来被接受。例如，如果请求的是在HTML文档中的图像，但是用户没有自动抓取图像的权限，unverifiable的值就应该是true。

```
import urllib      
import urllib2      
requrl = 'http://www.python.org/'      
header = { 'User-Agent' : 'Mozilla/4.0 compatible; MSIE 5.5; Windows NT',      
           'Referer'    : 'http://www.baidu.com/login.php'}      
values = {'name': 'Tom',      
          'sex' : 'Man',      
          'id'  : '10' }      
data = urllib.urlencode(values)  #对数据进行URL编码处理      1
req  = urllib2.Request(url=requrl, data=data，headers=header)      1
response = urllib2.urlopen(req)      1
the_page = response.read()       1
##还可以使用add_header方法添加      1
req = urllib2.Request(url=requrl,data=data,headers)      1
req.add_header('Referer', 'http://www.python.org/')      1
req.add_header('User-Agent','Mozilla/4.0 compatible; MSIE 5.5; Windows NT')      1
response = urllib2.urlopen(req)      1
the_page = response.read()
```


python2.X环境，使用urllib和urllib2进行发包的相关文章：[Python脚本自动化破解大白鲨摄像头(Shodan)](https://blog.csdn.net/qq_36119192/article/details/84193564) 

Python3.x中
----------

### urllib

在python3.x中，把 http 相关的所有包打包成了2个包：http 和  urllib 。也就是说urllib和urllib2合成了一个urllib包

http 会处理所有客户端--服务器http请求的具体细节，其中：

*   client 会处理客户端的部分
*   server 会协助你编写Python web服务器程序
*   cookies 和cookiejar会处理cookie，cookie可以在请求中存储数据

urllib 是基于 http 的高层库，它有以下三个主要功能：

*   reques t处理客户端的请求
*   response 处理服务端的响应
*   parse 会解析url

常见的类和方法

request.urlopen(url \[, data\] \[, timeout\])    

参数选项和python2.x中的 urllib.urlopen 一样

```
from urllib import request       
response = request.urlopen('http://python.org/')      
html = response.read()
```


request.urlopen( request.Request ) 

参数选项和python2.x中的 urllib.urlopen 一样

```
from urllib import request       
req = request.Request('http://python.org/')      
response = request.urlopen(req)      
the_page = response.read()
```


 自定义 headers 和 data

```
from urllib import parse      
from urllib import request       
url = 'http://www.someserver.com/cgi-bin/register.cgi'      
headers = { 'Host': '192.168.153.130',      
            'User-Agent' : 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)',      
            'Referer'    : 'http://www.python.org/',      
            'Accept'     : '*/*' }      
values = {'name': 'Tom',      1
          'sex' : 'Man',      1
          'id'  : '10' }      1
data = urllib.parse.urlencode(values)      1
req = urllib.request.Request(url, data)      1
response = urllib.request.urlopen(req)      1
the_page = response.read()      1
print(the_page.decode("utf8"))       1
urllib中进行URL编码和解码还可以使用quote() 和 unquote()函数      2
a=urllib.parse.quote("hello,word")   #将字符串进行编码      2
b=urllib.parse.unquote(a)            #将字符串进行解码
```


### requests

而[requests](https://so.csdn.net/so/search?q=requests&spm=1001.2101.3001.7020)库则是非常优秀的第三方库，它使用 Apache2 Licensed 许可证的 HTTP 库。Requests 使用的是 urllib(python3.x中的urllib)，因此继承了它的所有特性。Requests 支持 HTTP 连接保持和连接池，支持使用 cookie 保持会话，支持文件上传，支持自动确定响应内容的编码，支持国际化的 URL 和 POST 数据自动编码。现代、国际化、人性化

requests模块中可以有很多种请求网页的方式

*   requests.get('http://www.baidu.com')
*   requests.post('http://www.baidu.com')
*   requests.put('http://www.baidu.com')
*   requests.delete('http://www.baidu.com')
*   requests.head('http://www.baidu.com')
*   requests.options(http://www.baidu.com')

```
import requests       
headers = { 'Host': '192.168.153.130',      
            'User-Agent' : 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)',      
            'Referer'    : 'http://www.python.org/',      
            'Accept'     : '*/*' }      
values = {'name': 'Tom',      
          'sex' : 'Man',      
          'id'  : '10' }      1
proxie={"http":"http://127.0.0.1:8080"}        #设置代理       1
res=requests.get('http://www.baidu.com', data=values,headers=headers，timeout=0.1,proxies=proxie)       1
res.status_code    ##状态码      1
res.url            ##请求的url      1
res.request.headers    ##请求头信息，返回的是一个字典对象，不修改的话，默认是python的请求头      1
res.headers        ##响应头信息，返回的是一个字典对象      1
responce.headers['Content-Type']  #查看响应头的具体部分      1
res.cookies        ##cookie信息，返回的是一个字典对象      2
res.text           ##响应内容的字符串形式，即返回的页面内容      2
res.content        ##响应内容的二进制形式        2
res.encoding='utf-8'   ##设置响应的编码      2
res.apparent_encoding  ##从内容中分析出的响应内容编码方式（备选编码方式）
```


相关文章：[Python3——requests模块详解](https://blog.csdn.net/qq_36119192/article/details/82951408)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-151)[网络爬虫](https://edu.csdn.net/skill/python/python-3-151)[requests](https://edu.csdn.net/skill/python/python-3-151)84785 人正在系统学习中