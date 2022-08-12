**目录**

[DOM的概念](#t0)

[HTML DOM 的方法和属性](#t1)

[代码举例](#t2)

* * *

### DOM的概念

**DOM**，全称Document Object [Model](https://so.csdn.net/so/search?q=Model&spm=1001.2101.3001.7020)，是一个平台和语言都中立的接口，可以使程序和脚本能够动态访问和更新文档的内容、结构以及样式。

在网站页面中有许多页面的元素，当页面到达浏览器时浏览器会为页面创建一个顶级的Document object文档对象，接着生成各个子文档对象，每个页面元素对应一个文档对象，每个文档对象包含属性、方法和事件。可以通过JS脚本对文档对象进行编辑从而修改页面的元素。也就是说，客户端的脚本程序可以通过DOM来动态修改页面内容，从客户端获取DOM中的数据并在本地执行。

在 HTML DOM 中，所有事物都是节点。DOM 是被视为节点树的 HTML

*   整个文档是一个文档节点
*   每个 HTML 元素是元素节点
*   HTML 元素内的文本是文本节点
*   每个 HTML 属性是属性节点
*   注释是注释节点  
     

![](https://img-blog.csdn.net/20181003205949505?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **HTML DOM 的方法和属性**

  
一些常用的 HTML DOM 方法：

*   getElementById(id) - 获取带有指定 id 的节点（元素）
*   appendChild(node) - 插入新的子节点（元素）
*   removeChild(node) - 删除子节点（元素）

一些常用的 HTML DOM 属性：

*   innerHTML - 节点（元素）的文本值
*   parentNode - 节点（元素）的父节点
*   childNodes - 节点（元素）的子节点
*   attributes - 节点（元素）的属性节点 

### 代码举例

改变html内容

```
x=document.getElementById("demo")  //查找元素      
x.innerHTML="Hello JavaScript";    //改变内容
```


改变html的图像

```
element=document.getElementById(“image”);      
element.src=”img/1.jpg”;
```


改变html的样式

```
x=document.getElementById("demo")  //找到元素      
x.style.color="#ff0000";           //改变样式
```


创建新的html元素

```
<div id="div1">      
  <p id="p1">这是一个段落</p>      
  <p id="p2">这是另一个段落</p>      
</div>      
<script>      
  var para=document.createElement("p");      
  var node=document.createTextNode("这是新段落。");      
  para.appendChild(node);      
  var element=document.getElementById("div1");      1
  element.appendChild(para);      1
</script>
```


删除已有的 HTML 元素

```
<div id="div1">      
    <p id="p1">这是一个段落。</p>      
    <p id="p2">这是另一个段落。</p>      
</div>      
<script>      
    var parent=document.getElementById("div1");      
    var child=document.getElementById("p1");      
    parent.removeChild(child);      
</script>
```
