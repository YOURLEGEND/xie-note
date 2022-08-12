**目录**

[字典](#t0)

[字典中常见的函数](#t1)

[字典的访问](#t2)

[字典中 键值key 的遍历](#t3)

[字典中 值Value 的遍历](#t4) 

[字典中键值和数据的遍历](#t5)

[字典的操作(增删改查)](#t6)

[字典的排序](https://blog.csdn.net/qq_36119192/article/details/83574638#%C2%A0%E5%AD%97%E5%85%B8%E7%9A%84%E6%8E%92%E5%BA%8F)

* * *

字典
==

a={  'name':'xie' ,  'age':20 ,  3:'c'  }

**字典**是 Python 重要的数据类型，字典是由“键—值”对组成的[集合](https://so.csdn.net/so/search?q=%E9%9B%86%E5%90%88&spm=1001.2101.3001.7020)，字典中的“值”通过“键”来引用。 字典也称为关联数组、映射或散列表。

Python字典利用了“散列”方法，使用专门的散列函数完成，即字典中的每个键都被转换为一个数字—散列值。字典中值存储在一个底层列表中，并用散列值作为索引。访问值时，将提供的键转为散列值，再跳到列表的相应位置。 使用“键”来访问字典值效率极高。另外与列表一样，字典也是可以改变的：可以添加、删除或修改“键—值”对

注意，对于字典的键有两个限制：

*   字典中的键必须独一无二，即在同一个字典中，任何两个键都不能相同，如果有相同的键，则前面的键对应的值会删掉，替换成后面的键对应的值
*   键必须是不可变的。因此，字典键不能是 列表、字典和集合，可以是数字、字符串、元组

```
>>a={'name':'xie',5:6,(1,2):'ha','name':'wang'}      
>>print(a)      
{'name': 'wang', 5: 6, (1, 2): 'ha'}      
>>for i in a:      
>>    print(i,type(i))      
name <class 'str'>      
5 <class 'int'>      
(1, 2) <class 'tuple'>
```


字典中常见的函数
--------

![](https://img-blog.csdnimg.cn/20181031154343735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

字典的访问
-----

字典的访问与元组、列表有所不同，元组和列表是通过数字索引获取对应的值， 而字典是通过key值获取相应的value值。 

```
>>a={'name':'xie','sex':'man','age':20}      
>>print(a['name'])      
xie      
>>print(a.keys())      
>>print(a.values())      
dict_keys(['name', 'sex', 'age'])    //type类型是 dict_keys      
dict_values(['xie', 'man', 20])      //type类型是 dict_values
```


字典中 键值key 的遍历
-------------

```
>>>a={'name':'xie','age':20,3:'c'}      
>>>for i in a:      
>>>    print(i)      
name      
age      
3
```


字典中 值Value 的遍历 
---------------

```
>>a={'name':'xie','sex':'man','age':20}      
>>for i in a:      
>>    print(a[i])      
xie      
man      
20
```


字典中键值和数据的遍历
-----------

```
>>>a={'name':'xie','age':20,3:'c'}      
>>>b=a.items()      
>>>for i in b:      
>>>   print(i)      
('name', 'xie')         //type类型是元组      
('age', 20)      
(3, 'c')       
>>>for key,value in b:      1
>>>   print(key,value)      1
name xie      1
age 20      1
3 c
```


字典的操作(增删改查)
-----------

```
>>dict={'a':'apple','b':'banana','g':'grape','o':'orange'}      
>>print(dict)      
>>dict['w']='water'         //增      
>>print(dict)       
>>del(dict['a'])            //删      
>>print(dict)      
>>dict['g']='grapefruit'    //改      
>>print(dict)      
>>print(dict['g'])          //查      1
{'a': 'apple', 'b': 'banana', 'g': 'grape', 'o': 'orange'}      1
{'a': 'apple', 'b': 'banana', 'g': 'grape', 'o': 'orange', 'w': 'water'}      1
{'b': 'banana', 'g': 'grape', 'o': 'orange', 'w': 'water'}      1
{'b': 'banana', 'g': 'grapefruit', 'o': 'orange', 'w': 'water'}      1
grapefruit
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-15)[基础语法](https://edu.csdn.net/skill/python/python-3-15)[内置类](https://edu.csdn.net/skill/python/python-3-15)84785 人正在系统学习中