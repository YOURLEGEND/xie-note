**目录**

[模板及其渲染](#t0)

[模板查找路径](#t1)

[DTL模板语法](#t2)

[常用的模板标签](#t3)

[DTL常用过滤器](#t4)

[模块结构优化](#t5)

[加载静态文件](#t6)

* * *

模板及其渲染
------

视图函数只是直接返回文本，而在实际生产环境中其实很少这样用，因为实际的页面大多是带有样式的HTML代码，这可以让浏览器渲染出非常漂亮的页面。目前市面上有非常多的模板系统，其中最知名最好用的就是**DTL**和**Jinja2。**DTL  是 [Django](https://so.csdn.net/so/search?q=Django&spm=1001.2101.3001.7020) Template Language  三个单词的缩写，也就是Django自带的模板语言。当然也可以配置Django支持Jinja2等其他模板引擎，但是作为Django内置的模板语言，和Django可以达到无缝衔接而不会产生一些不兼容的情况。

**DTL与普通的HTML文件的区别**：

DTL模板是一种带有特殊语法的HTML文件，这个HTML文件可以被Django编译，可以传递参数进去，实现数据动态化。在编译完成后，生成一个普通的HTML文件，然后发送给客户端。

  
**渲染模板：**  
渲染模板有多种方式。这里讲下两种常用的方式。

*   render\_to\_string  ：找到模板，然后将模板编译后渲染成Python的字符串格式。最后再通过 HttpResponse  类包装成一个 HttpResponse  对象返回回去。示例代码如下

```
from django.template.loader import render_to_string      
from django.http import HttpResponse       
def index(request):      
    html=render_to_string("index.html")          #渲染成html页面      
    return HttpResponse(html)
```


*   以上方式虽然已经很方便了。但是django还提供了一个更加简便的方式，直接将模板渲染成字符串和包装成 HttpResponse  对象一步到位完成。示例代码如下：

```
from django.shortcuts import render       
def index(request):      
    return render(request,"index.html")
```


### 模板查找路径

在项目的 settings.py  文件中。有一个 TEMPLATES  配置，这个配置包含了模板引擎的配置，模板查找路径的配置，模板上下文的配置等。模板路径可以在两个地方配置。

*   DIRS  ：这是一个列表，在这个列表中可以存放所有的模板路径，以后在视图中使用 render  或者 render\_to\_string  渲染模板的时候，会在这个列表的路径中查找模板。
*   APP\_DIRS  ：默认为 True  ，这个设置为 True  后，会在 INSTALLED\_APPS  的安装了的 APP  下的 templates  文件加中查找模板。

**查找顺序**：比如代码 render(request,"index.html")  。首先会在 DIRS  这个列表中依次查找路径下有没有这个模板，如果有，就返回。如果 DIRS  列表中所有的路径都没有找到，那么会先检查当前这个视图所处的 app  是否已经安装，如果已经安装了，那么就先在当前这个 app  下的 templates  文件夹中查找模板，如果没有找到，那么会在其他已经安装了的 app  中查找。如果所有路径下都没有找到，那么会抛出一个 TemplateDoesNotExist  的异常。

settings.py中的 INSTALLED\_APPS ，我们需要把自己的app加入其中

```
INSTALLED_APPS = [      
    'django.contrib.admin',      
    'django.contrib.auth',      
    'django.contrib.contenttypes',      
    'django.contrib.sessions',      
    'django.contrib.messages',      
    'django.contrib.staticfiles',      
]
```


settings.py 中的 TEMPLATES 配置项

```
TEMPLATES = [      
    {      
        'BACKEND': 'django.template.backends.django.DjangoTemplates',      
        'DIRS': [os.path.join(BASE_DIR,'templates')],      
        'APP_DIRS': True,      
        'OPTIONS': {      
            'context_processors': [      
                'django.template.context_processors.debug',      
                'django.template.context_processors.request',      1
                'django.contrib.auth.context_processors.auth',      1
                'django.contrib.messages.context_processors.messages',      1
            ],      1
        },      1
    },      1
]
```


注：如果我们是在命令行下新建的项目，则DIRS这一行是空的，运行的时候会显示如下报错。所以我们需要手动加上DIRS这一行的内容 

![](https://img-blog.csdnimg.cn/20190218140635726.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### DTL模板语法

**变量：**  
模板中可以包含变量， Django  在渲染模板的时候，可以传递变量对应的值过去进行替换。变量的命名规范和 Python  非常类似，只能是阿拉伯数字和英文字符以及下划线的组合，不能出现标点符号等特殊字符。变量需要通过视图函数渲染，视图函数在使用 render  或者 render\_to\_string  的时候可以传递一个 context  的参数，这个参数是一个字典类型。以后在模板中的变量就从这个字典中读取值的。示例代码如下：

```
# profile.html模板代码      
<p>{{ username }}</p>       
# views.py代码      
def index(request):      
    content={ 'username':'谢公子' }      
    # return render(request,"index.html",context=content)      
    html=render_to_string("index.html",context=content)      
    return HttpResponse(html)
```


模板中的变量同样也支持 点(.)  的形式。在出现了点的情况，比如 person.username  ，模板是按照以下方式进行解析的：

*   如果 person  是一个字典，那么就会查找这个字典的 username这个 key  对应的值。
*   如果 person  是一个对象，那么就会查找这个对象的 username  属性，或者是 username  这个方法。
*   如果出现的是 person.1  ，会判断 persons  是否是一个列表或者元组或者任意的可以通过下标访问的对象，如果是的话就取这个列表的第1个值。如果不是就获取到的是一个空的字符串。

**当是一个对象时**

```
# profile.html模板代码      
<p>{{ person.username }}</p>       
# views.py代码      
class Person(object):      
    def __init__(self,username):      
        self.username=username;      
def index(request):      
    p=Person("谢公子")      1
    content={'person':p}      1
    return render(request,"index.html",context=content)
```


**当是一个字典时** 

```
# profile.html模板代码      
<p>{{ person.username }}</p>    #不能 person['username']       
# views.py代码      
def index(request):      
    p={'username':'谢公子'}      
    content={'person':p}      
    return render(request,"index.html",context=content)
```


**当是列表或者元组时** 

```
# profile.html模板代码      
<p>{{ person.0 }}</p>    #不能 person['0']       
# views.py代码      
def index(request):      
    p=[1,2,3,4,5,6]      
    content={'person':p}      
    return render(request,"index.html",context=content)
```


注：不能通过中括号的形式访问字典和列表中的值，比如 dict\['key'\] 和 list\[1\] 是不支持的！因为使用点（.）语法获取对象值的时候，可以获取这个对象的属性，如果这个对象是一个字典，也可以获取这个字典的值。所以在给这个字典添加key的时候，千万不能和字典中的一些属性重复。比如items，items是字典的方法，那么如果给这个字典添加一个items作为key，那么以后就不能再通过item来访问这个字典的键值对了

### 常用的模板标签

1、 **if  else标签**： if  标签相当于 Python  中的 if  语句，有 elif  和 else  相对应，但是所有的标签都需要用标签符号 {% %}  进行包裹。 if  标签中可以使用 ==、!=、<、<=、>、>=、in、not  
in、is、is not  等判断运算符。示例代码如下：

```
{% if age < 18 %}      
        未成年      
{% elif age == 18 %}      
        成年人      
{% else %}      
        大人       
{% endif %}   #结束符
```


2、 **if...in... 标签**

```
{% if '谢' in names %}      
    one      
{% else %}      
    two      
{% endif %}
```


3、  **for...in...  标签**： for...in...  类似于 Python  中的 for...in...  。可以遍历列表、元组、字符串、字典等一切可以遍历的对象。示例代码如下：

```
#views.py文件中content      
content={'books':['西游记','水浒传','三国演义','红楼梦']}       
#index.html中      
{% for book in books %}      
    <li>{{ book }} </li>      
{% endfor %}
```


![](https://img-blog.csdnimg.cn/20190218173411501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想要反向遍历，那么在遍历的时候就加上一个 reversed  。示例代码如下：

```
 {% for book in books reversed %}      
     <li> {{ book }} </li>      
 {% endfor %}
```


![](https://img-blog.csdnimg.cn/20190218173443603.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

遍历字典的时候，需要使用 items  、 keys  和 values  等方法。在 DTL  中，执行一个方法不能使用圆括号的形式。遍历字典示例代码如下：

```
#views.py文件中的content      
content = {      
        'person':{      
            'username':'谢公子',      
            'age': 20,      
            'sex':'men'      
        }      
    }      
return render(request, "index.html", context=content)       1
#index.html文件      1
{% for key in person.keys %}      1
    <li>{{ key }}</li>      1
{% endfor %}      1
    <hr>       1
{% for value in person.values %}      1
    <li>{{ value }}</li>      1
{% endfor %}      2
    <hr>       2
{% for key,value in person.items %}      2
    <li>{{ key }}:{{ value }} </li>      2
{% endfor %}
```


![](https://img-blog.csdnimg.cn/20190218174117730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在 for  循环中， DTL  提供了一些变量可供使用。这些变量如下：

*   forloop.counter  ：当前循环的下标。以1作为起始值。
*   forloop.counter0  ：当前循环的下标。以0作为起始值。
*   forloop.revcounter  ：当前循环的反向下标值。比如列表有5个元素，那么第一次遍历这个属性是等于5，第二次是4，以此类推。并且是以1作为最后一个元素的下标。
*   forloop.revcounter0  ：类似于forloop.revcounter。不同的是最后一个元素的下标是从0开始。
*   forloop.first  ：是否是第一次遍历。
*   forloop.last  ：是否是最后一次遍历。
*   forloop.parentloop  ：如果有多个循环嵌套，那么这个属性代表的是上一级的for循环。

```
#views.py文件      
def index(request):      
    content = {      
        'books':[      
            {      
                'name':'三国演义',      
                'author':'罗贯中',      
                'price':199      
            },      1
            {      1
                'name':"水浒传",      1
                'author':'施耐庵',      1
                'price':109      1
            },      1
            {      1
                'name':"红楼梦",      1
                'author':'曹雪芹',      1
                'price':100      1
            },      2
            {      2
                'name':"西游记",      2
                'author':"吴承恩",      2
                'price':90      2
            }      2
        ]      2
    }      2
    return render(request, "index.html", context=content)
```

```
#index.html       
<table>      
        <thead>      
            <tr>      
                <td>序号1</td>      
                <td>序号2</td>      
                <td>序号3</td>      
                <td>书名</td>      
                <td>作者</td>      1
                <td>价格</td>      1
            </tr>      1
        </thead>      1
        <tbody>      1
            {% for book in books %}      1
                {% if forloop.first %}                       {#是否是第一次遍历#}      1
                    <tr style="background: red;">      1
                {% elif forloop.last %}                      {#是否是最后一次遍历#}      1
                    <tr style="background: pink">      1
                {% else %}      2
                    <tr style="background: yellow;">      2
                {% endif %}      2
                    <td>{{ forloop.counter0 }}</td>          {#当前循环的下标,从0开始#}      2
                    <td>{{ forloop.counter }}</td>           {#当前循环的下标，从1开始#}      2
                    <td>{{ forloop.revcounter0 }}</td>       {#反向当前循环的下标，从0开始#}      2
                    <td>{{ forloop.revcounter }}</td>        {#反向当前循环的下标，从1开始#}       2
                    <td>{{ book.name }}</td>      2
                    <td>{{ book.author }}</td>      2
                    <td>{{ book.price }}</td>      3
                </tr>      3
            {% endfor %}      3
        </tbody>      3
    </table>
```


 ![](https://img-blog.csdnimg.cn/20190218175929219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

4、  **for...in...empty**  标签：这个标签使用是一样的，只不过是在遍历的对象如果没有元素的情况下，会执行 empty  中的内容。示例代码如下：

```
#views.py文件中的content      
content={'comment':"" }       
#index.html文件      
<ul>      
    {% for comment in comments %}      
         <li>{{ comment }}</li>      
    {% empty %}      
         <li>没有任何评论</li>      1
    {% endfor %}      1
</ul>
```


![](https://img-blog.csdnimg.cn/20190218180426653.png)

5、 **with  标签**：在模版中定义变量。有时候一个变量访问的时候比较复杂，那么可以先把这个复杂的变量缓存到一个变量上，以后就可以直接使用这个变量就可以了。示例代码如下：

```
context = {      
    "persons": ["张三","李四"]      
}      
{% with li=persons.0  si=persons.1%}      
    {{ li }},{{ si }}      
{% endwith %}
```


![](https://img-blog.csdnimg.cn/20190218185619271.png)   
注：在 with  语句中定义的变量，只能在 {%with%}{%endwith%}  中使用，不能在这个标签外面使用。定义变量的时候，不能在等号左右两边留有空格。比如 {% with lisi = persons.1%}  是错误的。  
还有另外一种写法同样也是支持的：

```
{% with persons.0 as li %}      
    {{ li }},{{ si }}      
{% endwith %}
```


6、  **url  标签**：在模版中，我们经常要写一些 url  ，比如某个 a  标签中需要定义 href  属性。当然如果通过硬编码的方式直接将这个 url  写死在里面也是可以的。但是这样对于以后项目维护可能不是一件好事。因此建议使用这种反转的方式来实现，类似于 django  中的 reverse  一样。示例代码如下：

```
<a href="{% url 'book:list' %}">图书列表页面</a>
```


如果 url  反转的时候需要传递参数，那么可以在后面传递。但是参数分位置参数和关键字参数。位置参数和关键字参数不能同时使用。示例代码如下：

```
# path部分      
path('detail/<book_id>/',views.book_detail,name='detail')       
# url反转，使用位置参数      
<a href="{% url  'book:detail'  1  %}">图书id</a>       
# url反转，使用关键字参数      
<a href="{% url  'book:detail'  book_id=1  %}">图书id</a>
```


如果想要在使用 url  标签反转的时候要传递查询字符串的参数，那么必须要手动在在后面添加。示例代码如下：

```
<a href="{% url 'book:detail' book_id=1 %}?page=2">图书详情页面</a>       
#views.py中      
def book_detail(request,book_id):      
    page=request.GET.get("page")      
    text="书籍id是：%s,页面是：%s " % (book_id,page)      
    return HttpResponse(text)
```


如果需要传递多个参数，那么通过空格的方式进行分隔。示例代码如下：

```
<a href="{% url 'book:detail' book_id=1  page=2 %}">图书详情页面</a>       
#views.py中      
def book_detail(request,book_id,page):      
    text="书籍id是：%s,页面是：%s " % (book_id,page)      
    return HttpResponse(text)
```


7、 **spaceless  标签**：移除html标签中的空白字符。包括空格、tab键、换行等。示例代码如下：

```
{% spaceless %}      
<p>      
<a href="foo/">Foo</a>      
</p>      
{% endspaceless %}
```


那么在渲染完成后，会变成以下的代码：

```
<p><a href="foo/">Foo</a></p>
```


spaceless  只会移除html标签之间的空白字符。而不会移除标签与文本之间的空白字符。看以下代码：

```
{% spaceless %}      
<strong>      
Hello      
</strong>      
{% endspaceless %}
```


这个将不会移除 strong  中的空白字符。  
8、 **autoescape  标签**：开启和关闭这个标签内元素的自动转义功能。自动转义是可以将一些特殊的字符。比如 <  转义成 html  语法能识别的字符，比如 <  会被转义成 <  ，而 >  会被自动转义成 >  。模板中默认是已经开启了自动转义的。 autoescape 的示例代码如下：

```
# 传递的上下文信息      
context = {      
    "info":"<a href='www.baidu.com'>百度</a>"      
}      
# 模板中开启自动转义      
{% autoescape on %}      
{{ info }}      
{% endautoescape %}
```


那么就会显示成一个普通的字符串

![](https://img-blog.csdnimg.cn/20190219084732197.png)

如果把 on  成 off  ，那么就会显示百度的一个超链接。  
示例代码如下：

```
{% autoescape off %}      
{{ info }}      
{% endautoescape %}
```


![](https://img-blog.csdnimg.cn/20190219084753164.png)

9、**verbatim  标签**：默认在 DTL  模板中是会去解析那些特殊字符的。比如 {%  和 %}  以及 {{  等。如果你在某个代码片段中不想使用 DTL  的解析引擎。那么你可以把这个代码片段放在 verbatim  标签中。示例代码下：

```
{% verbatim %}      
{{if dying}}Still alive.{{/if}}      
{% endverbatim %}
```


![](https://img-blog.csdnimg.cn/2019021908564762.png)

10、更多标签请参考官方文档： https://docs.djangoproject.com/en/2.0/ref/templates/builtins/

### DTL常用过滤器

在模版中，有时候需要对一些数据进行处理以后才能使用。一般在 Python  中我们是通过函数的形式来完成的。而在模版中，则是通过过滤器来实现的。过滤器使用的是 |  来使用。比如使用 add  过滤器，那么示例代码如下：

```
{{ value|add:"2" }}
```


在开发中常用的过滤器：

**add**  
将传进来的参数添加到原来的值上面。这个过滤器会尝试将 值  和 参数  转换成整形然后进行相加。如果转换成整形过程中失败了，那么会将 值  和 参数  进行拼接。如果是字符串，那么会拼接成字符串，如果是列表，那么会拼接成一个列表。示例代码如下：

```
{{ value|add:"2" }}
```


如果 value  是等于4，那么结果将是6。如果 value  是等于一个普通的字符串，比如 abc  ，那么结果将是 abc2  。 add  过滤器的源代码如下：

```
def add(value, arg):      
"""Add the arg to the value."""      
try:      
    return int(value) + int(arg)      
except (ValueError, TypeError):      
    try:      
        return value + arg      
except Exception:      
    return ''
```


**cut**  
移除值中所有指定的字符串。类似于 python  中的 replace(args,"")  。示例代码如下：

```
{{ value|cut:"xie" }}
```


以上示例将会移除 value  中所有的空格字符。 cut  过滤器的源代码如下：

```
def cut(value, arg):      
"""Remove all values of arg from the given string."""      
    safe = isinstance(value, SafeData)      
    value = value.replace(arg, '')      
    if safe and arg != ';':      
        return mark_safe(value)      
    return value
```


**date**  
将一个日期按照指定的格式，格式化成字符串。示例代码如下：  
\# 数据

```
context = {      
    "birthday": datetime.now()      
}
```


\# 模版

```
{{ birthday|date:"Y/m/d" }}
```


那么将会输出 2018/02/01  。其中 Y  代表的是四位数字的年份， m  代表的是两位数字的月份， d  代表的是两位数字的日。  
还有更多时间格式化的方式。见下表。  
格式字符 描述 示例

*   Y 四位数字的年份 2018
*   m 两位数字的月份 01-12
*   n 月份，1-9前面没有0前缀 1-12
*   d 两位数字的天 01-31
*   j 天，但是1-9前面没有0前缀 1-31
*   g 小时，12小时格式的，1-9前面没有0前缀 1-12
*   h 小时，12小时格式的，1-9前面有0前缀 01-12
*   G 小时，24小时格式的，1-9前面没有0前缀 1-23
*   H 小时，24小时格式的，1-9前面有0前缀 01-23
*   i 分钟，1-9前面有0前缀 00-59
*   s 秒，1-9前面有0前缀 00-59

**default**  
如果值被评估为 False  。比如 \[ \]  ， ""  ， None  ， {}  等这些在 if  判断中为 False  的值，都会  
使用 default  过滤器提供的默认值。示例代码如下：

```
{{ value|default:"nothing" }}
```


如果 value  是等于一个空的字符串。比如 ""  ，那么以上代码将会输出 nothing  。  
**default\_if\_none**  
如果值是 None  ，那么将会使用 default\_if\_none  提供的默认值。这个和 default  有区别， default  是所有被评估为 False  的都会使用默认值。而 default\_if\_none  则只有这个值是等于 None  的时候才会使用默认值。示例代码如下：

```
{{ value|default_if_none:"nothing" }}
```


如果 value  是等于 ""  也即空字符串，那么以上会输出空字符串。如果 value  是一个 None  值，以上代码才会输出 nothing  。  
**first**  
返回列表/元组/字符串中的第一个元素。示例代码如下：

```
{{ value|first }}
```


如果 value  是等于 \['a','b','c'\]  ，那么输出将会是 a  。  
**last**  
返回列表/元组/字符串中的最后一个元素。示例代码如下：

```
{{ value|last }}
```


如果 value  是等于 \['a','b','c'\]  ，那么输出将会是 c  。  
**floatformat**  
使用四舍五入的方式格式化一个浮点类型。如果这个过滤器没有传递任何参数。那么只会在小数点  
后保留一个小数，如果小数后面全是0，那么只会保留整数。当然也可以传递一个参数，标识具体  
要保留几个小数。  
1\. 如果没有传递参数：

```
| value | 模版代码 | 输出 | | --- | --- | --- | | 34.23234 |  {{ value\|floatformat }}  | 34.2 | |34.000 |  {{ value\|floatformat }}  | 34 | | 34.260 |  {{ value\|floatformat }}  | 34.3 |
```


2\. 如果传递参数：

```
| value | 模版代码 | 输出 | | --- | --- | --- | | 34.23234 |  {{value\|floatformat:3}}  |34.232 | |34.0000 |  {{value\|floatformat:3}}  | 34.000 | | 34.26000 |  {{value\|floatformat:3}}  |34.260 |
```


**join**  
类似与 Python  中的 join  ，将列表/元组/字符串用指定的字符进行拼接。示例代码如下：

```
{{ value|join:"/" }}
```


如果 value  是等于 \['a','b','c'\]  ，那么以上代码将输出 a/b/c  。  
**length**  
获取一个列表/元组/字符串/字典的长度。示例代码如下：

```
{{ value|length }}
```


如果 value  是等于 \['a','b','c'\]  ，那么以上代码将输出 3  。如果 value  为 None  ，那么以上将返回 0  。  
**lower**  
将值中所有的字符全部转换成小写。示例代码如下：

```
{{ value|lower }}
```


如果 value  是等于 Hello World  。那么以上代码将输出 hello world  。  
**upper**  
类似于 lower  ，只不过是将指定的字符串全部转换成大写。  
**random**  
在被给的列表/字符串/元组中随机的选择一个值。示例代码如下：

```
{{ value|random }}
```


如果 value  是等于 \['a','b','c'\]  ，那么以上代码会在列表中随机选择一个。  
**safe**  
标记一个字符串是安全的。也即会关掉这个字符串的自动转义。示例代码如下：

```
{{value|safe}}
```


如果 value  是一个不包含任何特殊字符的字符串，比如 <a>  这种，那么以上代码就会把字符串正常的输入。如果 value  是一串 html  代码，那么以上代码将会把这个 html  代码渲染到浏览器中。  
**slice**  
类似于 Python  中的切片操作。示例代码如下：

```
{{ some_list|slice:"2:" }}
```


以上代码将会给 some\_list  从 2  开始做切片操作。  
**stringtags**  
删除字符串中所有的 html  标签。示例代码如下：

```
{{ value|striptags }}
```


如果 value  是 <strong>hello world</strong>  ，那么以上代码将会输出 hello world  。  
**truncatechars**  
如果给定的字符串长度超过了过滤器指定的长度。那么就会进行切割，并且会拼接三个点来作为省略号。示例代码如下：

```
{{ value|truncatechars:5 }}
```


如果 value  是等于 北京欢迎您~  ，那么输出的结果是 北京...  。可能你会想，为什么不会 北京欢迎您...  呢。因为三个点也占了三个字符，所以 北京  +三个点的字符长度就是5。  
**truncatechars\_html**  
类似于 truncatechars  ，只不过是不会切割 html  标签。示例代码如下：

```
{{ value|truncatechars:5 }}
```


如果 value  是等于 <p>北京欢迎您~</p>  ，那么输出将是 <p>北京...</p>  。

### 模块结构优化

**引入模版**  
有时候一些代码是在许多模版中都用到的。如果我们每次都重复的去拷贝代码那肯定不符合项目的规范。一般我们可以把这些重复性的代码抽取出来，就类似于Python中的函数一样，以后想要使用这些代码的时候，就通过 include  包含进来。这个标签就是 include  。示例代码如下：

```
# header.html      
<p>我是header</p>       
# footer.html      
<p>我是footer</p>       
# main.html      
{% include 'header.html' %}      
<p>我是main内容</p>      1
{% include 'footer.html' %}
```


**include  标签寻找路径的方式**。也是跟 render  渲染模板的函数是一样的。  
默认 include  标签包含模版，会自动的使用主模版中的上下文，也即可以自动的使用主模版中的变量。如果想传入一些其他的参数，那么可以使用 with  语句。示例代码如下：

```
# header.html      
<p>用户名：{{ username }}</p>      
# main.html      
{% include "header.html" with username='huangyong' %}
```


**模板继承：**  
在前端页面开发中。有些代码是需要重复使用的。这种情况可以使用 include  标签来实现。也可以使用另外一个比较强大的方式来实现，那就是模版继承。模版继承类似于 Python  中的类，在父类中可以先定义好一些变量和方法，然后在子类中实现。模版继承也可以在父模版中先定义好一些子模版需要用到的代码，然后子模版直接继承就可以了。并且因为子模版肯定有自己的不同代码，因此可以在父模版中定义一个block接口，然后子模版再去实现。以下是父模版的代码：

这个模版，我们取名叫做 base.html  ，定义好一个简单的 html  骨架，然后定义好两个 block  接口，让子模版来根据具体需求来实现。

```
{% load static %}      
<!DOCTYPE html>      
<html lang="en">      
<head>      
<link rel="stylesheet" href="{% static 'style.css' %}" />      
<title>{% block title %}我的站点{% endblock %}</title>      
</head>      
<body>      
<div id="sidebar">      1
{% block sidebar %}      1
<ul>      1
<li><a href="/">首页</a></li>      1
<li><a href="/blog/">博客</a></li>      1
</ul>      1
{% endblock %}      1
</div>      1
<div id="content">      1
{% block content %}{% endblock %}      1
</div>      2
</body>      2
</html>
```


子模板然后通过 extends  标签来实现，示例代码如下：

```
{% extends "base.html" %}      
{% block title %}博客列表{% endblock %}      
{% block content %}      
hello,word!      
{% endblock %}
```


需要注意的是：extends标签必须放在模版的第一行。子模板中的代码必须放在block中，否则将不会被渲染。如果在某个 block  中需要使用父模版的内容，那么可以使用 {{block.super}}  来继承。比如上例， {%block title%}  ，如果想要使用父模版的 title  ，那么可以在子模版的 title block  中使用 {{ block.super }}  来实现。在定义 block  的时候，除了在 block  开始的地方定义这个 block  的名字，还可以在 block  结束的时候定义名字。比如 {% block title %}{% endblock title %}  。这在大型模版中显得尤其有用，能让你快速的看到 block  包含在哪里。

![](https://img-blog.csdnimg.cn/20190219111938986.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **加载静态文件**

在一个网页中，不仅仅只有一个 html  骨架，还需要 css  样式文件， js  执行文件以及一些图片等。因此在 DTL  中加载静态文件是一个必须要解决的问题。在 DTL  中，使用 static  标签来加载静态文件。要使用 static  标签，首先需要 {% load static %}  。加载静态文件的步骤如下：  
1、首先确保 django.contrib.staticfiles  已经添加到 settings.INSTALLED\_APPS  中，默认是添加的了。  
2、确保在 settings.py  中设置了 STATIC\_URL，默认是添加的了  。  
3、在**已经安装**了的 app  下创建一个文件夹叫做 static  ，然后再在这个 static  文件夹下创建一个当前 app  的名字的文件夹，再把静态文件放到这个文件夹下。例如你的 app  叫做 polls  ，有一个静态文件叫做 style.css  ，那么路径为polls/static/polls/style.css  。（为什么在 app  下创建一个 static  文件夹，还需要在这个 static  下创建一个同 app  名字的文件夹呢？原因是如果直接把静态文件放在 static  文件夹下，那么在模版加载静态文件的时候就是使用style.css  ，如果在多个 app  之间有同名的静态文件，这时候可能就会产生混淆。而在 static  文件夹下加了一个同名 app  文件夹，在模版中加载的时候就是使用 app/style.css ，这样就可以避免产生混淆。）

![](https://img-blog.csdnimg.cn/20190219142939464.png)

注：这里已安装app是指在settings.py文件中的INSTALLED\_APPS中需要加上我们的polls。  
4、如果有一些静态文件是不和任何 app  挂钩的。那么可以在 settings.py  中添加 STATICFILES\_DIRS  ，以后 DTL  就会在这个列表的路径中查找静态文件。比如可以设置为:

```
STATICFILES_DIRS = [      
    os.path.join(BASE_DIR,"static")      
]
```


5、在模版中使用 load  标签加载 static  标签。比如要加载在项目的 static  文件夹下的 style.css  的文件。那么示例代码如下：

```
{% load static %}      
<link rel="stylesheet" href="{% static 'polls/style.css' %}">      
<img src="{% static 'polls/1.jpg' %}" alt="">
```


6、如果不想每次在模版中加载静态文件都使用 load  加载 static  标签，那么可以  
在 settings.py  中的 TEMPLATES/OPTIONS  添加 

```
'builtins':['django.templatetags.static']
```


这样以后在模版中就可以直接使用 static  标签，而不用手动的 load  了。  
7\. 如果没有在 settings.INSTALLED\_APPS  中添加 django.contrib.staticfiles  。那么我们就需要手动的将请求静态文件的 url  与静态文件的路径进行映射了。示例代码如下：

```
from django.conf import settings      
from django.conf.urls.static import static      
urlpatterns = [   # 其他的url映射  ] + static(settings.STATIC_URL,document_root=settings.STATIC_ROOT)
```
