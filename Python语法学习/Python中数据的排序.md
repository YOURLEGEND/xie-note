**目录**

[列表的排序](#t0)

[sort(key,reverse)方法](#t1) 

[sorted(target,key,reverse) 函数](#t2)

[元组tuple的排序](#t3)

[sort(key,reverse)方法](#t4)

[sorted(target,key,reverse)函数](#t5) 

[字符串的排序](#t6)

[字典的排序](#t7)

* * *

列表的排序
-----

列表的排序可以使用列表自带的 sort()  方法，也可以使用 sorted() 函数

sort() 方法是修改原列表；而 sorted() 函数是对列表的复制再排序，返回一个新的列表，两者有区别。

![](https://img-blog.csdnimg.cn/20181031095215984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### sort(key,reverse)方法 

对于列表中的元素是数字的话，默认是按照大小排序

```
>>a=[3,1,6,2,-4,7]      
>>a.sort()                    //从小到大      
>>print(a)      
[-4, 1, 2, 3, 6, 7]      
>>a.sort(reverse=Ture)        //倒序，从大到小      
>>print(a)      
[7, 6, 3, 2, 1, -4]       
还可以传入参数 key，key参数是对要排序的数据的操作      1
>>a.sort(key=lambda x:abs(x))      1
>>print(a)      1
[1, 2, 3, -4, 6, 7]
```


对于列表中元素是[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)的话，默认是按照 元素第一个字母的ASCII 值来排序 

```
>>a=['day','Back','alice','Change']      
>>a.sort()                     //默认是安装第一个字母的ASCII值排序      
>>print(a)      
['Back', 'Change', 'alice', 'day']      
>>a.sort(key=str.lower)        //不区分第一个字母的大小写排序      
>>print(a)      
['alice', 'Back', 'Change', 'day']
```


### sorted(target,key,reverse) 函数

对于列表中的元素是数字的话，默认是按照大小排序

```
>>a=[3,1,6,2,-4,7]      
>>b=sorted(a)      
>>print(b)      
[-4, 1, 2, 3, 6, 7]           //从小到大      
>>c=sorted(a,reverse=True)       
[7, 6, 3, 2, 1, -4]           //倒序，从大到小      
>>d=sorted(a,key=lambda x:abs(x))      
[1, 2, 3, -4, 6, 7]
```


对于列表中元素是字符串的话，默认是按照 元素第一个字母的ASCII 值来排序  

```
>>a=['day','Back','alice','Change']      
>>b=sorted(a)                   //默认是安装第一个字母的ASCII值排序      
>>print(b)      
['Back', 'Change', 'alice', 'day']        
>>c=sorted(a,key=str.lower)    //不区分第一个字母的大小写排序      
>>print(c)      
['alice', 'Back', 'Change', 'day']
```


元组tuple的排序
----------

### sort(key,reverse)方法

[元组](https://so.csdn.net/so/search?q=%E5%85%83%E7%BB%84&spm=1001.2101.3001.7020)自身没有 sort() 排序方法，要对元组用 sort() 进行排序，我们可以先将元组转换成list 列表，然后排序，最后再转换成元组。

```
>>a=(3,1,6,2,-4,7)      
>>b=list(a)      
>>b.sort()      //也可以传入key和reverse参数      
>>a=tuple(b)      
>>print(a)      
(-4, 1, 2, 3, 6, 7)
```


### sorted(target,key,reverse)函数 

```
>>a=(3,1,6,2,-4,7)      
>>b=sorted(a)        //也可以传入key和reverse参数      
>>print(b)      
[-4, 1, 2, 3, 6, 7]
```


字符串的排序
------

python中的字符串类型是不允许直接修改元素的。必须先把要排序的字符串放在容器里，如 list 。然后再利用 list 的 sort() 方法排序

```
>>a="hello,word!"      
>>b=list(a)      
>>b.sort()      
>>print(b)      
['!', ',', 'd', 'e', 'h', 'l', 'l', 'o', 'o', 'r', 'w']      
>>c="".join(b)      
>>print(c)      
!,dehlloorw       1
或者直接一行搞定      1
>>a="hello,word!"      1
>>s="".join((lambda x:(x.sort(),x)[1])(list(s)))      1
>>print(s)      1
!,dehlloorw       1
或者利用 sorted() 函数      1
>>a="hello,word!"      1
>>b=sorted(a)      1
>>print(b,type(b))      2
['!', ',', 'd', 'e', 'h', 'l', 'l', 'o', 'o', 'r', 'w'] <class 'list'>      2
>>c="".join(b)      2
>>print(c)      2
!,dehlloorw
```


 字典的排序
------

对字典进行排序？这其实是一个伪命题，搞清楚python字典的定义---字典本身默认以key的字符顺序输出显示---就像我们用的真实的字典一样，按照abcd字母的顺序排列，并且本质上各自没有先后关系，是一个哈希表的结构：

但实际应用中我们确实有这种排序的“需求”-----安装key值首字母的ASCII值排序，按照values的值排序，或者按照别的奇怪的顺序进行输出，我们只需要把字典转化成 lis t或者 tuple，把字典每一对键值转化为list中的两位子list或者子tuple再输出，就可以达到我们的目的

```
import operator      
stu={'name':'xie','age':"20",'id':"40",'sex':'man'}       
##安装key值升序进行排列      
a=dict(sorted(stu.items(),key=operator.itemgetter(0),reverse=False))      
##安装key值降序进行排列      
b=dict(sorted(stu.items(),key=operator.itemgetter(0),reverse=True))      
##安装value值升序进行排列      
c=dict(sorted(stu.items(),key=operator.itemgetter(1),reverse=False))      1
##安装value键值降序进行排列      1
d=dict(sorted(stu.items(),key=operator.itemgetter(1),reverse=True))      1
print(stu)      1
print(a)      1
print(b)      1
print(c)      1
print(d)      1
##############################################################      1
{'name': 'xie', 'age': '20', 'id': '40', 'sex': 'man'}      1
{'age': '20', 'id': '40', 'name': 'xie', 'sex': 'man'}      2
{'sex': 'man', 'name': 'xie', 'id': '40', 'age': '20'}      2
{'age': '20', 'id': '40', 'sex': 'man', 'name': 'xie'}      2
{'name': 'xie', 'sex': 'man', 'id': '40', 'age': '20'}
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-198)[科学计算基础软件包NumPy](https://edu.csdn.net/skill/python/python-3-198)[操作数组](https://edu.csdn.net/skill/python/python-3-198)84785 人正在系统学习中