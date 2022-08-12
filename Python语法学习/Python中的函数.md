**目录**

[Python内置函数](#t0)

[函数式编程](#t1)

[匿名函数(lambda)](#t2)

[函数作为参数](#t3) 

[返回函数(闭包)](#t4)

[装饰器](#t5)

[Python中内置的装饰器](#t6)

[偏函数](#%E5%81%8F%E5%87%BD%E6%95%B0)

[高阶函数](#t7)

[map](#t8)

[reduce](#t9)

[filter](#t10)

[sorted](#t11)

[自定义函数](#t12)

[参数](#t13)

[默认参数](#t14)

[可变参数](#t15)

[关键字参数](#t16)

[返回值](#t17)

* * *

函数是用于实现某一功能的接口，通过对函数传入参数，用来实现某一功能。

Python内置函数
==========

![](https://img-blog.csdnimg.cn/20181030092515158.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Python内置函数](https://www.runoob.com/python/python-built-in-functions.html)

函数式编程
=====

匿名函数(lambda)
------------

![](https://img-blog.csdnimg.cn/20181030095550412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

普通函数

```
>>def Sum(x,y):      
>>    return x+y      
>>Sum(2,3)      
5
```


匿名函数

```
>>Sum=lambda x,y:x+y        //冒号之前的是匿名函数的参数，冒号后面的是匿名函数的返回值      
>>Sum(2,3)      
5
```


匿名函数的应用 

```
>>def f(n):      
>>    return lambda x:x+n      
>>f=f(42)          #相当于 f=f(n)=42+n      
>>print(f(8))                 
50
```


函数作为参数 
-------

```
def Sum(fun,x1,x2):      
    return fun(x1,x2)      
def add(x1,x2):      
    return x1+x2      
a=Sum(add,2,3)      
print(a)      
## 5
```


返回函数(闭包)
--------

[高阶函数](https://so.csdn.net/so/search?q=%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0&spm=1001.2101.3001.7020)除了可以接受函数作为参数外，还可以把函数作为结果值返回。

```
def outer(a):               //外函数      
    b=10      
    def inner():            //内函数，      
        return a+b          //内函数中用到了外函数的临时变量      
    return inner            //返回内函数的应用      
demo=outer(5)               //demo 等于 内函数的引用      
demo()      
##  15       1
def outer(n):              //外函数      1
    def inner(a):          //内函数，接受参数      1
        return a**n        //调用外函数的参数      1
    return inner      1
x=outer(2)(3)      1
print(x)      1
## 9
```


当我们调用 outer(5) 时，返回的并不是求和结果，而是内部 inner() 函数的引用，调用函数 demo() 时，才真正计算求和的结果：  
在这个例子中，我们在函数 outer() 中又定义了函数 inner()，并且，内部函数 inner() 可以引用外部函数 outer() 的参数和局部变量，当 outer() 返回函数 inne r时，相关参数和变量都保存在返回的函数中，这种称为 “ 闭包（Closure）”的程序结构拥有极大的威力。外部函数发现，自己的临时变量会在将来的内部函数中用到，自己在结束的时候，返回内部函数的同时，会把外函数的临时变量送给内函数绑定在一起。所以外函数已经结束了，调用内函数的时候仍然能够使用外函数的临时变量  
   
在内部函数中修改外部函数的变量：可以用 nonlocal 关键字声明这个变量， 表示这个变量不是局部变量空间的变量，需要向上一层变量空间找这个变量

```
def outer(a):              //外函数      
    b=10      
    def inner():           //内函数，      
        nonlocal b         //表示这个变量不是局部变量，需要向上一层变量空间找这个变量      
        b=20               //修改外变量的临时参数      
        return a+b         //内函数中用到了外函数的临时变量      
    return inner           //返回内函数的引用      
demo=outer(5)              //demo 等于 内函数的引用      
demo()      1
###  25
```


还有一点需要注意：使用[闭包](https://so.csdn.net/so/search?q=%E9%97%AD%E5%8C%85&spm=1001.2101.3001.7020)的过程中，一旦外函数被调用一次返回了内函数的引用，虽然每次调用内函数，是开启一个函数执行过后消亡，但是闭包变量实际上只有一份，每次开启内函数都在使用同一份闭包变量

```
def outer(a):               //外函数      
    def inner(b):           //内函数，      
        nonlocal a          //表示这个变量不是局部变量，需要向上一层变量空间找这个变量      
        a=a+b               //修改外变量的临时参数      
        return a            //内函数中用到了外函数的临时变量      
    return inner            //返回内函数的应用      
demo=outer(2)               //demo 等于 内函数的引用      
print( demo(3)  )      
## 5      1
print( demo(4) )      1
## 9
```


 ！！！返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

装饰器
---

假如我们要增强一个函数的功能，但又不希望修改函数的定义，这种在代码运行期间动态增强功能的方式，称之为装饰器(Decorator)。本质上，decorator 就是一个返回函数的高阶函数，而传入参数又是要执行的函数，它在闭包的基础上多进行了几步。

比如，我们现在要在函数调用前后记录时间，然后记录程序的运行时间。我们一般会想到的是在程序前后直接加入计时代码，但是这样往往需要修改函数的定义，所以一般不这样使用。

```
def myfunc():      
    startT=time.time()     #开始时间      
    print("myfunc start.....")      
    time.sleep(1)      
    print("myfunc end....")      
    endT=time.time()       #结束时间      
    msecs=(endT-startT)*1000      
    print("运行程序花费了 %f ms"%msecs)      
myfunc()
```


所以，装饰器就出现了。我们如果要定义一个能记录程序运行时间的装饰器decorator，可以如下定义：

```
from functools import wraps      
def deco(func):      
    @wraps(func)              #不加这个的话 myfunc.__name__ 的值就变成了 wrapper      
    def wrapper():      
        startT=time.time()       #在执行函数的之前做的操作      
        print("执行函数之前")      
        func()      
        print("执行函数之后")      
        endT=time.time()          #在执行函数的之后做的操作      1
        msecs=(endT-startT)*1000      1
        print("运行程序花费了 %f ms"%msecs)      1
    return wrapper      1
@deco                             # 相当于 myfunc=deco(myfunc)      1
def myfunc():      1
    print("myfunc start.....")      1
    time.sleep(1)      1
    print("myfunc end....")       1
myfunc()                  #实际上是在执行 wrapper()函数,也就是 deco(myfunc).wrapper()
```


 相关文章：[Python函数装饰器](http://www.runoob.com/w3cnote/python-func-decorators.html)

### Python中内置的装饰器

@staticmethod : 类静态方法  
   与成员方法的区别是没有self参数，并且可以在类不进行实例化的情况下调用  
@classmethod : 类方法  
   与成员方法的区别在于所接收的第一个参数不是self(类实例的指针)，而是cls (当前类的具体类型)  
@property : 属性方法  
  将一个类方法转变成一个类属性，只读属性

```
class A:      
    def talk():      
        print("talk")       
A.talk()   ###可以执行      
a=A()      
a.talk()  ###不可以执       1
class A:      1
    def talk(self):      1
        print("talk")       1
A.talk()   ###不可以执行      1
a=A()      1
a.talk()  ###可以执       1
class A:      1
    @staticmethod      2
    def talk():      2
        print("talk")       2
A.talk()   ###可以执行      2
a=A()      2
a.talk()  ###可以执行
```


偏函数
---

当函数的参数个数太多，需要简化时，可以使用 functools.partial 创建一个新的函数，这个新的函数可以固定住原函数的部分参数，从而在调用时更简单。这样的新函数叫做 偏函数  
例如 int() 函数可以把字符串转换为整数，当仅传入字符串时，int() 函数默认按十进制转换

```
>>> int('12345')      
12345
```


但int()函数还提供额外的base参数，默认值为10。如果传入base参数，就可以做N进制的转换：

```
>>> int('12345', base=8)      
5349      
>>> int('12345', 16)      
74565
```


当我们要转换大量的二进制字符串时，每次都传入int(x, base=2)非常麻烦，于是我们可以使用 funtools.partial

```
>>> import functools      
>>> int2 = functools.partial(int, base=2)      
>>> int2('1000000')      
64      
>>> int2('1010101')      
85
```


高阶函数
----

### map

map()函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。

```
>>> def f(x):      
...     return x * x      
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])                 ##返回一个Iteraotr      
>>>print ( list(r) )                                                               ## 变成一个list,然后打印出来      
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```


### reduce

[reduce](https://so.csdn.net/so/search?q=reduce&spm=1001.2101.3001.7020)()把一个函数作用在一个序列\[x1, x2, x3, ...\]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，返回一个整形,其效果就是：   reduce(f, \[x1, x2, x3, x4\]) = f(f(f(x1, x2), x3), x4)

```
>>> from functools import reduce      
>>> def fn(x, y):      
...     return x * 10 + y      
...      
>>> reduce(fn, [1, 3, 5, 7, 9])      
13579
```


### filter

Python内建的filter()函数用于过滤序列。和map()类似，filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素，最后返回一个 Iterator  
   
例如，在一个list中，删掉偶数，只保留奇数，可以这么写：

```
>>>def is_odd(n):      
    return n % 2 == 1      
>>>list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))      
# 结果: [1, 5, 9, 15]
```


### sorted

排序也是在程序中经常用到的算法。无论使用冒泡排序还是快速排序，排序的核心是比较两个元素的大小。如果是数字，我们可以直接比较，但如果是字符串或者两个dict呢？直接比较数学上的大小是没有意义的，因此，比较的过程必须通过函数抽象出来。Python内置的sorted()函数可以实现对数据按照指定规则排序，它可以接收一个key函数来实现自定义的排序，还可以接收第三个参数 reverse=True来实现对数据的反向排序。  
例：

```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'])      
['Credit', 'Zoo', 'about', 'bob']
```


默认情况下，对字符串排序，是按照ASCII的大小比较的，由于'Z' < 'a'，结果，大写字母Z会排在小写字母a的前面  
现在，我们提出排序应该忽略大小写，按照字母序排序。要实现这个算法，不必对现有代码大加改动，只要我们能用一个key函数把字符串映射为忽略大小写排序即可。忽略大小写来比较两个字符串，实际上就是先把字符串都变成大写（或者都变成小写），再比较。  
这样，我们给sorted传入key函数，即可实现忽略大小写的排序：

```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)      
['about', 'bob', 'Credit', 'Zoo']
```


要进行反向排序，不必改动key函数，可以传入第三个参数 reverse=True：

```
>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)      
['Zoo', 'Credit', 'bob', 'about']
```


自定义函数
=====

参数
--

Python的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数外，还可以使用默认参数、可变参数和关键字参数，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

### 默认参数

```
def  Max(x , y):      
    if x>y:      
        return x      
    else:      
        return y
```


对于以上的代码，x和y参数还可以默认给出,但是如果要给定默认参数，第一个参数默认给定了的话，后面的参数也必须默认给定，或者就是后面的参数给定，但是第一个参数不给定

```
def  Max(x=3 , y=6)：      
def  Max(x ,   y=10)：      
def  Max(x=10 , y)：          ##这样不行，语法错误
```


定义默认参数要牢记一点：默认参数必须指向不变对象！

### 可变参数

在Python函数中，还可以定义可变参数。顾名思义，可变参数就是传入的参数个数是可变的，可以是1个、2个到任意个，还可以是0个。  
我们以数学题为例子，给定一组数字a，b，c……，请计算a2 + b2 + c2 + ……  
一般做法

```
def calc(numbers):      
    sum=0      
    for n in numbers:      
        sum=sum+n*n      
    return sum      
#在调用的时候，可以组装出一个list或tuple      
calc( [1,2,3] )      
或      
calc( (1,2,3) )
```


可变函数做法

```
def calc(*numbers):           ##定义了一个可变参数，在函数内部，参数numbers接收到的是一个tuple      
    sum=0      
    for n in numbers:      
        sum=sum+n*n      
    return sum      
##  如果已经有一个list或者tuple，要调用一个可变参数怎么办？可以这样做：      
nums=[1,2,3,4,5,6]      
calc( *nums )      
##  *nums表示把nums这个list的所有元素作为可变参数传进去
```


### 关键字参数

关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict

```
def person(name,age,**kw):      
    print('name:' , name , 'age:' , age , 'other:' , kw)      
person('xie' , 30 , city='beijing' , number='123456')      
##   name: xie age: 30 other: {'city': 'beijing', 'number': '123456'}
```


关键字参数有什么用？它可以扩展函数的功能。比如，在person函数里，我们保证能接收到name和age这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。  
和可变函数类似，关键字函数也可以先组装一个dict，然后传进去

```
extra={'city':'beijing','number':'123456'}      
person('xie', 30 , **extra)
```


\*\*extra表示把extra这个dict的所有key-value用关键字参数传入到函数的\*\*kw参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra  
   
如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下

```
def person(name,age,*,city,number):      
       print(name,age,city,number)
```


和关键字参数\*\*kw不同，命名关键字参数需要一个特殊分隔符\*，\*后面的参数被视为命名关键字参数。  
   
在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。  
   
多参数冗余处理，定义参数的时候，多设置用于接收多余参数的  
   
调用函数事，如果参数个数不对或者参数类型不对，pyhton解释器会抛出 TypeError 异常。

返回值
---

函数体内部的语句在执行时，一旦执行到return时，函数就执行完毕，并将结果返回。因此，函数内部通过条件判断和循环可以实现非常复杂的逻辑。  
如果没有return语句，函数执行完毕后也会返回结果，只是结果为None。return None可以简写为return

 
-

文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-9)[基础语法](https://edu.csdn.net/skill/python/python-3-9)[函数](https://edu.csdn.net/skill/python/python-3-9)84785 人正在系统学习中