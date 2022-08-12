**目录**

[BeautifulSoup](#t0)

[Tag](#t1)

[NavigableString](#t2)

[BeautifulSoup](#BeautifulSoup)

[Comment](#t3)

[遍历文档树](#t4)

[直接子节点](#t5)

[所有子孙节点](#t6)

[​ 节点内容](#%E2%80%8B%C2%A0%E8%8A%82%E7%82%B9%E5%86%85%E5%AE%B9)

[搜索标签](#t7)

[CSS选择器](#t8)

* * *

BeautifulSoup
-------------

**BeautifulSoup** 是Python的一个库，最主要的功能就是从网页爬取我们需要的数据。[BeautifulSoup](https://so.csdn.net/so/search?q=BeautifulSoup&spm=1001.2101.3001.7020)将 html 解析为对象进行处理，全部页面转变为字典或者数组，相对于正则表达式的方式，可以大大简化处理过程。

```
from bs4 import BeautifulSoup
```


BeautifulSoup将htmll对象转成对象的过程

```
response=requests.get("https://www.baidu.com")  ##获得网页的回应      
html=response.text                         ##获得网页的源代码      
soup=BeautifulSoup(html,"html.parser")     ##将网页转化成BeautifSoup对象
```


Beautiful Soup将复杂的HTML文档转换成一个复杂的树形结构，每个节点都是python对象，所有对象可以归纳为4种

*   Tag
*   NavigableString
*   BeautifulSoup
*   Comment

### Tag

Tag 是什么？通俗点讲就是 HTML 中的一个个标签，例如

```
<html>      
<head>      
   <title>谢公子的小黑屋</title>      
</head>      
<body>      
  <h2>这是标题</h2>      
  <p class="xie" name="p标签">你好，世界</p>      
  <img src="1.jpg">      
  <form action='action.php' method="get">      1
  用户名：<input type="text" name="username" />    <br/>      1
  密码：  <input type="password" name="passwd" />  <br/>      1
          <input type="submit" value="登录" />      1
  </form>      1
</body>      1
</html>
```


上面的 title 、p 等等 HTML 标签加上里面的内容就是 Tag，下面我们来感受一下怎样用 Beautiful Soup 来方便地获取 Tags

```
print( soup.title )           ##打印出网页的title标签      
print( soup.p)                 ##打印出网页的p标签
```


![](https://img-blog.csdn.net/20181006193812305?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们可以利用soup加标签名轻松地获取这些标签的内容，但是它查找的是在所有内容中的第一个符号要求的标签  
对于 Tag，它有两个重要的属性，是 name 和 attrs

```
soup.p.name              ##p标签的名字      
soup.p.string            ##p标签的内容      
soup.p.attrs             ##p标签的属性，返回的是一个字典      
soup.p['class']          ##p标签的class属性      
soup.p.get('class')      ##p标签的class属性,和上面的等价      
soup.p['class']="newClass"   ##修改p标签的class属性      
del soup.p['class']      ##删除p标签的class属性
```


![](https://img-blog.csdn.net/20181006174246417?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### NavigableString

既然我们已经得到了标签的内容，那么如果我们想要获取标签内部的文字怎么办呢？很简单，用 .string  即可 

```
print(soup.p)      
print(soup.p.string)         ##打印出p标签的内容      
## <p class="xie" name="p标签">你好，世界！</p>      
## 你好，世界！
```


### BeautifulSoup

BeautifulSoup对象表示的是一个文档的全部内容。大部分时候，可以把它当做一个Tag对象，是一个特殊的Tag，我们可以分别获取它的类型，名称，以及属性

![](https://img-blog.csdn.net/20181006184917491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Comment

Comment对象是一个特殊类型的NavigableString对象，其实输出的内容仍然不包括注释符号，但是如果不好好处理它，可能会对我们的文本处理造成意想不到的麻烦

现在有这么一个网页

```
<html>      
<body>      
  <p class="xie" name="p标签"><!--你好，世界！--></p>      
</body>      
</html>
```


![](https://img-blog.csdn.net/2018100618574756?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
p标签里的内容实际上是注释，但是如果我们利用 .string 来输出它的内容，我们发现它已经把注释给去掉了，所以这可能会带给我们不必要的麻烦。如果标签中有注释，则是 comment 对象，没有注释则不是 comment对象所以我们在使用前判断是不是属于 comment类型 。 

```
if type(soup.p.string) != bs4.element.Comment:      
    print (soup.p.string)
```


遍历文档树
-----

### 直接子节点

.contents 返回的是一个list列表，可以通过索引方式获取

.children 返回的是一个list生成器，我们需要[遍历](https://so.csdn.net/so/search?q=%E9%81%8D%E5%8E%86&spm=1001.2101.3001.7020)获取所有子孙节点

```
print(soup.body.contents)     ##打印出body标签内的所有标签，返回的是一个列表，包含\n      
print(soup.body.contents[0])  ##我们可以通过索引的方式获取       
print(soup.body.children)     ##返回的是list生成器，需要遍历获取值      
for child in soup.body.children:      
    print(child)
```


![](https://img-blog.csdn.net/20181006192733729?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### 所有子孙节点

.contents和.children属性仅包含tag的直接子节点，.descendants属性可以对所有tag的子孙节点进行递归循环，和children类似

```
for child in soup.body.descendants:   ##遍历所有子孙节点标签      
      print( child )
```


### ![](https://img-blog.csdn.net/20181006193200377?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

### 节点内容

如果一个标签里面没有标签了，那么.string就会返回标签里面的内容。如果标签里面还有其他的标签，则会返回 None

![](https://img-blog.csdn.net/20181006193537718?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

搜索标签
----

**find\_all( name , attrs ,recursive , text , \*\*kwargs)  搜素当前页面符合过滤器条件的第一个标签**

**find\_all( name , attrs ,recursive , text , \*\*kwargs)  搜素当前页面符合过滤器条件的所有标签**  
   
name参数，查找所有名字为name的 tag，字符串对象会被自动忽略掉  
(A) 传字符串  
最简单的过滤器是字符串，在搜索方法中传入一个字符串参数，find\_all方法会查找所有与字符串完整匹配的内容

```
print(soup.find_all('a'))    ##查找所有的a标签，返回的是一个list列表      
print(soup.find_all('a',class_='xie'))   ##查找class='xie'的a标签
```


(B)传正则表达式  
如果传入正则表达式作为参数，find\_all方法会通过正则表达式的match()来匹配内容

```
for tag in soup.find_all(re.compile("^b")):  ##找出所有以b开头的标签      
    print(tag.name)
```


(c)传列表  
如果传入列表参数，find\_all将会与列表中任一元素匹配的内容返回。

```
print(soup.find_all(['a', 'table']))   ##找出所有的a标签和table标签
```


(D)传True  
True可以匹配任何值

```
for tag in soup.find_all(True):      
    print(tag.name)
```


(E)传方法  
如果没有合适过滤器，那么还可以定义一个方法，方法只接收一个元素。如果这个方法返回true，表示当前元素匹配并且被找到，如果不是，则返回false

```
def has_class_but_no_id(tag):    ##定义一个方法，如果tag中包含class属性却不包含id属性，那么返回true      
    return tag.has_attr('class') and not tag.has_attr('id')      
print( soup.find_all(has_class_but_no_id(tag)) )
```


attrs的keyword参数  
注意：如果一个指定的参数不是搜素内置的函数名，搜索时会把该参数当做指定名字tag的属性来搜素，如果包含一个名字为id的参数，find\_all会搜素每个tag的id属性

```
print(soup.find_all( id='link' ))  ##打印出所有id=link的标签      
print(soup.find_all(href=re.compile("elsie"),id='link'))  ##打印出href中含有elsie，并且id=link的标签      
print(soup.find_all( class_='xie'))  ##class是python的关键字，所以加下划线  打印出class为xie的标签
```


text参数  
通过text参数可以搜素文档中的字符串内容，与name参数的可选值一样，text参数接受字符串、列表、正则表达式和True

```
print(soup.find_all(text="name"))    ##打印出网页中的name，返回一个列表，没有则返回空      
print(soup.find_all(text=re.compile("name")))  ##打印出网页中含有name的词，返回一个列表
```


limit参数  
find\_all()方法返回全部的搜素结构，如果文档树很大那么搜素会很慢，如果我们不需要全部结果，可以使用limit参数限制返回结果的数量

```
print(soup.find_all('a',limit=3))   ##找出3个a标签
```


recursive参数  
调用tag的find\_all()方法时，Beautiful Soup会检索当前tag的所有子孙节点，如果只想搜素tag的直接子节点，可以使用参数recursive=False

```
print(soup.find_all('head',recursive=False))
```


find(name,attrs,recursive,text,\*\*kwargs)  
它与find\_all()方法唯一的区别是find\_all()方法返回结果是只包含第一个元素的列表，而find()方法直接返回结果  
   
 

CSS选择器
------

我们在写CSS时，标签名不加任何修饰，类名前加点，id名前加#，在这里我们也可以利用类似的方法来筛选元素，用到的方法是soup.select() , 返回类型是list

  
通过标签名查找

```
print(soup.select('title'))   ##打印出标签是title的
```


通过类名查找

```
print(soup.select('.b'))      ##打印出类名是b的
```


通过id名查找

```
print(soup.select('#sister'))  ##打印出id名是sister的
```


组合查找

```
print(soup.select('p #link .xie'))  ##打印出p标签中，id=link，class=xie的
```


属性查找  
查找时还可以加入属性元素，属性需要用中括号括起来，注意属性和标签属于同一节点，所以中间不能加空格，否则匹配不到

```
print(soup.select("a[class='sister']"))  ##打印出a标签中class属性是sister的
```


以上的select方法返回的结果都是列表形式，可以遍历形式输出，然后用get\_text()方法来获取他的内容

```
for i in soup.select('a'):      
    print(i.get_text())   ##打印出a标签中的文本
```


  
  
 

   
 

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-149)[网络爬虫](https://edu.csdn.net/skill/python/python-3-149)[Beautiful Soup](https://edu.csdn.net/skill/python/python-3-149)84785 人正在系统学习中