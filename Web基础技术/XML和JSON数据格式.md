**目录**

[XML](#t0)

[DTD(文档类型定义)](#t1)

[DTD声明](#t2)

[DTD实体](#t3)

[JSON](#t4)

[使用python解析JSON数据](#t5)

[XML和JSON的区别](#t6) 

[XML的优缺点](#t7)

[JSON的优缺点](#t8)

* * *

[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)和JSON都是web存储和传输过程中数据的格式，其中JSON 使用比较广泛，主要用来前后端交互数据。

XML
---

**XML**(Extensible Markup Language)扩展标记语言 ，是一种常用的标记语言，用于标记电子文件使其具有结构性，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 XML使用 **DTD(document type definition)**文档类型定义来组织数据；格式统一，跨平台和语言，早已成为业界公认的标准。XML是标准通用标记语言 (SGML) 的子集，非常适合 Web 传输。XML 提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。

```
#XML声明      
<?xml version="1.0" encoding="UTF-8"?>       
#文档类型定义DTD      
<!DOCTYPE note [      
    <!ELEMENT note (to,from,heading,body)>      
    <!ELEMENT to       (#PCDATA)>      
    <!ELEMENT from     (#PCDATA)>      
    <!ELEMENT heading  (#PCDATA)>      
    <!ELEMENT body     (#PCDATA)>      1
]>      1
#文档元素      1
<note>      1
  <to>Tove</to>      1
  <from>Jani</from>      1
  <heading>Reminder</heading>      1
  <body>Hello,word!</body>      1
</note>
```


**XML文档的构建模块** 

所有的 XML 文档（以及 HTML 文档）均由以下简单的构建模块构成：

*   元素
*   属性
*   实体
*   PCDATA
*   CDATA

下面是每个构建模块的简要描述。  
**1：元素**  
元素是 XML 以及 HTML 文档的主要构建模块，元素可包含文本、其他元素或者是空的。  
实例：

```
<body>body text in between</body>
```


空的 HTML 元素的例子是 <hr>、<br> 以及 <img>。

**2：属性**  
属性可提供有关元素的额外信息  
实例：

```
<img src="computer.gif" />
```


**3：实体**  
实体是用来定义普通文本的变量。实体引用是对实体的引用。

**4：PCDATA**  
PCDATA 的意思是被解析的字符数据（parsed character data）。  
PCDATA 是会被解析器解析的文本。这些文本将被解析器检查实体以及标记。

**5：CDATA**  
CDATA 的意思是字符数据（character data）。  
CDATA 是不会被解析器解析的文本。

### DTD(文档类型定义)

DTD（文档类型定义）的作用是定义 XML 文档的合法构建模块。

DTD 可以在 XML 文档内声明，也可以外部引用。

### DTD声明

![](https://img-blog.csdnimg.cn/20190119160727540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

1：**内部声明**：<!DOCTYPE   根元素   \[元素声明\]  > 

```
<?xml version="1.0"?>      
<!DOCTYPE note [      
  <!ELEMENT note (to,from,heading,body)>      
  <!ELEMENT to      (#PCDATA)>      
  <!ELEMENT from    (#PCDATA)>      
  <!ELEMENT heading (#PCDATA)>      
  <!ELEMENT body    (#PCDATA)>      
]>      
<note>      1
  <to>George</to>      1
  <from>John</from>      1
  <heading>Reminder</heading>      1
  <body>Don't forget the meeting!</body>      1
</note>
```


2：**外部声明**（引用外部DTD）：<!DOCTYPE 根元素 SYSTEM "文件名">  

```
<?xml version="1.0"?>      
<!DOCTYPE note SYSTEM "http://127.0.0.1/note.dtd">      
<note>      
<to>George</to>      
<from>John</from>      
<heading>Reminder</heading>      
<body>Don't forget the meeting!</body>      
</note>        1
#而http://127.0.0.1/note.dtd的内容为      1
<!ELEMENT note (to,from,heading,body)>      1
<!ELEMENT to (#PCDATA)>      1
<!ELEMENT from (#PCDATA)>      1
<!ELEMENT heading (#PCDATA)>      1
<!ELEMENT body (#PCDATA)>
```


### DTD实体

![](https://img-blog.csdnimg.cn/20200317192836515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

DTD实体是用于定义引用普通文本或特殊字符的快捷方式的变量，可分为内部实体和外部实体。

*   内部实体：<!ENTITY  实体名  实体的值 >
*   外部实体：<!ENTITY  实体名  SYSTEM   url  >

内外部实体又可分为 **一般实体** 和 **参数实体**

*   一般实体的声明语法：<!ENTITY  实体名   "实体内容“>                  引用实体的方式：&实体名；
*   参数实体只能在DTD中使用，参数实体的声明格式： <!ENTITY  %实体名   "实体内容“>        引用实体的方式：%实体名；

```
<?xml version="1.0"?>      
<!DOCTYPE message [      
    <!ENTITY normal    "hello">     <!-- 内部一般实体 -->      
    <!ENTITY % normal2 "hello">     <!-- 内部参数实体 -->      
    <!ENTITY normal3 SYSTEM "http://xml.org/hhh.dtd">    <!-- 外部一般实体 -->      
    <!ENTITY % normal4 SYSTEM "file:///1234.dtd">        <!-- 外部参数实体 -->      
    %normal4;            <!-- 引用参数实体 -->      
]>      
<message>&normal;</message>    <!-- 引用参数实体 -->
```


而且参数实体还能嵌套定义，但需要注意的是，内层的定义的参数实体% 需要进行HTML转义，否则会出现解析错误。

```
<?xml version="1.0"?>      
<!DOCTYPE test [      
    <!ENTITY % outside '<!ENTITY &#x25; files SYSTEM "file:///etc/passwd">'>      
]>      
<message>&normal;</message>
```


 **内部实体声明：**

**一般实体**

```
一般实体      
<?xml version="1.0"?>      
<!DOCTYPE test [      
    <!ENTITY writer "Bill Gates">      
    <!ENTITY copyright "Copyright W3School.com.cn">      
]>      
<test>&writer;&copyright;</test>
```


**参数实体** 

```
<?xml version="1.0"?>      
<!DOCTYPE test [      
    <!ENTITY %writer "Bill Gates">      
    <!ENTITY %copyright "Copyright W3School.com.cn">      
]>      
<test>%writer;%copyright;</test>
```


 **外部实体声明**

**一般实体**

```
<?xml version="1.0"?>      
<!DOCTYPE test [      
    <!ENTITY  writer  SYSTEM  "http://www.w3school.com.cn/dtd/entities.dtd">      
    <!ENTITY  copyright  SYSTEM  "http://www.w3school.com.cn/dtd/entities.dtd">      
]>      
<author>&writer;&copyright;</author>
```


**参数实体** 

```
<?xml version="1.0"?>      
<!DOCTYPE test [      
    <!ENTITY  %writer  SYSTEM  "http://www.w3school.com.cn/dtd/entities.dtd">      
    <!ENTITY  %copyright  SYSTEM  "http://www.w3school.com.cn/dtd/entities.dtd">      
]>      
<author>%writer;%copyright;</author>
```


JSON
----

**JSON**全称JavaScaript对象表示法(JavaScript Object Notation)，是存储和交换文本信息的语法。具有文本量更小、更快和更易解析的特点。Json和HTML不一样，HTML主要用于显示数据，JSON主要用于传递数据，所以一般作为数据的查询接口。

在 JS 语言中，一切都是对象。因此，任何支持的类型都可以通过 JSON 来表示，例如字符串、数字、对象、数组等。对象和数组是比较特殊且常用的两种类型：

*   对象表示为键值对
*   数据由逗号分隔
*   花括号保存对象
*   方括号保存数组

**JSON键值对表** 

![](https://img-blog.csdnimg.cn/20190529201657474.png)

```
{ "name":"xie",      
  "sex" :"man",      
  "id" : "66" }       
注意： ""和值之间不能有空格，不然取值的时候会出错
```


**JSON数组**

![](https://img-blog.csdnimg.cn/20190529201746474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
表示一：      
[ { "name":"xie",      
    "sex" :"man",      
    "id" : "66" },      
  { "name":"xiao",      
    "sex" :"woman",      
    "id" : "88" },      
  { "name":"hong",      
    "sex" :"woman",      1
    "id" : "77" }]       1
表示二：      1
{      1
  "name":["xie","xiao","hong"],      1
  "sex" :["man","woman","woman"],      1
  "id"  :["66","88","77"]      1
}       1
表示三：      2
{      2
   "student":[ { "name":"xie",      2
                 "sex" :"man",      2
                 "id" : "66" },      2
               { "name":"xiao",      2
                 "sex" :"woman",      2
                 "id" : "88" },      2
               { "name":"hong",      2
                 "sex" :"woman",      2
                 "id" : "77" }]      3
}
```


### 使用python解析JSON数据

对于表示法一和表示法二方法表示的JSON数据，可以采用下面方法读取JSON数据

1.json是上面的表示法一 ，2.json是上面的表示法二。可以看出，虽然数据是一样的，但是数据读取出来的排序是不一样的。

查询列的话直接 \['name'\]  ，查询行的话data.iloc\[0\] 表示第一行，或者 data.loc\['1'\] 表示打印出行名1的那行

![](https://img-blog.csdn.net/20181014172825509?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181014153731538?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](https://img-blog.csdn.net/20181014153747148?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

而对于表示法三表示的数据，则可以通过下面的方法读取。把数据读取在 jsonObject 对象中，然后访问数据的话，一层一层的访问。

jsonString是JSON的字符串格式数据，如果没有解码，调用decode() 函数即可

json.loads( jsonString )

![](https://img-blog.csdn.net/20181014155422562?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181014155553282?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

XML和JSON的区别 
------------

### XML的优缺点

**XML的优点：**

*   格式统一，符合标准；
*   容易与其他系统进行远程交互，数据共享比较方便。

**XML的缺点：**

*   XML文件庞大，文件格式复杂，传输占带宽；
*   服务器端和客户端都需要花费大量代码来解析XML，导致服务器端和客户端代码变得异常复杂且不易维护；
*   客户端不同浏览器之间解析XML的方式不一致，需要重复编写很多代码；
*   服务器端和客户端解析XML花费较多的资源和时间。

### JSON的优缺点

**JSON的优点：**

*   数据格式比较简单，易于读写，格式都是压缩的，占用带宽小；
*   易于解析，客户端JavaScript可以简单的通过eval()进行JSON数据的读取；
*   支持多种语言，包括ActionScript, C, C#, ColdFusion, Java, JavaScript, Perl, PHP, Python, Ruby等服务器端语言，便于服务器端的解析；
*   在PHP世界，已经有PHP-JSON和JSON-PHP出现了，偏于PHP序列化后的程序直接调用，PHP服务器端的对象、数组等能直接生成JSON格式，便于客户端的访问提取；
*   因为JSON格式能直接为服务器端代码使用，大大简化了服务器端和客户端的代码开发量，且完成任务不变，并且易于维护。

**JSON的缺点：**

*   没有XML格式这么推广的深入人心和喜用广泛，没有XML那么通用性；
*   JSON格式目前在Web Service中推广还属于初级阶段。

相关文章：[XXE(XML外部实体注入)](https://blog.csdn.net/qq_36119192/article/details/84993356)

                 [XXE漏洞利用](https://blog.csdn.net/qq_36119192/article/details/103866581)