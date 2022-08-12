**目录**

[CSS](#t0)

[CSS选择器](#t1)

[CSS应用方式](#t2) 

[外部样式表](#t3)

[内部样式表一：](#t4)

[内部样式表二：](#t5)

[盒模型](#t6)

[块级元素](#t7)

[内联元素](#t8)

[内联块元素](#t9)

[定位](#t10)

[static](#t11)

[fixed](#t12)

[relative](#t13)

[absolute](#t14)

[sticky](#t15)

* * *

CSS
===

**CSS**(Cascading Style Sheets)层叠样式表是一种用来表现HTML文件样式的计算机语言。CSS不仅可以静态地修饰网页，还可以配合各种脚本语言动态地对网页各元素进行格式化。CSS 能够对网页中元素位置的排版进行像素级精确控制，支持几乎所有的字体字号样式，拥有对网页对象和模型样式编辑的能力。

CSS选择器
------

CSS对元素的定位有很多种方法：

*      主要的有三种：id选择器，类选择器，标签选择器
*      关系选择器：后台选择器，选择子元素，选择直接子元素，选择兄弟元素
*      组合选择器：分组定义样式(并列选择器)
*      属性选择器：按属性过滤选择器，主要用于表单元素和a标签的选择

**id选择器**

```
    <style>      
        #one{      
            color:red      
        }      
    </style>      
    <p id="one">hello,word!</p>
```


**class类选择器**

```
    <style>      
        .one{      
            color:red      
        }      
    </style>      
    <p class="one">hello,word!</p>
```


**标签选择器**

```
    <style>      
        p{      
            color:red      
        }      
    </style>      
    <p>hello,word!</p>
```


**后代选择器**

后代选择器用于选取某元素的后代元素，也就是某元素在另一个元素里面就可以选择

```
<style>      
 div p{                //选取所有div内的p元素      
        color:green;      
     }      
</style>      
<div>      
       <p id="one">hello,word!</p>      
</div>
```


**子元素选择器**

与后代选择器相比，子元素选择器（Child selectors）只能选择作为某元素子元素的元素

```
<style>      
 div>p{                //选取所有div的子元素p      
        color:green;      
     }      
</style>      
<div>      
       <p id="one">hello,word!</p>      
</div>
```


**相邻兄弟选择器**

相邻兄弟选择器（Adjacent sibling selector）可选择紧接在另一元素后的元素，且二者有相同父元素。

```
<style>      
  div+p{               //将选择China所在的p元素      
        color:green;        
       }      
</style>       
<div>      
    <p id="one">hello,word!</p>      
</div>      1
<p>China</p>
```


**后续兄弟选择器**

后续兄弟选择器选取指定元素之后的所有相邻兄弟元素

```
<style>      
  div~p{               //将选择China、happy、html所在的p元素      
        color:green;        
       }      
</style>       
<div>      
    <p id="one">hello,word!</p>      
</div>      1
<p>China</p>      1
<p>happy</p>      1
<p>html</p>
```


**并列选择器**

```
table,tr,th,td{      
     border: 1px solid grey;      
     border-collapse: collapse;      
     border-left: 3px dotted red;      
   }      
tr{      
     height: 35px;      
     text-align: center;      
   }      1
th{      1
     background-color: bisque;      1
   }      1
th,td{      1
    width:100px;      1
    padding: 10px;      1
  }      1
<table>      1
    <tr>      1
        <th>cell</th>      2
        <th>cell</th>      2
        <th>cell</th>      2
    </tr>      2
    <tr>      2
        <td>cell</td>      2
        <td>cell</td>      2
        <td>cell</td>      2
    </tr>      2
    <tr>      2
        <td>cell</td>      3
        <td>cell</td>      3
        <td>cell</td>      3
    </tr>      3
</table>
```


**属性选择器**

选择具有特定属性的元素

```
<style>      
        [title]{         //选择有title属性的元素      
            color:red      
        }      
</style>      
<h title="hello">Hello,word!</h>      
<p title="p">文本</p>
```


**属性和值选择器**

选择具有特定属性的且值相等的元素

```
<style>      
        [title=p]{         //选择有title=p属性的元素      
            color:red      
        }      
</style>      
<h title="hello">Hello,word!</h>      
<p title="p">文本</p>
```


**属性和值的选择器 - 多值**

选择具有特定属性且值中含有指定字符的元素

```
<style>      
        [title~=p]{         //选择title属性的值中含有hello的元素      
            color:red      
        }      
</style>      
 <h1 title="hello world">Hello world</h1>      
 <p title="student hello">Hello CSS students!</p>      
 <p title="student">Hi CSS students!</p>
```


 **元素属性和值选择器**

选择某个元素属性等于某个值

```
<style>      
        p[title=one]{      
            color:red      
        }      
</style>      
<p title="one">Hello CSS students!</p>      
<p title="two">Hi CSS students!</p>
```


CSS应用方式 
--------

### 外部样式表

```
<link rel="stylesheet" type="text/css" href="mystyle.css">
```


### 内部样式表一：

```
<html>      
<head lang="en">      
    <style type="text/css">      
        .title{      
            color: red;      
        }      
        #p{      
            color:blue;      
        }      1
    </style>      1
</head>      1
<body>      1
    <h3 class="title">这是标题</h>      1
    <p id="p">这是文本</p>      1
</body>      1
</html>
```


### 内部样式表二：

```
<html>      
<head lang="en">      
</head>      
<body>      
    <h3 style="color: red;">这是标题</h>      
    <p style="color: blue">这是文本</p>      
</body>      
</html>
```


盒模型
---

所有 HTML 元素可以看作盒子，在CSS中，"box model"这一术语是用来设计和布局时使用。CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。盒模型允许我们在其它元素和周围元素边框之间的空间放置元素。

![](https://img-blog.csdnimg.cn/20181106203906228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   **Margin(外边距)** - 清除边框外的区域，外边距是透明的。
*   **Border(边框)** - 围绕在内边距和内容外的边框。
*   **Padding(内边距)** - 清除内容周围的区域，内边距是透明的。
*   **Content(内容)** - 盒子的内容，显示文本和图像。

当我们设置一个元素的宽度或者高度时，只是设置的其 content 的宽高度，他真正占用的地方还有padding、border、margin。

上下相邻的普通元素，之间的边距并非简单的相加，而是取其中的最大值，这种现象叫做margin重叠。（float元素不会有这种现象）

### 块级元素

**Block element 块级元素**

顾名思义就是以块显示的元素，高度宽度都是可以设置的。比如我们常用 的<div>、<p>、<ul>默认状态下都是属于块级元素。块级元素比较霸道，默认状态下每次都占据一整个行，后面的内容必须再新起一行显示。当然非块级元素也可以通过css的  display:block;  将其更改成块级元素。

**块级元素特点：**

*   每个块级元素都从新的一行开始，并且其后的元素也另起一行。（霸道，一个块级元素独占一行）
*   元素的宽度(width)、高度(height)、行高(line-height)以及上下外边距(margin-top/margin-bottom)都可设置。当
*   元素宽度在不设置的情况下，是它本身父容器的100%（和父元素的宽度一致），当设置了宽度以后，左右边距(margin-left/margin-right)也可设置
*   p标签元素默认和其他元素有上下外边距(margin-top/margin-bottom)

块级元素左右居中(比如div在div中，p在div中)：**margin:  0   auto**

块级元素之间垂直居中(比如div在div中，p在div中时，有特殊情况，后面会有讲解)：设置相对定位方式

块级元素里面的文字左右居中： text-align: center

块级元素里面的字体上下居中：line-height:  100px; (文字相对于此块级元素上下垂直居中)

div元素中的文字水平和垂直居中

```
<style>      
       div{      
           background: red;      
           width: 100px;      
           height:100px;      
           text-align: center;   /*div里面的文字左右居中*/      
           line-height: 100px;   /*div里面的文字垂直对齐*/      
           margin:0 auto;        /*div左右居中*/      
       }      1
</style>      1
<div>      1
        hello,word!      1
</div>
```


div中的div水平和垂直居中

```
<style>      
      #one{      
            background: red;      
            width:500px;      
            height:500px;      
      }      
      #two{      
            background: green;      
            width:100px;      1
            height:100px;       1
            text-align: center;   /*div中的文字水平对齐*/      1
            line-height: 100px;   /*div中的文字垂直对齐*/       1
            position: absolute;   /*设置子div相对于父div相对定位*/      1
            top:200px;            /*子div相对于父div垂直对齐*/      1
            left:200px;           /*子div相对于父div垂直对齐*/      1
      }      1
</style>
```


注意：假如 p 在 div 中时，有特殊情况。当设置line-height时 (这里p在div中时，当父div设置ling-height是父div的高度时，此时p占满了整个父div，所以设置line-height可以让p里面的文字看起来是垂直对齐的。但是当p也设置了宽度和高度时，这时，文字不是垂直对齐的)

如下，此时的p不设置宽度和高度，这样p中的文字看起来是水平和垂直对齐的

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta charset="utf-8">      
    <title>css学习</title>      
    <style>      
        #one{      
            background: red;      
            width:500px;      1
            height:500px;      1
            line-height: 500px;    /*p元素垂直居中*/      1
        }      1
        #two{      1
            background: green;      1
            text-align: center;    /*p元素里面的文字水平居中*/       1
        }      1
    </style>      1
</head>      1
<body>      2
<div id="one">      2
    <p id="two">hello,word!</p>      2
</div>      2
</body>      2
</html>
```


![](https://img-blog.csdnimg.cn/2019030118370954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是当p也设置了宽度和高度时，此时，如下

```
<!DOCTYPE html>      
<html>      
<head>      
    <meta charset="utf-8">      
    <title>css学习</title>      
    <style>      
        #one{      
            background: red;      
            width:500px;      1
            height:500px;      1
            line-height: 500px;    /*文字垂直居中，相对于div*/      1
        }      1
        #two{      1
            background: green;      1
            width:100px;      1
            height:100px;      1
            text-align: center;    /*p元素里面的文字水平居中，相对于p元素*/      1
        }      1
    </style>      2
</head>      2
<body>      2
<div id="one">      2
    <p id="two">hello,word!</p>      2
</div>      2
</body>      2
</html>
```


![](https://img-blog.csdnimg.cn/20190301183755334.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

常见的块级元素：

\* address - 地址  
\* blockquote - 块引用  
\* center - 居中对齐块  
\* dir - 目录列表  
\* div - 常用块级容易，也是css layout的主要标签  
\* dl - 定义列表  
\* fieldset - form控制组  
\* form - 交互表单  
\* h1 - 大标题  
\* h2 - 副标题  
\* h3 - 3级标题  
\* h4 - 4级标题  
\* h5 - 5级标题  
\* h6 - 6级标题  
\* hr - 水平分隔线  
\* isindex - input prompt  
\* menu - 菜单列表  
\* noframes - frames可选内容，（对于不支持frame的浏览器显示此区块内容  
\* noscript - ）可选脚本内容（对于不支持script的浏览器显示此内容）  
\* ol - 排序表单  
\* p - 段落  
\* pre - 格式化文本  
\* table - 表格  
\* ul - 非排序列表

### 内联元素

**inline element 内联元素**

通俗点来说就是文本的显示方式，与块级元素相反，内联元素的高度宽度都是不可以设置的，其宽度就是自身文字或者图片的宽 度。我们常用到的<a>、<span>、<em>都属于内联元素。内联元素的显示特点就是像文本一样的显示，不会独自占据一个行。当然块级元素也能变成内联元素,那就是通过css的  display:inline;   或者 float 来实现 。

竖直方向无法设置margin属性，水平方向可以设置margin属性 ，行内元素之间本身没有边距

水平居中：先将其改为块级元素:  display:block;   然后：text-align:center         

```
<style>      
        a{      
            width:500px;      
            color:red;      
            background: yellowgreen;      
            display: block;        /*设置为块级元素*/      
            text-align: center;    /*文字水平居中*/      
            margin: 0 50px;        /*整个a标签水平居中，当设置了宽度时*/      
        }      1
</style>      1
<a href="http://www.baidu.com">百度一下，你就知道</a>
```


![](https://img-blog.csdnimg.cn/20190304090901779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

内联元素特点：  
1、和其他元素都在一行上；  
2、元素的高度、宽度及上下边距不可设置；  
3、元素的宽度就是它包含的文字或图片的宽度，不可改变

常见内联元素

\* a - 锚点  
\* abbr - 缩写  
\* acronym - 首字  
\* b - 粗体(不推荐)  
\* bdo - bidi override  
\* big - 大字体  
\* br - 换行  
\* cite - 引用  
\* code - 计算机代码(在引用源码的时候需要)  
\* dfn - 定义字段  
\* em - 强调  
\* font - 字体设定(不推荐)  
\* i - 斜体  
\* kbd - 定义键盘文本  
\* label - 表格标签  
\* q - 短引用  
\* s - 中划线(不推荐)  
\* samp - 定义范例计算机代码  
\* select - 项目选择  
\* small - 小字体文本  
\* span - 常用内联容器，定义文本内区块  
\* strike - 中划线  
\* strong - 粗体强调  
\* sub - 下标  
\* sup - 上标  
\* textarea - 多行文本输入框  
\* tt - 电传文本  
\* u - 下划线  
\* var - 定义变量

### 内联块元素

**inline-block内联块元素**

可以共享一行，有宽高属性，可以设置上下左右外边距，行内块元素自带会有边距。设置为行内块元素：**display: inline-block**

常见行内块元素：

\* input  
\* img

内联块元素的水平和垂直居中：

```
<style>      
       #one{      
           background: red;      
           width:500px;      
           height:500px;      
       }      
       img{      
           width:100px;      
           height:100px;      1
           margin:200px 200px;   /*水平和垂直居中*/      1
       }      1
</style>      1
<div id="one">      1
        <img src="images/b_0.gif" alt=""/>      1
</div>
```


定位
--

元素可以使用的顶部，底部，左侧和右侧属性定位。然而，这些属性无法工作，除非是先设定position属性。他们也有不同的工作方式，这取决于定位方法 position

### static

HTML元素的默认值，即没有定位，元素出现在正常的流中。  
静态定位的元素不会受到 top, bottom, left, right影响

### fixed

元素的位置相对于浏览器窗口是固定位置。  
即使窗口是滚动的它也不会移动  
比如我们有些网站旁边的广告，他是不随着窗口滚动而滑下去的。

### relative

相对定位元素的定位是相对其正常位置。

### absolute

绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于<html>

### sticky

sticky 英文字面意思是粘，粘贴，所以可以把它称之为粘性定位。

position: sticky; 基于用户的滚动位置来定位。

粘性定位的元素是依赖于用户的滚动，在 **position:relative** 与 **position:fixed** 定位之间切换。

它的行为就像 **position:relative;** 而当页面滚动超出目标区域时，它的表现就像 **position:fixed;**，它会固定在目标位置。

元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。

比如很多网站的顶部的导航栏就是粘性定位。

### 相关文章：[CSS教程](http://www.runoob.com/css/css-tutorial.html)

###