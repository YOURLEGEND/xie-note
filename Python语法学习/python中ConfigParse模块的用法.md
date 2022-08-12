ConfigParse
-----------

ConfigParse模块是python中用来读配置文件的，用法很简单。在Python2.x 中名为 ConfigParser，3.x 已更名小写，并加入了一些新功能。

如下是python3环境下的示例代码。

这里假设我们这里有一个配置文件config.txt，内容如下：

```
[DEFAULT]      
name = xx      
age = xx       
scopt = xx       
[people1]      
name = wu      
age = 18      
sex = man       1
[people2]      1
name = wang      1
age = 28      1
sex = men       1
[people3]      1
name = li      1
age = 38      1
sex = man
```


\[ \]包含的为section，section下是key-value值。可以是 key=value的格式，也可以是 key : value 的格式。\[DEFAULT\] 一般包含 ini 格式配置文件的默认项，所以 configparser 部分方法会自动跳过这个 section 。 sections() 方法是获取不到DEFAULT的，还有clear()以及remove\_option()方法对 \[DEFAULT\] 也无效：

```
import configparser       
conf=configparser.ConfigParser()      
conf.read("config.txt")       
#获取所有的sections      
print("获取所有的sections")      
print(conf.sections())      
print("\n")       1
#获取指定section的key-value，返回的是一个数组      1
print("获取指定section的key-value，返回的是一个数组")      1
print(conf.items("people1"))      1
print("\n")       1
#获取指定section的key-value      1
print("获取指定section的key-value")      1
name=conf.get("people2","name")      1
age=conf.get("people2","age")      2
sex=conf.get("people2","sex")      2
print("name is :"+name)      2
print("age is :"+age)      2
print("sex is :"+sex)      2
print("\n")       2
#获取指定section的keys      2
print("获取指定section的keys")      2
print(conf.options("people3"))      2
print("\n")       3
#修改或添加指定section的指定key的value值      3
print("修改指定section的指定key的value值")      3
conf.set("people1","name","wu")      3
conf.set("people1","scope","98")      3
name=conf.get("people1","name")      3
scope=conf.get("people1","scope")      3
print("name is :"+name)      3
print("scope is :"+scope)      3
print("\n")       4
#添加section      4
print("添加section")      4
conf.add_section("people4")      4
conf.set("people4","name","liu")      4
conf.write(open("config.txt","w"))      #一定要写入才能生效      4
name=conf.get("people4","name")      4
print("name is :"+name)      4
print("\n")       5
#删除section      5
print("删除section")      5
conf.remove_section("people4")      5
conf.write(open("config.txt","w"))      #一定要写入才能生效       5
#移除指定section的指定key      5
conf.remove_option("people2","name")      5
conf.write(open("config.txt","w"))      #一定要写入才能生效       6
#清楚[DEFAULT]之外的所有内容      6
conf.clear()      6
conf.write(open("config.txt","w"))      #一定要写入才能生效
```


![](https://img-blog.csdnimg.cn/2020072523511229.png) 

参考文章：[Python3 中 configparser 模块解析配置的用法详解](https://www.cnblogs.com/zkqiang/p/10515092.html)