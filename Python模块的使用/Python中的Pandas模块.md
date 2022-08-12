**目录**

[Pandas](#t0)

[Series](#t1)

[序列的创建](#t2)

[序列的读取](#t3) 

[DataFrame](#t4)

[DataFrame的创建](#t5) 

[DataFrame数据的读取](#t6)

[Panel](#t7)

[Panel的创建](#t8) 

* * *

Pandas
------

**Pandas** ( Python Data Analysis Library )是基于[NumPy](https://so.csdn.net/so/search?q=NumPy&spm=1001.2101.3001.7020) 的一种工具，该工具是为了解决数据分析任务而创建的。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。pandas提供了大量能使我们快速便捷地处理数据的函数和方法。你很快就会发现，它是使Python成为强大而高效的数据分析环境的重要因素之一  
   
python中有三种数据结构

1.  Series：一维数组，与Numpy中的一维array类似。二者与Python基本的数据结构List也很相近，其区别是：List中的元素可以是不同的数据类型，而array和Series中则只允许存储相同的数据类型，这样可以更有效的使用内存，提高运算效率。Time- Series：以时间为索引的Series。
2.  DataFrame：二维的表格型数据结构。很多功能与R中的data.frame类似。可以将DataFrame理解为Series的容器。DataFrame是用的最多的数据结构
3.  Panel ：三维的数组，可以理解为DataFrame的容器。

[pandas](https://so.csdn.net/so/search?q=pandas&spm=1001.2101.3001.7020)可以读取很多种的数据，用的比较多的是读取 htm、json、csv的数据

```
import pandas       
data1=pandas.read_html('1.html')   #读取html格式数据      
data2=pandas.read_json('2.json')   #读取json格式数据      
data3=pandas.read_csv('3.csv')     #读取csv格式的数据
```


Series
------

系列([Series](https://so.csdn.net/so/search?q=Series&spm=1001.2101.3001.7020))是能够保存任何类型的数据(整数，字符串，浮点数，Python对象等)的一维标记数组。轴标签统称为索引

Series的创建函数：pandas.Series( data, index, dtype,copy )

<table border="1" cellpadding="1" cellspacing="1" style="width:700px;"><tbody><tr><td>参数</td><td>描述</td></tr><tr><td>data</td><td>数据采取各种形式，如：ndarray，list，constants</td></tr><tr><td>index</td><td>索引值必须是唯一的和散列的，与数据的长度相同。默认&nbsp;np.arange(n)&nbsp;如果没有索引被传递</td></tr><tr><td>dtype</td><td>dtype&nbsp;用户数据类型。如果没有，将推断数据类型</td></tr><tr><td>copy</td><td>复制数据，默认为&nbsp;false</td></tr></tbody></table>

### 序列的创建

创建一个空series序列

![](https://img-blog.csdn.net/20181014162300233?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

从字典创建一个series序列

![](https://img-blog.csdn.net/20181014162832883?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 序列的读取 

读取直接用 \['行名'\]，序列只可以读取行的内容

![](https://img-blog.csdn.net/20181014163328886?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

DataFrame
---------

数据帧([DataFrame](https://so.csdn.net/so/search?q=DataFrame&spm=1001.2101.3001.7020))是二维数据结构，即数据以行和列的表格方式排列。  
数据帧(DataFrame)的功能特点：

*   潜在的列是不同的类型
*   大小可变
*   标记轴(行和列)
*   可以对行和列执行算术运算

DataFrame的创建函数：pandas.DataFrame( data, index, columns, dtype, copy)

<table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td>参数</td><td>描述</td></tr><tr><td>data</td><td>数据采取各种形式，如：ndarray，series，map，lists，dict，constant和另一个DataFrame</td></tr><tr><td>index</td><td>对于行标签，要用于结果帧的索引是可选缺省值 np.arrange(n) ，如果没有传递索引值</td></tr><tr><td>columns</td><td>对于列标签，可选的默认语法是 np.arange(n)&nbsp;这只有在没有索引传递的情况下才是这样</td></tr><tr><td>dtype</td><td>每列的数据类型</td></tr><tr><td>copy</td><td>如果默认值为false，则此命令用于复制数据</td></tr></tbody></table>

### DataFrame的创建 

创建一个空DataFrame序列

![](https://img-blog.csdn.net/20181014170141494?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

从字典创建一个series序列(必须加index) 

![](https://img-blog.csdn.net/20181014170505848?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### DataFrame数据的读取

读取列，直接 \['列名'\]

![](https://img-blog.csdn.net/20181015223042106?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

读取行 

![](https://img-blog.csdn.net/2018101522320076?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/2018101522325191?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Panel
-----

面板(Panel)是3D容器的数据。面板数据一词来源于计量经济学，部分源于名称：Pandas - pan(el)-da(ta)-s。  
3轴(axis)这个名称旨在给出描述涉及面板数据的操作的一些语义。它们是 

*   items - axis 0，每个项目对应于内部包含的数据帧(DataFrame)
*   major\_axis - axis 1，它是每个数据帧(DataFrame)的索引(行)
*   minor\_axis - axis 2，它是每个数据帧(DataFrame)的列

Panel的创建函数：pandas.Panel(data, items, major\_axis, minor\_axis, dtype, copy)

| 参数 | 说明 |
| --- | --- |
| data | 数据采取各种形式，如：ndarray，series，map，lists，dict，constant和另一个数据帧 DataFrame |
| items | axis=0 |
| major\_axis | axis=1 |
| minor\_axis | axis=2 |
| dtype | 每列的数据类型 |
| copy | 复制数据，默认 false |

### Panel的创建 

创建一个空Panel序列

![](https://img-blog.csdn.net/20181014171219552?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-204)[结构化数据分析工具Pandas](https://edu.csdn.net/skill/python/python-3-204)[数据结构](https://edu.csdn.net/skill/python/python-3-204)84711 人正在系统学习中