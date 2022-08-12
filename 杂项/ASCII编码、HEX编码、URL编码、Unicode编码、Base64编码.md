**目录**

[ASCII编码](#t0)

[HEX编码](#t1)

[URL编码](#t2)

[Unicode编码](#t3)

[Base64编码](#t4)

* * *

ASCII编码
-------

*   字符的ascii编码可以对照ASCII编码表
*   中文的ASCII编码是对照unicode编码表

```
string = input("请输入一个字符: ")      
print(string+" 的ASCII码为：",ord(string))  		   #将字符转换为ascii码值       
number = input("请输入一个ASCII值: ")      
number = int(number)                              #将接收到的字符数字转换int格式      
print(number," 对应的字符是：",chr(number))    #将ascii码值转换为字符
```


![](https://img-blog.csdnimg.cn/20210521143002351.png) 

![](https://img-blog.csdnimg.cn/20200229230721866.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

HEX编码
-----

hex编码就是16进制编码，是字符的[ascii码](https://so.csdn.net/so/search?q=ascii%E7%A0%81&spm=1001.2101.3001.7020)值的16进制表示

```
string = input("请输入一个字符: ")                    #接收一个字符      
number=ord(string)                                   #得到该字符的ASCII值      
print(string+" 的16进制表示为：",hex(number))  		 #将字符的ASCII值转换为16进制形式       
string2 = input("请输入16进制表示: ")                 #接收一个16进制格式的ascii值      
number2=int(string2,16)                              #将该16进制的数据转为10进制的数据ascii值      
print(string+" 的16进制表示为：",chr(number2))  	     #将该10进制的ascii转为字符
```


![](https://img-blog.csdnimg.cn/20200229232742934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

URL编码
-----

*   字母不需要进行URL编码
*   特殊字符的URL编码是其ASCII值的16进制表示，前面加个%
*   中文的URL编码是其UTF-8编码前面加个 %

```
from urllib.parse import quote      
from urllib.parse import unquote       
string = input("请输入一个字符: ")      
print(string+" 的URL编码为：",quote(string,"utf-8"))       #将字符进行URL编码       
string2 = input("请输入一个URL编码: ")      
print(string2+" 对应的字符为：",unquote(string2,"utf-8"))  #将URL编码转换为字符
```


![](https://img-blog.csdnimg.cn/20200229234443509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Unicode编码
---------

*   字符的unicode编码是其ascii值前加上 &#   
*   中文的unicode编码是其hex编码的 0x 换成 \\u

```
string = input("请输入一个字符: ")      
print(string+" 的unicode编码为：",string.encode('unicode_escape').decode('utf-8'))  		   #将字符转换为ascii码值       
print("\u4e2d")           #将unicode编码转为字符，直接打印即可
```


![](https://img-blog.csdnimg.cn/20200301002649818.png)

Base64编码
--------

```
import base64      
string = input("请输入一个字符: ")      
print(string+" 的Base64编码为：",base64.b64encode(string.encode('utf-8')).decode("utf-8"))   #字符转为base64编码       
string2 = input("请输入一个base64编码: ")      
print(string2+" 对应的字符为：",base64.b64decode(string2.encode('utf-8')).decode("utf-8"))   #base64编码转为字符
```


![](https://img-blog.csdnimg.cn/20200301003258571.png)