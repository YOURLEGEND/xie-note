**目录**

[AJAX](#t0)

[XMLHttpRequest](#t1) 

[Ajax向服务器发送请求](#t2)

[Ajax接收服务器响应](#t3)

[AJAX - onreadystatechange 事件](#t4) 

[使用 Callback 函数](#t5)

[一次Ajax请求过程](#t6)

[使用JQuery的AJAX获取返回头](#t7)

* * *

> 以下是基于原生javascript的[ajax](https://so.csdn.net/so/search?q=ajax&spm=1001.2101.3001.7020)

AJAX
----

**AJAX(Asynchronous JavaScript and XML)** 异步的 JavaScript 和 [XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)，AJAX 不是新的编程语言，而是一种使用现有标准的新方法。AJAX 是与服务器交换数据并更新部分网页的技术，在不重新加载整个页面的情况下，更新网页的部分内容。

传统的网页（不使用 AJAX）如果需要更新内容，必需重载整个页面。

在AJAX技术中，主要利用 JavaScript 的 XMLHttpRequest 对象来传递用户界面上的数据到服务端并返回结果。XMLHttpRequest 对象用来传递通过HTTP传递的数据，一旦数据返回到客户端就可以立刻使用DOM技术来操作数据并显示到网页上。AJAX的最终目的是创建更好更快以及交互性更强的 Web 应用程序。

![](https://img-blog.csdnimg.cn/20181118134747932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181118134938667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **XMLHttpRequest** 

XMLHttpRequest 对象是 AJAX 的基础。所有现代浏览器均支持 XMLHttpRequest 对象（IE5 和 IE6 使用 ActiveXObject）。

XMLHttpRequest 用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

**创建XMLHttpRequest对象**

```
var xmlhttp = new XMLHttpRequest();   //for ie6 以上       
var xmlhttp = new ActiveXObject('Microsoft.XMLHTTP'); //for ie6
```


为了应对所有的现代浏览器，包括 IE5 和 IE6，请检查浏览器是否支持 XMLHttpRequest 对象。如果支持，则创建 XMLHttpRequest 对象。如果不支持，则创建 ActiveXObject ： 

```
var xmlhttp;      
if (window.XMLHttpRequest){        // code for IE7+, Firefox, Chrome, Opera, Safari      
  xmlhttp=new XMLHttpRequest();      
}else {                            // code for IE6, IE5      
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");      
}
```


### **Ajax向服务器发送请求**

如需将请求发送到服务器，我们使用 XMLHttpRequest 对象的 open() 和send() 方法

```
#GET请求      
xmlhttp.open( "GET" , url , true/false );      #true代表异步，false代表同步      
xmlhttp.send();        
#POST请求      
xmlhttp.open( "POST" , url , true/false );      
xmlhttp.send(uname='admin'&pawd='pass');          #当是post请求是，send函数内填post提交的数据
```


### Ajax接收服务器响应

如需获得来自服务器的响应，请使用 XMLHttpRequest 对象的 responseText 或 responseXML 属性。

| 属性 | 描述 |
| --- | --- |
| responseText | 获得字符串形式的响应数据。 |
| responseXML | 获得 XML 形式的响应数据。 |

例：

```
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;       
##################################################################       
xmlDoc=xmlhttp.responseXML;          #获得服务器端响应的xml格式的数据      
txt="";      
x=xmlDoc.getElementsByTagName("ARTIST");      
for (i=0;i<x.length;i++){      
  txt=txt + x[i].childNodes[0].nodeValue + "<br />";      1
}      1
document.getElementById("myDiv").innerHTML=txt;
```


### AJAX - onreadystatechange 事件 

当请求被发送到服务器，服务器对我们的请求响应时，我们需要执行一些基于响应的任务。也就是每当 readyState 改变时，就会触发 **onreadystatechange** 事件。

readyState 属性存有 XMLHttpRequest 的状态信息。

下面是 XMLHttpRequest 对象的三个重要的属性：

![](https://img-blog.csdnimg.cn/20190529202631291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在 onreadystatechange 事件中，我们规定当服务器响应已做好被处理的准备时所执行的任务。

当 readyState 等于 4 且状态为 200 时，表示响应已就绪：

```
xmlhttp.onreadystatechange=function(){      
  if (xmlhttp.readyState==4 && xmlhttp.status==200){      
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;      
    }      
}
```


注释：onreadystatechange 事件被触发 5 次（0 - 4），对应着 readyState 的每个变化

### 使用 Callback 函数

callback 回调函数是一种以参数形式传递函数给另一个函数的函数。

如果你的网站上存在多个 AJAX 任务，那么你应该为创建 XMLHttpRequest 对象编写一个_标准_的函数，然后每个ajax任务再编写不同的执行成功后的回调函数callback。

该函数调用应该包含 URL 以及发生 onreadystatechange 事件时执行的任务（每次调用可能不尽相同）：

```
<!DOCTYPE html>      
<html>      
<head>      
<script>      
var xmlhttp;      
function loadXMLDoc(url,cfunc){             //标准的ajax函数      
    if (window.XMLHttpRequest){            // IE7+, Firefox, Chrome, Opera, Safari 代码      
      xmlhttp=new XMLHttpRequest();      
    }else{                                // IE6, IE5 代码      1
      xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");      1
    }      1
    xmlhttp.open("GET",url,true);      1
    xmlhttp.send();      1
    xmlhttp.onreadystatechange=cfunc;    //状态改变时执行的函数      1
}      1
function myFunction()                   //callback回调函数      1
{      1
	loadXMLDoc("/try/ajax/ajax_info.txt",function()      1
	{      2
		if (xmlhttp.readyState==4 && xmlhttp.status==200)      2
		{      2
			document.getElementById("myDiv").innerHTML=xmlhttp.responseText;      2
		}      2
	});      2
}      2
</script>      2
</head>      2
<body>      2
    <div id="myDiv"><h2>使用 AJAX 修改文本内容</h2></div>      3
    <button type="button" onclick="myFunction()">修改内容</button>      3
</body>      3
</html>
```


### 一次Ajax请求过程

使用Ajax技术读取服务器端的数据，并显示在页面上

```
<!DOCTYPE html>      
<html lang="en">      
<head>      
    <meta charset="UTF-8">      
    <title>Ajax</title>      
    <script type="text/javascript">      
        function foo(){      
            var xmlhttp=new XMLHttpRequest();      
            xmlhttp.open("GET","1.txt",true);   #读取本路径下的1.txt文件      1
            xmlhttp.send();      1
            xmlhttp.onreadystatechange=function()      1
            {      1
                if (xmlhttp.readyState==4 && xmlhttp.status==200)      1
                {      1
                    document.getElementById("my").innerHTML=xmlhttp.responseText;      1
                }      1
            }      1
        }      1
    </script>      2
</head>      2
<body>      2
    <button id="btn" onclick="foo()">确定</button>      2
    <p id="my">hello,word!</p>      2
</body>      2
</html>
```


1.txt文件内容

```
AJAX
```


![](https://img-blog.csdnimg.cn/20181118161643218.png)

点击确定按钮

![](https://img-blog.csdnimg.cn/20181118161711791.png)

使用JQuery的AJAX获取返回头
------------------

```
$.ajax({      
    'url': 'xxx',      
     success: function(data, status, xhr) {       
        console.log("Success:"+xhr.status+": "+xhr.statusText);      
        console.log("############################");      
        console.log(xhr.getAllResponseHeaders());      
        console.log("############################");      
        console.log(xhr.getResponseHeader("Content-Type"));      
    }      1
});
```


![](https://img-blog.csdnimg.cn/20190529215404221.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

参考文章：[AJAX教程](http://www.runoob.com/ajax/ajax-tutorial.html)

                  [JQuery ajax方法](http://www.runoob.com/jquery/jquery-ref-ajax.html)