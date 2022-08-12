**目录**

[Counter](#t0)

[most\_common](#t1) 

* * *

对于序列如[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)str、列表list和tuple可以统计里面数据出现的次数。我们使用的是 collections 模块。

collections模块的常用方法有：

*   计数器(Counter)
*   双向队列(deque)
*   默认字典(defaultdict)
*   有序字典(OrderedDict)
*   可命名元组(namedtuple)

使用以上类型时需要导入模块 from collections import \*

### Counter

Counter()方法对传入的序列中出现的数据进行汇总，返回一个<class 'collections.Counter'>的对象

```
from collections import Counter      
a="aabcac"      
b=['a','a','b','c','a','c']      
c=('a','a','b','c','a','c')      
print(Counter(a),type(Counter(a)))      
print(Counter(b))      
print(Counter(c))      
#######################################      
Counter({'a': 3, 'c': 2, 'b': 1}) <class 'collections.Counter'>      1
Counter({'a': 3, 'c': 2, 'b': 1})      1
Counter({'a': 3, 'c': 2, 'b': 1})
```


### most\_common 

most\_common方法对Counter()汇总的数据进行从高到低的排序，返回前 n 个元素的字典，返回的是列表型的数据

```
from collections import Counter      
a="aabcac"      
b=['a','a','b','c','a','c']      
c=('a','a','b','c','a','c')      
print(Counter(a))      
print(Counter(b))      
print(Counter(c))       
print(Counter(a).most_common(3),type(Counter(a).most_common(3)))      1
print(Counter(b).most_common(2))      1
print(Counter(c).most_common(1))      1
##############################################      1
Counter({'a': 3, 'c': 2, 'b': 1})      1
Counter({'a': 3, 'c': 2, 'b': 1})      1
Counter({'a': 3, 'c': 2, 'b': 1})      1
[('a', 3), ('c', 2), ('b', 1)] <class 'list'>      1
[('a', 3), ('c', 2)]      1
[('a', 3)]
```
