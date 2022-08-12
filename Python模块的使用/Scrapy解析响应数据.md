**目录**

[解析响应数据](#t0)

[使用xpath选择器解析](#t1)

[使用CSS选择器解析](#t2)

* * *

解析响应数据
------

对于服务器端返回的数据我们该如何处理呢？我们需要提取从服务器返回的数据，解析的方向有下面这几种：

*   普通文本操作
    
*   [正则表达式](https://so.csdn.net/so/search?q=%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1001.2101.3001.7020)：re
    
*   Dom树操作：[BeautifulSoup](https://so.csdn.net/so/search?q=BeautifulSoup&spm=1001.2101.3001.7020)(解析速度快慢)
    
*   [Xpath](https://so.csdn.net/so/search?q=Xpath&spm=1001.2101.3001.7020)选择器：lxml (基于lxml库建立的，并且简化了API接口，解析速度快)
    

有如下网页，html代码如下

```
 <html>      
 <head>      
    <title>谢公子的小黑屋</title>      
 </head>      
 <body>      
   <h2>这是标题</h2>      
   <p class="xie" name="p标签">你好，世界</p>      
   <img src="1.jpg">      
   <div class="one">      1
     <div class="two">      1
         <div class="three">      1
             <div class="title">这是第一个标题</div>      1
             <div class="time">2019-1-1 01:11:11</div>      1
         </div>      1
     </div>      1
     <div class="two">      1
         <div class="three">      1
             <div class="title">这是第二个标题</div>      1
             <div class="time">2019-2-2 02:22:22</div>      2
         </div>      2
     </div>      2
     <div class="two">      2
         <div class="three">      2
             <div class="title">这是第三个标题</div>      2
             <div class="time">2019-3-3 03:33:33</div>      2
         </div>      2
     </div>      2
   </div>      2
 </body>      3
 </html>
```


返回的 response 是一个 class 类

![](https://img-blog.csdnimg.cn/20200101130217925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们可以用 response 的 text 方法，得到的是一个 str 字符串对象

![](https://img-blog.csdnimg.cn/20200101130232541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用xpath选择器解析

将字符串转换为Selector类，即可使用 xpath [选择器](https://so.csdn.net/so/search?q=%E9%80%89%E6%8B%A9%E5%99%A8&spm=1001.2101.3001.7020)。

```
from parsel import Selector       
response=requests.get(url="xx")      
select=Selector(response.text)      
select.xpath("")
```

```
// ：从当前节点选取子孙节点，如果符号前面没路径，表示整个文档      
/ ：从当前节点选取直接子节点      
. ：选取当前节点      
.. ：选取当前节点父节点      
@ ：选取属性      
//* ：整个HTML文本中的所有节点      
extract()：返回response类的data数据      
text()：返回标签的内容        1
举例：      1
response.xpath('/html/body/div') #选取body下的所有div      1
response.xpath('//a') #选中文档所有a      1
response.xpath('/html/body//div') #选中body下的所有节点中的div，无论在什么位置      1
response.xpath('//a/text()') #选取所有a的文本      1
response.xpath('/html/div/*') #选取div的所有元素子节点      1
response.xpath('//div/*/img') #选取div孙节点的所有img      1
response.xpath('//img/@src') #选取所有img的src属性      1
response.xpath('//a[1]/img/@*') #选取第一个a下img的所有属性      1
response.xpath('//a[2]') #所有a中的第2个      2
response.xpath('//a[last()]') #所有a中的最后一个 ('/a[last()-1]')#倒第二个 （'//a[position()<=3]'）#使用position函数选中前三个 （'//div[@id]'）#选中所有含有id属性的div （'//div[@id="song"]'）#选中所有id属性为song的div      2
response.xpath('//p[contains(@class,'song')]') #选择class属性中含有‘song’的p元素      2
response.xpath('//div/a | //div/p') 或者，页面中可能是a可能是p
```


**例：选取 title**

```
response.xpath("//title")      
response.xpath("//title").extract()      
response.xpath("//title").extract()[0]      
response.xpath("//title/text()").extract()[0]
```


![](https://img-blog.csdnimg.cn/20200101130251918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

选取h2标签

![](https://img-blog.csdnimg.cn/2020010113032766.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

选取每个div的标题和时间

![](https://img-blog.csdnimg.cn/20200101130339604.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 使用CSS选择器解析

相关文章：[scrapy提取数据](https://www.cnblogs.com/sunBinary/p/11147275.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-153)[网络爬虫](https://edu.csdn.net/skill/python/python-3-153)[Scrapy框架](https://edu.csdn.net/skill/python/python-3-153)84711 人正在系统学习中