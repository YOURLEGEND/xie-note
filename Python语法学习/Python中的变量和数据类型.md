**目录**

[变量](#t0)

[全局变量和局部变量](#t1)

[数据类型](#t2)

[数字](#t3)

[整数](#t4)

[浮点数](#t5)

[复数](#t6)

[序列](#t7)

[字符串str](#t8)

[列表List](#t9)

[元组tuple](#t10)

[序列的访问（切片）](#t11)

[数据的嵌套](#t12) 

[非序列](#t13)

[字典dict](#t14)

[集合set](#t15)

[布尔型](#t16)

[空值](#t17)

* * *

变量
==

变量是计算机内存中的一块区域，变量可以存储任何值（也称指向），而且值可以改变。变量是存储在内存中的值。这就意味着在创建变量时会在内存中开辟一个空间。基于变量的数据类型，解释器会分配指定内存，并决定什么数据可以被存储在内存中。因此，变量可以指定不同的数据类型，这些变量可以存储整数，小数或[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)等。

变量命名规则：变量名的长度不受限制，必须是字母、数字或下划线（\_）的组合，不能使用空格、连字符、标点符号、引号或其他字符， 变量名的第一个字符不能是数字，必须是字母或下划线，变量名区分大小写。

*   正常变量：正常变量名是公开的（public），可以被直接引用，比如：a，name，等；
*   特殊变量：类似 \_\_xxx\_\_ 这样，可以被直接引用，但是有特殊用途，比如 \_\_author\_\_ ，\_\_name\_\_ 就是特殊变量，hello模块定义的文档注释也可以用特殊变量 \_\_doc\_\_ 访问，我们自己的变量一般不要用这种变量名；
*   私有变量：类似 \_xxx 和 \_\_ xxx这样的函数或变量就是非公开的（private），不应该被直接引用，比如 \_abc，\_\_abc 等
*   常量：通常全部用大写的字母表示：如   PI = 3.14159265359   ，但事实上PI仍然是一个变量，Python根本没有任何机制保证PI不会被改变，所以，用全部大写的变量名表示常量只是一个习惯上的用法，如果你一定要改变变量PI的值，也没人能拦住你。

python自带的变量，我们不能用下面的变量名：

![](https://img-blog.csdnimg.cn/20181029123831366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_27,color_FFFFFF,t_70)

全局变量和局部变量
---------

[全局变量](https://so.csdn.net/so/search?q=%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F&spm=1001.2101.3001.7020)是在整个py文件中声明，全局范围内都可以访问的变量。

局部变量是在某个函数中声明的，只能在该函数中调用它，如果试图在超出范围的地方调用，程序就爆掉了。

如果我们在全局申明了一个变量，我们想要在函数内部使用这个变量时，我们需要用 **global** 指定这个变量。

如下，我们在函数式内部使用全局变量时，没用global指定，所以发生了报错！

```
a="hello"      
def test():      
    a=a+"world!"      
    print(a)      
test()
```


![](https://img-blog.csdnimg.cn/20190315140812182.png)

当我们用global指定后

```
a="hello"      
def test():      
    global a      
    a=a+"world!"      
    print(a)      
test()
```


![](https://img-blog.csdnimg.cn/20190315140913436.png)

我们再看看另一个跟变量相关的关键字nonlocal，字面意思就是指当前的这个变量不是局部变量。

在一个返回函数中，我们在内部的函数中调用外部的函数的变量，这时，我们就需要用nonlocal指定了。

```
def test():      
    a="hello"      
    def test2():      
        a=a+"world"      
        print(a)      
    return test2()      
test()
```


![](https://img-blog.csdnimg.cn/20190315141038208.png)

```
def test():      
    a="hello"      
    def test2():      
        nonlocal a      
        a=a+"world"      
        print(a)      
    return test2()      
test()
```


![](https://img-blog.csdnimg.cn/20190315141125985.png)
======================================================

数据类型
====

![](https://img-blog.csdnimg.cn/20181029193013209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

数字
--

### 整数

Python可以处理任意大小的整数，当然包括负整数，在程序中的表示方法和数学上的写法一模一样，例如：1，100，-8080，0，等等。计算机由于使用二进制，所以，有时候用十六进制表示整数比较方便，十六进制用 0 x前缀和 0-9，a-f 表示，例如：0xff00，0xa5b4c3d2，等等。

*   整数的除法：5/2       # 2.5
*   整数的整除：5//2      \# 2
*   整数的取余:   5%2    \# 1  
     

### 浮点数

[浮点数](https://so.csdn.net/so/search?q=%E6%B5%AE%E7%82%B9%E6%95%B0&spm=1001.2101.3001.7020)也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的，比如，1.23x10^9和12.3x10^8是完全相等的。浮点数可以用数学写法，如1.23，3.14，-9.01，等等。但是对于很大或很小的浮点数，就必须用科学计数法表示，把10用e替代，1.23x10^9就是1.23e9，或者12.3e8，0.000012可以写成1.2e-5，等等。  
整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（除法难道也是精确的？是的！），而浮点数运算则可能会有四舍五入的误差

### 复数

a=5+6j

序列
--

序列是Python中最基本的数据结构。序列中的每个元素都分配一个索引，第一个索引是0，第二个索引是1，依此类推。每个索引对应一个元素。序列类似于其他语言的数组，可以通过索引查找或者通过索引修改值

### 字符串str

a="hello,word!"

字符串是 Python 中最常用的数据类型。我们可以使用引号 ( ' 或 " )来创建字符串。

创建字符串很简单，只要为变量分配一个值即可。例如：

```
a='hello'      
b="hello"      
c="let's go"
```


原始字符串

```
a=r"hello,\nworld"        这里是 \n 不进行转义
```


字符串转换为字节 

```
a=b"root"         a的type是bytes
```


字符串的切片

```
>>a="hello,word!"      
>>print(a[0:5])      
hello      
>>print(a[0::2])      
hlowr!      
>>print(a[-3:-1])      
rd
```


字符串的格式化 

```
a="hel{},{}rd!"      
i="lo"      
j="wo"      
a=a.format(i,j)      
print(a)       
>> hello,word!
```


转义字符串

<table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td>\（在行尾时）</td><td>\n</td><td>\v</td><td>\t</td><td>\r</td><td>\f</td><td>\e</td><td style="width:75px;">\0000</td><td style="width:66px;">\b</td></tr><tr><td>续行符</td><td>换行</td><td>纵向制表符</td><td>横向制表符</td><td>回车</td><td>换页</td><td>转义</td><td style="width:75px;">空</td><td style="width:66px;">退</td></tr></tbody></table>

字符串的常用方法

*   len("hello"):               返回字符串的长度
*   str.capitalize()  :  将原来的字符串的首字母大写，并返回，返回值为str类型
*   str.lower():             将字符串所有字母小写，并返回，返回值为str类型
*   str.upper():            将字符串所有字母大写，并返回，返回值为str类型
*   str.count('a'):              判断字符串存在几个字符a, 返回值为int类型
*   str.find('a')：         判断字符a在字符串中的位置，返回字符a的索引
*   str.split('.')：           将字符串中的 . 分隔开 ，返回一个列表对象
*   str.strip()         该方法用于移除字符串头尾指定的字符（默认为空格）(只能移除头尾的，不能移除中间的字符)。
*   “--".join(a)           将分隔的字符用--连接起来
*   str.replace("aa","bb")    将字符串中所有的aa字符替换为bb字符
*   str.startswith("aa")   判断字符串是否以aa开头，返回True或者False
*   str.format()         格式化字符串
*   str.encode("utf-8")：  对字符串进行utf-8编码

[字符串的排序](https://blog.csdn.net/qq_36119192/article/details/83574638#%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%8E%92%E5%BA%8F)

### [列表List](https://blog.csdn.net/qq_36119192/article/details/83585254)

### 元组tuple

 a=(1,2,3,"hello")         

元组是一种不可变序列，即创建之后不能再做任何修改。 元组由不同的元素组成，每个元素可以存储不同类型的数据，如字符串、数字甚至元组、列表。 元组通常代表一行数据，而元组中的元素代表不同的数据项  
   
当元组中的数据包含可变元素的数据类型，例如列表时，该数据类型的元素是可以改变的

```
>>a=[1,2,3]      
>>b=(4,a,6)      
b=(4,[1,2,3],6)      
>>a[1]=10      
b=(4,[1,10,3],6)
```


元组支持相加和乘以一个数，元组不支持相减和相除

```
元组的相加      
>>t1=(1,'two',3)      
>>t2=(2,'three',5)      
>>print(t1+t2)      
>>print(t1*2)      
(1, 'two', 3, 2, 'three', 5)      
(1, 'two', 3, 1, 'two', 3)
```


### 序列的访问（切片）

字符串、列表和元组均支持索引访问，也就是切片。

下面以列表为例

```
设 L=[1 , 2, 3, 4, 5, 6]       
L[0:3] = [ 1, 2, 3]     从索引0开始取，直到索引3为止，但不包括索引3      
L[:3]   = [ 1, 2, 3]       第一个参数不写默认是0      
L[3:5]=[4,5]                  从索引3开始取，直到索引5为止，但不包括索引5      
L[2:5:2]=[3,5]            从索引2开始取，直到索引5为止，但不包括索引5，步长为2      
L[-2:] = [5,6]           从倒数第二个开始取，知道结束      
L[::2]=[1,3,5]           取所有的数，指定步长为2
```


### 数据的嵌套 

![](https://img-blog.csdnimg.cn/20181030162954513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

非序列
---

### [字典dict](https://blog.csdn.net/qq_36119192/article/details/83585440)

### 集合set

b={ 1 , 2 , 4 }

在Python中，[集合](https://so.csdn.net/so/search?q=%E9%9B%86%E5%90%88&spm=1001.2101.3001.7020)是一系列不重复的元素。集合类似于字典，但只包含键，而没有相关联的值。 在Python中，集合是相对较新的功能，在其还不支持集合时，一般使用字典模拟集合。 集合分两类：可变集合（set）和 不可变集合（frozenset）。对于可变集合，可添加和删除元素，而不可变集合一旦创建就不能更改。 与字典一样，集合的元素排列顺序也是不确定的。集合没有特殊的表示方法，而是通过一个set函数转换成集合，集合不能通过索引取值

```
>>a="abcbsgacbt"      
>>b=set(a)      
>>print(b)      
{'t', 's', 'c', 'g', 'a', 'b'}
```


  
集合中的函数:  add、clear、copy、difference、difference\_update、discard、intersection、intersection\_update、isdisjoint、issubset、issuberset、pop、remove、symmetric\_difference、symmetric\_difference\_update、union、update

布尔型
---

True    False

任何一个非0的值都是True，0的值都是False

空值
--

空值是Python里一个特殊的值，用None表示。None不能理解为0，也不能理解为""，None是一个特殊的空值。

```
>>a=None      
>>print(a,type(a))      
None <class 'NoneType'>       
a=""      
b=0      
if a==None:      
    print(True)      1
elif b==None:      1
    print("Middle")      1
else:      1
    print(False)      1
##################      1
False
```


文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-126)[其他](https://edu.csdn.net/skill/python/python-3-126)[Python语言基础](https://edu.csdn.net/skill/python/python-3-126)84785 人正在系统学习中