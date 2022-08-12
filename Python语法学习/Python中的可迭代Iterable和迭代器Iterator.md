**目录**

[Iterable可迭代对象](#t0)

[如何判断对象是否是可迭代对象Iterable](#t1)

[Iterator迭代器](#t2)

[如何判断对象是否迭代器Iterator](#t3)

[将Iterable转换成Iterator](#t4)

* * *

Iterable可迭代对象
-------------

如果给定一个 list列表 或 tuple元组 ，我们可以通过 for 循环来[遍历](https://so.csdn.net/so/search?q=%E9%81%8D%E5%8E%86&spm=1001.2101.3001.7020)这个 list 或 tuple ，这种遍历我们称为迭代（Iteration），可以直接作用于 for 循环的对象统称为 可迭代对象。当我们使用 for 循环时，只要作用于一个可迭代对象，for循环就可以正常运行，而我们不太关心该对象究竟是 list 还是其他数据类型。

```
a=[1,2,3,4,5]      
b=(6,7,8,9,10)      
for i in a:      
    print(i)      
for i in b:      
    print(i)
```


### 如何判断对象是否是可迭代对象Iterable

那么，如何判断一个对象是可迭代对象呢？可以通过 collections 模块的 Iterable 类的 isinstance()方法判断：

```
from collections import Iterable      
>>isinstance(123,Iterable)               #判断整数是否是可迭代对象      
False      
>>isinstance('abc',Iterable)             #判断字符串是否是可迭代对象      
True      
>>isinstance([1,2,3],Iterable)           #判断列表是否是可迭代对象      
True      
>>isinstance((1,2,3),Iterable)           #判断元组是否是可迭代对象      
True      1
>>isinstance({'name':'xie','age':10},Iterable)   #判断字典是否是可迭代对象      1
True
```


Iterator迭代器
-----------

凡是可作用于for循环的对象都是 Iterable(可迭代) 类型；  
凡是可作用于next()函数的对象都是 Iterator(迭代器) 类型，它们表示一个惰性计算的序列；

[Iterator](https://so.csdn.net/so/search?q=Iterator&spm=1001.2101.3001.7020)迭代器一定是Iterable可迭代的，但是Iterable可迭代对象不一定是Iterator迭代器

常见类型如str、list、tuple、dict等是 Iterable 但不是 Iterator 

### 如何判断对象是否迭代器Iterator

那么，如何判断一个对象是可迭代对象呢？可以通过 collections 模块的 Iterator 类的isinstance()方法判断：

```
from collections import Iterator      
>>isinstance('abc',Iterator)             #判断字符串是否是迭代器      
False      
>>isinstance([1,2,3],Iterator)           #判断列表是否是迭代器      
False      
>>isinstance((1,2,3),Iterator)           #判断元组是否是迭代器      
False      
>>isinstance({'name':'xie','age':10},Iterator)   #判断字典是否是迭代器      
False
```


你可能会问，为什么list、dict、tuple、str等数据类型不是Iterator？  
这是因为Python的 Iterator 对象表示的是一个数据流，Iterator 对象可以被 next() 函数调用并不断返回下一个数据，直到没有数据时抛出 StopIteration 错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以 Iterator 的计算是惰性的，只有在需要返回下一个数据时它才会计算。  
Iterator 甚至可以表示一个无限大的数据流，例如全体自然数。而使用list、dict、tuple和str是永远不可能存储全体自然数的

### 将Iterable转换成Iterator

可以利用 iter( ) 函数将 Iterable 转换成 Iterator 对象

iter(Iterable)--->Iterator

```
from collections import Iterator      
>>isinstance(iter('abc'),Iterator)             #判断字符串是否是迭代器      
True      
>>isinstance(iter([1,2,3]),Iterator)           #判断列表是否是迭代器      
True      
>>isinstance(iter((1,2,3)),Iterator)           #判断元组是否是迭代器      
True      
>>isinstance(iter({'name':'xie','age':10}),Iterator)   #判断字典是否是迭代器      
True
```


  
  
   
   
 

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-24)[进阶语法](https://edu.csdn.net/skill/python/python-3-24)[文件](https://edu.csdn.net/skill/python/python-3-24)84785 人正在系统学习中