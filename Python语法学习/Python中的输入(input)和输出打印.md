**目录**

[最简单的打印](#t0)

[打印数字](#t1)

[打印字符](#t2)

[字符串的格式化输出](#t3)

[python中让输出不换行](#t4)

* * *

### 以下的都是在Python3.X环境下的

使用 input 函数接收用户的输入，返回的是 str [字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)

![](https://img-blog.csdnimg.cn/20181030110744556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 最简单的打印

```
>>print("hello,word!")      
hello,word!
```


### 打印数字

```
>>a=5      
>>b=6      
>>print(a)      
>>print(a,b)      
>>print(a+b)      
5      
5 6      
11
```


### 打印字符

使用逗号连接会有空格，使用+号连接没有空格

```
>>a="hello,"      
>>b="world!"      
>>print(a,b)      
>>print(a+b)      
hello, world!      
hello,world!
```


特别注意，当字符串是等于一个数的时候，这样两个字符串相加还是字符串。要把字符串转化为数字类型的才可以使用相加

```
>>a=input("请输入第一个数字：")         #20      
>>b=input("请输入第二个数字: ")         #10      
>>print(a,b)      
>>print(a+b)      
>>print(int(a)+int(b))      
20 10      
2010      
30
```


### 字符串的格式化输出

```
>>name="小谢"      
>>age="20"      
>>print("{}的年龄是{}".format(name,age))      
>>print("%s的年龄是%s"%(name,age))      
小谢的年龄是20      
小谢的年龄是20       
>>print("i have a {1} and have a {0}".format("apple","orange"))      
i have a orange and have a apple       1
>>print("i have a {one} and have a {two}".format(one="apple",two="orange"))      1
i have a apple and have a orange       1
>>print("i have a {} and have a {two}".format("apple",two="orange"))      1
i have a apple and have a orange       1
>>import math      1
>>print("{1:.3f} and {0.4f}".format(math.pi,math.e))      1
2.718 and 3.1416       2
#自动填充      2
>>print('12'.zfill(5))      2
>>print('-3.14'.zfill(7))      2
00012      2
-003.14       2
>>print('Hi,%s!'%input('Please enter your name!'))    //接收用户的输入，然后打印出来      2
Please enter your name!xie                   // xie 是用户输入的      3
Hi,xie!
```


### pprint打印

pprint模块用于打印 Python 数据结构. 当你打印特定数据结构时你会发现它很有用(输出格式比较整齐, 便于阅读)

```
import pprint      
data = (      
    "this is a string", [1, 2, 3, 4], ("more tuples",      
    1.0, 2.3, 4.5), "this is yet another string"      
    )       
print(data)      
print("*"*100)      
pprint.pprint(data)      1
######################################      1
('this is a string', [1, 2, 3, 4], ('more tuples', 1.0, 2.3, 4.5), 'this is yet another string')      1
******************************************************************************************      1
('this is a string',      1
 [1, 2, 3, 4],      1
 ('more tuples', 1.0, 2.3, 4.5),      1
 'this is yet another string')
```


### [python中让输出不换行](https://blog.csdn.net/qq_36119192/article/details/82951113)

文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-44)[其他](https://edu.csdn.net/skill/python/python-3-44)[输入与输出](https://edu.csdn.net/skill/python/python-3-44)84785 人正在系统学习中