### HPP(HTTP参数污染)

**HPP**是HTTP Parameter Pollution的缩写，意为HTTP参数污染。  
**原理：**浏览器在跟服务器进行交互的过程中，浏览器往往会在GET/POST请求里面带上参数，这些参数会以 名称-值 对的形势出现，通常在一个请求中，同样名称的参数只会出现一次。但是在HTTP协议中是允许同样名称的参数出现多次的。比如下面这个链接：http://www.baidu.com?name=aa&name=bb ，针对同样名称的参数出现多次的情况，不同的服务器的处理方式会不一样。有的服务器是取第一个参数，也就是name=aa。有的服务器是取第二个参数，也就是name=bb。有的服务器两个参数都取，也就是name=aa,bb 。这种特性在绕过一些服务器端的逻辑判断时，非常有用。

HPP漏洞，与Web服务器环境、服务端使用的脚本有关。如下是不同Web服务器对于出现多个参数时的选择：

![通过HTTP参数污染（HPP）接管账户](https://www.4hou.com/uploads/20180714/1531545862769692.png)

当网站开发者不熟悉Web服务器对于多参数时如何选择，将给攻击者可乘之机。HPP能针对客户端和服务端进行攻击。

HPP参数污染还可以用于绕过某些防火墙对于 SQL注入的检测，例如当Web服务器对多参数都同时选择时，我们可以用以下这种方式绕过某些防火墙：

```
http://www.baidu.com/index.asp?page=select 1,2,3 from table where id=1      
http://www.baidu.com/index.asp?page=select 1&page=2,3 from table where id=1
```


参考文章：[Web应用里的HTTP参数污染（HPP）漏洞](https://blog.csdn.net/xiaoWhite_meng/article/details/80507583)

相关文章：[通过HTTP参数污染（HPP）接管账户](https://www.4hou.com/technology/12465.html)