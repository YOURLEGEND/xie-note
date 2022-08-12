**目录**

[HTML](#t0)

[XML](#t1)

[XHTML](#t2)

* * *

HTML
----

**HTML(HyperText Markup Language)**：超文本标记语言,是一种用于创建网页的标准标记语言，是用来格式化并显示数据的

用HTML语法写成的文件称为网页文件，后缀为 .html  或  .htm  
一个最简单的网页：

```
<html>       
    <head>       
       <title> 第一个网页 </title>       
   </head>       
   <body>       
       <p> hello,word! </p>       
   </body>       
</html>
```


XML
---

**XML （eXtensible Markup Language）**指可扩展标记语言，[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020) 被设计用来传输和存储数据，而不是显示数据。XML 标签没有被预定义。我们需要自行定义标签，XML 被设计为具有自我描述性，XML 是 W3C 的推荐标准。

XML 是对 HTML 的补充，XML 不会替代 HTML，理解这一点很重要。在大多数 Web 应用程序中，XML 用于存储、传输数据，而 HTML 用于格式化并显示数据。

示例：

```
<?xml version="1.0" encoding="UTF-8"?>      
<note>      
  <to>Tove</to>      
  <from>Jani</from>      
  <heading>Reminder</heading>      
  <body>Hello,word!</body>      
</note>
```


XHTML
-----

**XHTML（eXtensible HyperText Markup Language)**可扩展超文本标记语言，是一种标记语言，表现方式与超文本标记语言（HTML）类似，不过语法上更加严格。从继承关系上讲，HTML是一种基于标准通用标记语言（SGML）的应用，是一种非常灵活的置标语言，而**XHTML**则基于可扩展标记语言（XML），XML是SGML的一个子集。

相关文章：[XHTML教程](http://www.w3school.com.cn/xhtml/index.asp)