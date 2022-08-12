**目录**

[列表](#t0)

[列表中常见的函数](#t1)  

[列表的操作(增删改查)](#t2)

[列表生成式](#t3)

[列表生成器](#t4)

[列表生成器的取值](#t5)

[生成式和生成器的区别](#t6)

[将一个函数变为生成器](#t7)

[列表去重](#t8)

* * *

列表
==

a=\[1,"htll",(5,6)\]     

列表是Python中非常重要的数据类型，通常作为函数的返回类型。

列表中的元素的值可以改变，元素类型可以不同(可以是数字、字符、或者序列)，元素可重复。

列表中的元素可以是元组或其他序列

```
>>b=[(1,2),(3,4),(5,6)]      
>>for x,y in b:      
>>    print (x,y)      
1   2      
3   4      
5   6
```


列表可以相加和乘以一个整数，列表不支持相减、相除和除以一个数 

```
>>a=[1,2,3]      
>>b=[4,5,6]      
>>print(a+b)         //列表的相加      
>>print(a*2)         //列表乘以一个整数      
[1, 2, 3, 4, 5, 6]      
[1, 2, 3, 1, 2, 3]
```


列表中常见的函数  
----------

![](https://img-blog.csdnimg.cn/20181031125113143.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

列表的操作(增删改查)
-----------

```
>>a=[1,'xie',('age',20)]      
>>print(a)      
>>a.append('hello')        //增，在最后加一个元素      
>>print(a)      
>>a.pop(-1)               //删，删除并返回索引为-1的元素      
>>print(a)      
>>a.insert(1,'hello')    //增，在索引为1的元素前加一个元素      
>>print(a)      
>>a.remove('hello')      //删，移除 'hello' 元素      1
>>print(a)      1
>>a[1]='xiao'           //改，修改索引为1的元素      1
>>print(a)      1
>>print(a[1])           //查，查询索引为1的元素      1
[1, 'xie', ('age', 20)]      1
[1, 'xie', ('age', 20), 'hello']      1
[1, 'xie', ('age', 20)]      1
[1, 'hello', 'xie', ('age', 20)]      1
[1, 'xie', ('age', 20)]      1
[1, 'xiao', ('age', 20)]      2
xiao
```


列表生成式
-----

根据已有列表，高效创建新列表的方式。列表生成式是Python迭代机制的一种应用，它常用于实现创建新的列表，因此用在\[ \]中。

语法：\[ expression  for iter\_val in iterable if  cond\_expr \]

```
>>>[i for i in range(10) if i<5]      
[0, 1, 2, 3, 4]
```


列表生成器
-----

通过列表生成式，我们可以直接创建一个新的列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。  
所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的 list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。  
列表生成器是列表生成式的 \[\] 换成 ()，这样就生成了一个  generator生成器

列表生成式：\[ expression  for iter\_val in iterable if  cond\_expr \]

列表生成器：( expression  for iter\_val in iterable if  cond\_expr )

```
>>>[i for i in range(10) if i<5]      
[0, 1, 2, 3, 4]      
>>>(i for i in range(10) if i<5)      
<generator object <genexpr> at 0x000002512EB6D318>
```


### 列表生成器的取值

列表生成器是一个Iterator迭代器，所以可以通过 next() 函数不断取值，直到取到最后没有值了，然后抛出StopIteration错误表示

```
>>a=(i for i in range(10) if i<5)      
>>print(next(a))      
0      
>>print(next(a))      
1      
>>print(next(a))      
2      
>>print(next(a))      
3      1
>>print(next(a))      1
4      1
>>print(next(a))      1
StopIteration
```


因为是Iterator迭代器，所以也是Iterable可迭代对象，所以可以使用for循环取值

```
>>a=(i for i in range(10) if i<5)      
>>for i in a:      
>>    print(i)      
0      
1      
2      
3      
4
```


注: 生成式是一个list，而生成器是一个 generator，list是可以直接就打印出来的，而generator要打印出来，可以使用迭代，因为generator生成器是一个可迭代的对象(Iterable)，也是一个迭代器 (Iterator)。

可以用list( )方法将其转换为 list 列表，然后直接打印出来

```
>>>a=(i for i in range(10) if i<5)      
>>>b=list(a)      
>>>print(b)      
[0, 1, 2, 3, 4]
```


生成式和生成器的区别
----------

*   生成器格式最外层是一个 () ,而列表生成式格式最外层是一个 \[\]
*   生成器是将数据不用一次读取，而列表生成式是一次读取所有数据(耗内存)
*   生成器返回的是一个生成器对象(不能直接循环)，而列表生成式返回的是一个新列表
*   生成式是Iterable可迭代对象，但不是Iterator迭代器
*   生成器既是Iterator迭代器，也是Iterable可迭代对象

将一个函数变为生成器
----------

写一个斐波拉契数列的函数

```
def fib(max):      
    n,a,b=0,0,1      
    while n<max:      
        print(b)      
        a,b=b,a+b      
        n=n+1
```


仔细观察，可以看出，fib函数实际上是定义了斐波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似 generator。  
也就是说，上面的函数和 generator仅一步之遥。要把fib函数变成 generator，只需要把 print(b) 改为 yield b 就可以了  
写一个斐波拉契数列的generator

```
def fib(max):      
    n,a,b=0,0,1      
    while n<max:      
        yield b           //如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator      
        a,b=b,a+b      
        n=n+1
```


最难理解的就是 generator 和函数的执行流程不一样。函数是顺序执行，遇到 return 语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用 next() 的时候执行，遇到 yield 语句返回，再次执行时从上次返回的yield语句处继续执行  
要理解generator的工作原理，它是在for循环的过程中不断计算出下一个元素，并在适当的条件结束for循环。对于函数改成的generator来说，遇到return语句或者执行到函数体最后一行语句，就是结束generator的指令，for循环随之结束

列表去重
----

```
lists=["aa","bb","aa","cc","cc","bb"]      
dict1={}      
lists=list(dict1.fromkeys(lists).keys())      
print(lists)
```


运行结果如下所示  
![](https://img-blog.csdnimg.cn/20200202160903245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Python中的迭代Iterable和迭代器Iterator](https://blog.csdn.net/qq_36119192/article/details/83512573)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-15)[基础语法](https://edu.csdn.net/skill/python/python-3-15)[内置类](https://edu.csdn.net/skill/python/python-3-15)84785 人正在系统学习中