**目录**

[JQuery](#t0)

[JQuery选择器](#t1)

[JQuery事件](#t2)

[JQuery对DOM元素的操作](#t3) 

* * *

JQuery
------

jQuery是一个快速、简洁的JavaScript框架，它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作、事件处理、动画设计和Ajax交互。jQuery的核心特性可以总结为：具有独特的链式语法和短小清晰的多功能接口；具有高效灵活的css选择器，并且可对CSS选择器进行扩展；拥有便捷的插件扩展机制和丰富的插件。

目前jQuery有1.x和2.x两个主要版本，区别在于2.x移除了对古老的IE 6、7、8的支持，因此2.x的代码更精简，jQuery只是一个`jquery-xxx.js`文件。

**JQuery的使用**

我们只需要在我们网站的head中引入jquery文件即可。

```
<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>
```


**$符号** 

`$`是著名的jQuery符号。实际上，jQuery把所有功能全部封装在一个全局变量`jQuery`中，而`$`也是一个合法的变量名，它是变量`jQuery`的别名，即  $ ==  jQuery

```
document.getElementById('test');  //普通JavaScript写法      
$('#test');            //JQuery写法      
jQuery('#test');       //JQuery的另外一种写法
```


**文档就绪事件(入口函数)**

这是为了防止文档在完全加载之前运行 jQuery 代码，即在 DOM 加载完成后才可以对 DOM 进行操作

**JQuery的写法**

```
$(document).ready(function(){      
   // 开始写 jQuery 代码...      
});       
//或者       
$(function(){      
   // 开始写 jQuery 代码...      
});
```


**普通JavaScript的写法**

```
window.onload = function () {      
    // 执行JS代码      
}
```


jQuery 入口函数与 JavaScript 入口函数的区别：

*    jQuery 的入口函数是在 html 所有标签(DOM)都加载之后，就会去执行。
*    JavaScript 的 window.onload 事件是等到所有内容，包括外部图片之类的文件加载完后，才会执行。

### JQuery选择器

jQuery 选择器允许对 HTML 元素组或单个元素进行操作，jQuery 选择器基于元素的 id、class、type、attr、属性值等选择HTML 元素。 它基于已经存在的CSS选择器，除此之外，它还有一些自定义的选择器。

jQuery 中所有选择器都以美元符号开头：**$( )**

```
$("*")       //选取所有元素      
$("p")       //选择页面所有的p标签元素      
$("#test")   //选择id为test的标签元素      
$(".test")   //选择class为test的标签元素      
$(this)      //选取当前HTML元素      
$("p.test")  //选取class为test的p元素      
$("p:first") //选取第一个p元素      
$("ul li:first")  //选取第一个ul元素的第一个li元素
```


 更多的选择，传送门：[http://www.runoob.com/jquery/jquery-selectors.html](http://www.runoob.com/jquery/jquery-selectors.html)

### JQuery事件

常见的事件

![](https://img-blog.csdnimg.cn/20190301154440407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
//hover()方法用于模拟光标悬停事件。当鼠标移动到元素上时，会触发指定的第一个函数(mouseenter);当鼠标移出这个元素时，会触发指定的第二个函数(mouseleave)。      
$("#p1").hover(      
    function(){      
        alert("你进入了 p1!");      
    },      
    function(){      
        alert("拜拜! 现在你离开了 p1!");      
    }      
);       1
//点击事件      1
$("p").click(function(){      1
  $(this).hide();      1
});
```


### JQuery对DOM元素的操作 

**获取内容和属性值**

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta charset="utf-8">      
    <script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js">      
    </script>      
    <script>      
        $(document).ready(function(){      
//            获取标签的text值      1
            $("#btn1").click(function(){      1
                alert($("#test").text());      1
            });      1
//            获取标签的html值      1
            $("#btn2").click(function(){      1
                alert($("#test").html());      1
            });      1
//            获取标签的属性值      1
            $("#btn3").click(function(){      1
                alert($("#runoob").attr("href"));      2
            });      2
//            获取标签的值      2
            $("#btn4").click(function(){      2
                alert($("#test2").val());      2
            });      2
        });      2
    </script>      2
</head>      2
<body>      2
<p id="test">这是段落中的 <b>粗体</b> 文本。</p>      3
<p><a href="http://www.runoob.com" id="runoob">菜鸟教程</a></p>      3
<p>名称: <input type="text" id="test2" value="菜鸟教程"></p>      3
<button id="btn1">显示文本</button>      3
<button id="btn2">显示 HTML</button>      3
<button id="btn3">显示 href 属性的值</button>      3
<button id="btn4">显示value的值</button>      3
</body>      3
</html>
```


**设置内容和属性值**

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta charset="utf-8">      
    <script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>      
    <script>      
        $(document).ready(function(){      
//            设置标签的text值 <b>当成html实体了      
            $("#btn1").click(function(){      1
                $("#test").text("<b>hello,word!</b>");      1
            });      1
//            设置标签的html值      1
            $("#btn2").click(function(){      1
                $("#test").html("<b>hello,word!</b>");      1
            });      1
            $("#btn3").click(function(){      1
                $("#img").attr("src","https://pic4.zhimg.com/v2-95c83e8f42dd744676a75ed00b6b65ae_1200x500.jpg");      1
            });      1
//            设置input标签的值      2
            $("#btn4").click(function(){      2
                $("#test2").val("谢公子");   //这种方式只能设置value值      2
//              $("#test2").attr("value","张小姐");   这种方法可以设置所有属性的值，因为value也是属性，所以也可以设置value      2
            });      2
//            设置复选框的选中状态      2
            $("#btn5").click(function(){      2
                $("#checkbox2").attr("checked",true);      2
            });      2
        });      2
    </script>      3
</head>      3
<body>      3
<p id="test">这是段落中的文本。</p>      3
<a href="http://www.runoob.com" id="runoob">菜鸟教程</a> <br/>      3
姓名: <input type="text" id="test2" name="" value=""> <br/>      3
爱好： <input type="checkbox" name="hobby" value="sport" id="checkbox1"/>运动      3
<input type="checkbox" name="hobby" value="music" id="checkbox2"/>听音乐      3
<input type="checkbox" name="hobby" value="reader" id="checkbox3"/>阅读 <br/>      3
<button id="btn1">设置文本</button>      3
<button id="btn2">设置 HTML</button>      4
<button id="btn3">设置图片src属性的值</button>      4
<button id="btn4">设置 value的值</button>      4
<button id="btn5">设置爱好为听音乐</button>   <br/>      4
<img src="" id="img" alt=""/>      4
</body>      4
</html>
```


![](https://img-blog.csdnimg.cn/20190412164102748.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

参考文章：[http://www.runoob.com/jquery/jquery-tutorial.html](http://www.runoob.com/jquery/jquery-tutorial.html)