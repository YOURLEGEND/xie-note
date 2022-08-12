**目录**

[Bootstrap](#t0)

[Bootstrap包](#t1)

[Bootstrap的使用](#t2)

* * *

Bootstrap
---------

[Bootstrap](https://so.csdn.net/so/search?q=Bootstrap&spm=1001.2101.3001.7020) 是基于 HTML、CSS、JavaScript 开发的简洁、直观、强悍的前端开发框架，使得 Web 开发更加快捷。Bootstrap提供了优雅的HTML和CSS规范，它是由动态CSS语言 Less (Less 是一门 CSS 预处理语言，它扩充了 CSS 语言，增加了诸如变量、混合（mixin）、函数等功能，让 CSS 更易维护、方便制作主题、扩充。) 写成。

### Bootstrap包

**基本结构**：Bootstrap 提供了一个带有[网格](https://so.csdn.net/so/search?q=%E7%BD%91%E6%A0%BC&spm=1001.2101.3001.7020)系统、链接样式、背景的基本结构。这将在 **B**ootstrap 基本结构 部分详细讲解。

*   **CSS**：Bootstrap 自带以下特性：全局的 CSS 设置、定义基本的 HTML 元素样式、可扩展的 class，以及一个先进的网格系统。这将在 Bootstrap CSS 部分详细讲解。
*   **组件**：Bootstrap 包含了十几个可重用的组件，用于创建图像、下拉菜单、导航、警告框、弹出框等等。这将在 布局组件 部分详细讲解。
*   **JavaScript 插件**：Bootstrap 包含了十几个自定义的 jQuery 插件。您可以直接包含所有的插件，也可以逐个包含这些插件。这将在 Bootstrap 插件 部分详细讲解。
*   **定制**：您可以定制 Bootstrap 的组件、LESS 变量和 jQuery 插件来得到您自己的版本。

### Bootstrap的使用

引入bootstrap的样式表

```
<link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/4.0.0/css/bootstrap.min.css">
```


Bootstrap 中的许多组件需要依赖 JavaScript 才能运行。具体来说，他们依赖的是 JQuery、Popper.js 以及我们自己开发的 JavaScript 插件。具体操作就是将下列 `<script>`标签放到页面底部的 `</body>` 标签之前。注意顺序，jQuery 必须放在最前面，然后是 Popper.js，最后是我们自己的 JavaScript 插件

```
<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.slim.min.js"></script>      
<script src="https://cdn.bootcss.com/popper.js/1.12.9/umd/popper.min.js"></script>      
<script src="https://cdn.bootcss.com/bootstrap/4.0.0/js/bootstrap.min.js"></script>
```


为了确保在所有设备上能够正确渲染并支持触控缩放，务必将**设置 viewport 属性的 meta 标签**添加到 `<head>` 中 

```
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```


参考文章：[http://www.runoob.com/bootstrap/bootstrap-tutorial.html](http://www.runoob.com/bootstrap/bootstrap-tutorial.html)