**目录**

[URL中的特殊字符](#t0)

[#号的作用](#t1)

* * *

### URL中的特殊字符

我们经常会看到URL中有一些特殊的字符，比如 #，？，& ，/ ，+ 等。那么，它们的含义是什么呢？

```
特殊含义                                                    十六进制       
+      表示空格（在URL中不能使用空格）                          %2B      
空格   URL中的空格可以用+号或者编码                             %20      
/      分隔目录和子目录                                        %2F      
?      分隔实际的URL和参数                                     %3F      
#      表示书签                                               %23      
&      URL中指定的参数间的分隔符                               %26      
=      URL中指定的参数的值                                    %3D         1
比如：http://www.xx.com/test/?name=admin&passwd=123456     test目录下的name参数等于admin，passwd参数等于123456       1
http://www.xx.com/?action=i+love+you            加号代替空格      1
http://www.xx.com/?action=i%20love%20you        空格编码
```


（1）在URL中，空格和单引号都会被编码，例如访问：https://www.baidu.com/?id=1&name='xi e'&pass="密码"

![](https://img-blog.csdnimg.cn/20190520100952518.png)

抓包是这样的，单引号，双引号、中文和空格都会被编码

![](https://img-blog.csdnimg.cn/2019052010093858.png)

（2）在我们的URL参数中有一个参数的值中包含了 & 的话，则在URL中必须将 & 给编码。例如我们的name参数的值是12&dsfa 的话，我们在URL中则必须将&给编码

[https://www.baidu.com/?id=1&name=12&dsfa](https://www.baidu.com/?id=1&name=12&dsfa)   ，这里没将&进行编码的话，浏览器则把12&dsfa给拆开了

![](https://img-blog.csdnimg.cn/20190519223503203.png)

[https://www.baidu.com/?id=1&name=12%26dsfa](https://www.baidu.com/?id=1&name=12%26dsfa) ，这里将 & 进行了编码，则浏览器将name的值解析为了 12&dsfa

![](https://img-blog.csdnimg.cn/2019051922353350.png)

### #号的作用

\# 代表网页中的一个位置。其右面的字符，就是该位置的标识符。比如，http://www.example.com/index.html#print就代表网页index.html的print位置。浏览器读取这个URL后，会自动将print位置滚动至可视区域。为网页位置指定标识符，有两个方法。一是使用锚点，比如<a name="print"></a>，二是使用id属性，比如<div id="print">

未完待续。。。。

参考文章：[URL中“#” “？” &“”号的作用](https://www.cnblogs.com/julin-peng/p/4237791.html)

                  [URL中“#” “？” &“”号的作用](https://www.cnblogs.com/kaituorensheng/p/3776527.html)