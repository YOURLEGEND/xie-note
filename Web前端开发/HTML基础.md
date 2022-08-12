**目录**

[HTML](#t0)

[head中常见的标签](#t1)

[body中常见的标签](#t2)

[HTML实体](#t3)

[表单提交不跳转页面](#t4)

* * *

HTML
====

HTML：超文本标记语言（英语：HyperText Markup Language）是一种用于创建网页的标准标记语言

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


*   <html> ：该标签是根，所有内容放在它里面
*   <head> : 该标签放一些头部的信息
*   <body> : 正文部分，里面的内容直接用于显示

head中常见的标签
----------

<title> : 该标签定义了网页的标题

```
<title>谢公子的小黑屋</title>
```


<meta>：该标签提供了元数据，元数据不显示在页面上，但会被浏览器解析。meta 元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据。meta标签的作用有：搜索引擎优化（SEO），定义页面使用语言，自动刷新并指向新的页面，实现网页转换时的动态效果，控制页面缓冲，网页定级评价，控制网页显示的窗口等！meta标签的组成：meta标签共有两个属性，它们分别是 http-equiv属性和name属性，不同的属性又有不同的参数值，这些不同的参数值就实现了不同的网页功能 

```
<meta charset="UTF-8" >                                                 //指定网页的编码      
<meta http-equiv="content-Type" content="text/html;charset=UTF-8">      //指定网页内容的类型，和文字编码      
<meta http-equiv="refresh"  content="5;url=http://www.baidu.com">       //5秒之后跳转到指定页面      
<meta http-equiv="refresh"  content="2">                            //每隔2秒刷新一次页面      
<meta http-equiv="Set-Cookie"  content="cookievalue=xxx;">          //设置cookie      
<meta name="keywords" content="信息安全">  //为搜索引擎设置关键字      
<meta name="description" content="黑客 技术 安全">  //为网页定义描述内容      
<meta name="author" content="xie">   //定义网页作者
```


<base>：该标签描述了基本的路径，该网页下的所有链接文件默认都是从该路径下找文件

```
<base href="../xie">  //该网页下的文件默认都是从这个目录去找
```


<link>：该标签定义了文档与外部资源之间的关系，通常用于链接到样式表

```
<link rel="stylesheet" type="text/css" href="mystyle.css">
```


<style>：该标签定义了HTML文档的CSS样式文件

```
<style type="text/css">      
  p{      
    color:blue      
   }      
</style>
```


<script>：标签用于加载脚本文件，如： JavaScript

```
<script  src='xie.js'> </script>
```


body中常见的标签
----------

标题：   <h1>一级标题 </h1>   ........        <h6>六级标题</h6>  
段落 ：  <p> 段落 </p>  
代码 ：  <pre> </pre>  
超链接：<a href=""  target=”\_blank / \_parent">   </a>    
             target=\_blank的时候，是打开一个新页面;    target=\_parent的时候，是在原页面打开

超链接有三种：站内链接；站外链接；锚链接

超链接的动作

```
<style>            
 a{text-decoration: none;}        /*去除超链接的下划线*/      
 /*顺序必须是: l - v - h - a */      
 a:link {color:#000000;}      /* 未访问链接*/      
 a:visited {color:#00FF00;}  /* 已访问链接 */      
 a:hover {color:#FF00FF;font-size: larger; background-color: #2299ff;}  /* 鼠标移动到链接上 */      
 a:active {color:#0000FF;}  /* 鼠标点击时 */      
</style>      
<a href="http://www.baidu.com"  target=”_blank / _parent">   </a>
```


图片:     <img  src=”  ”  width=”50”  height="50"   alt=""   title=""/>     
            alt是当图片加载不出来的时候显示的东西 ,title是当鼠标放上去的时候显示的东西  
换行：   <br/>  
画横线   <hr/>  
删除线：<del></del>  
下划线：<ins></ins>  
上标： <sup>  
下标： <sub>  
无序列表   <ul>  <li></li>  <li><li>  </ul>  
有序列表   <ol>  <li></li>   <li><li> </ol>  
自定义列表 <dl> <dt></dt><dd></dd>  </dl>                
字符实体：   空格           大于：>         小于： <           双引号: "    
音乐：<audio src="1.mp3"  autoplay="autoplay"  loop="loop"   controls="controls"> </audio>  
视频：<video src="1.mkv"  controls="controls"    loop="loop"    width="1100px"   height="500px"></video>  
下拉列表： <select name="xueli" id="">  </select>  
表格：<table border='1px'  cellspacing='1px'   cellpadding='1px'> <tr><th></th></tr>  <tr><td></td></tr></table>  
         cellspacing是单元格之间的距离，cellpadding文字与单元格之间的距离  
[表单](https://so.csdn.net/so/search?q=%E8%A1%A8%E5%8D%95&spm=1001.2101.3001.7020)：<form action='action.php' method="get/post">  <input  type=''/></form>  
        type属性: button、checkbox、file、hidden、image、password、radio、reset、submit、text、number  
框架 <frameset>   <frame  src=”” />  <  /frameset>  
文本域：<textarea  name="intro"  rows="10"  cols="30">自我介绍</textarea>  
ifame框架：<iframe src=''  width='' height=''></frame>

用法如下：

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8" >      
    <title>谢公子的小黑屋</title>      
</head>      
<body>      
    <h1>一级标题</h1>      
    <h2>二级标题</h2>      1
    <h3>三级标题</h3>      1
    <h4>四级标题</h4>      1
    <h5>五级标题</h5>      1
    <h6>六级标题</h6>      1
    <p>这个p元素定义了 HTML 文档中的一个段落。这个元素拥有一个开始标签，以及一个结束标签</p>      1
    <pre>      1
        def Test():{      1
            print("hello,word!")      1
        }      1
    </pre>      2
    <a href="http://www.baidu.com" target="_blank">百度一下，你就知道</a> <br/>      2
    锚链接：<a href="#zhuce">去注册</a> <br/>      2
    <img src="1.jpg" width="433" height="262" title="黑客" alt="hack">  <br/>      2
    <del>删除线</del>  <br/>      2
    <ins>下划线</ins>  <br/>       2
    <hr/>      2
    2<sup>3</sup>      <br/>      2
    log<sub>10</sub>   <br/>      2
    大于：&gt;  <br/>      3
    小于：&lt;  <br/>      3
    双引号：&quot;  <br/>      3
    空&nbsp;格 <br/>      3
    <ul>      3
        <li>无序列表one</li>      3
        <li>无序列表two</li>      3
        <li>无序列表three</li>      3
    </ul>      3
    <ol>      3
        <li>有序列表1</li>      4
        <li>有序列表2</li>      4
        <li>有序列表3</li>      4
    </ol>      4
    <dl>      4
        <dt>刘诗诗</dt>      4
        <dd>著名女演员</dd>      4
        <dt>陈道明</dt>      4
        <dd>著名男演员</dd>      4
    </dl>      4
    <table border="1px" cellspacing="1px" cellpadding="1px" width="200px" >      5
        <caption>课程表</caption>      5
        <tr>      5
            <th>aa</th>      5
            <th>sss</th>      5
            <th>dd</th>      5
            <th>bbbb</th>      5
        </tr>      5
        <tr>      5
            <td colspan="2">aasdfa</td>      5
            <td>dd</td>      6
            <td rowspan="2">bbbb</td>      6
        </tr>      6
        <tr>      6
            <td>aa</td>      6
            <td>sssafda</td>      6
            <td>      6
                <table border="1" cellpadding="10" cellspacing="10">      6
                    <tr><td>111</td><td>222</td></tr>      6
                    <tr><td>3333</td><td>4444</td></tr>      6
                </table>      7
            </td>      7
        </tr>      7
    </table>      7
    <form action="action.php" method="POST">      7
        <a name="zhuce"></a>      7
        账号： <input type="text" name="username" placeholder="请输入你的用户名"/> <br/>      7
        密码： <input type="password" name="passwd"/> <br/>      7
        性别： <input type="radio" name="sex" value="man" checked/>男      7
               <input type="radio" name="sex" value="woman"/>女 <br/>      7
        爱好： <input type="checkbox" name="hobby" value="sport"/>运动      8
              <input type="checkbox" name="hobby" value="music"/>听音乐      8
              <input type="checkbox" name="hobby" value="reader"/>阅读 <br/>      8
        选择城市：<select name="city" id="">      8
                     <option value="beijing">北京</option>      8
                     <option value="shanghai">上海</option>      8
                     <option value="shenzhen">深圳</option>      8
                     <option value="gaozhong">广州</option>      8
                     <option value="tainjin">天津</option>      8
                 </select> <br/>      8
        个人描述： <br/>      9
        <textarea  cols="30" rows="10"></textarea> <br/>      9
        <input type="submit" value="注册"/>      9
        <input type="reset" value="重置"/>      9
    </form>      9
    <audio src="1.mp3" autoplay=""  controls="controls"></audio> <br/>      9
    <video src="1.mp4" controls="controls"  width="1000px" height="300px"></video> <br/>      9
    <iframe src="https://www.baidu.com"  width="1000"  height="1000"  frameborder="0"> </frame>      9
</body>      9
</html>
```


HTML实体
------

<table border="1" cellspacing="0" style="margin-left:.9pt;width:369pt;"><tbody><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><strong><span style="color:#000000;"><strong>显示结果</strong></span></strong></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><strong><span style="color:#000000;"><strong>描述</strong></span></strong></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><strong><span style="color:#000000;"><strong>实体名称</strong></span></strong></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;">&nbsp;</p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">空格</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;">&nbsp;</p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">&lt;</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">小于号</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">&lt;</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">&gt;</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">大于号</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">&gt;</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">&amp;</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">和号</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">&amp;</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">"</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">引号</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">"</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">'</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">撇号&nbsp;</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">' (IE不支持)</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">￠</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">分（cent）</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">¢</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">£</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">镑（pound）</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">£</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">¥</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">元（yen）</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">¥</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">€</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">欧元（euro）</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">€</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">§</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">小节</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">§</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">©</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">版权（copyright）</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">©</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">®</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">注册商标</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">®</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">™</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">商标</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">™</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">×</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">乘号</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">×</span></p></td></tr><tr><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">÷</span></p></td><td style="width:102.75pt;"><p style="margin-left:0pt;"><span style="color:#000000;">除号</span></p></td><td style="width:163.5pt;"><p style="margin-left:0pt;"><span style="color:#000000;">÷</span></p></td></tr></tbody></table>

表单提交不跳转页面
---------

我们在提交表单的时候，往往会跳转到提交表单的页面。那么，我们如何让其不跳转到页面呢？

如下，在form表单下添加一个iframe标签，并且将其name的值设置为form标签的target的值，并且设置iframe标签不显示，这样，我们看起来提交表单后也不会跳转页面了。

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title>表单页面不跳转</title>      
</head>      
<body id="body">      
<form action="http://120.79.74.249:8080" method="get" target="frameName">      
    用户名：<input type="text" name="user"/> <br/>      1
    密码：  <input type="password" name="pass"/> <br/>      1
    <input type="submit" value="提交"/>      1
</form>      1
<iframe src="" frameborder="0" name="frameName" style="display: none"></iframe>      1
</body>      1
</html>
```


### 相关文章：[HTML5教程](http://www.runoob.com/html/html-tutorial.html)