**目录**

[NumPy](#t0)

[ndarray对象](#t1)

[Numpy数据类型](#t2)

[Numpy数组属性](#t3)

* * *

NumPy
-----

[NumPy](https://so.csdn.net/so/search?q=NumPy&spm=1001.2101.3001.7020)(Numerical Python) 是 Python 的一个扩展程序库，支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。Nupmy可用来存储和处理大型矩阵，比Python自身的嵌套列表（nested list structure)结构要高效的多（该结构也可以用来表示矩阵（matrix））。据说NumPy将Python相当于变成一种免费的更强大的MatLab系统。

NumPy 是一个运行速度非常快的数学库，主要用于数组计算，包含：

*   一个强大的 N 维数组对象 ndarray
*   广播功能函数
*   整合 C/C++/Fortran 代码的工具
*   线性代数、傅里叶变换、随机数生成等功能

### ndarray对象

NumPy 最重要的一个对象是其 N 维数组对象 ndarray，它是一系列同类型数据的集合，可以使用基于 0 的索引访问集合中的项目。

ndarray 对象是用于存放同类型元素的多维数组。ndarray中的每个元素在内存中使用相同大小的块。 ndarray中的每个元素是数据类型对象的对象(称为 dtype)

> numpy.array( object ,  dtype = None , ndmin = 0 ,copy = True , order = None ,  subok = False )

 一般只有 object 、dtype和 ndmin 参数常用，其他参数不常用 

![](https://img-blog.csdnimg.cn/20181105142135974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
import numpy      
a=numpy.array([1,2,3])                #一维      
b=numpy.array([[1,2,3],[4,5,6]])      #二维      
c=numpy.array([1,2,3],dtype=complex)  #元素类型为复数      
d=numpy.array([1,2,3],ndmin=2)        #二维      
print(a,type(a))      
print(b,type(b))      
print(c,type(c))      
print(d,type(d))      1
####################################      1
[1 2 3] <class 'numpy.ndarray'>      1
[[1 2 3]      1
 [4 5 6]] <class 'numpy.ndarray'>      1
[1.+0.j 2.+0.j 3.+0.j] <class 'numpy.ndarray'      1
[[1 2 3]] <class 'numpy.ndarray'>
```


### [Numpy数据类型](http://www.runoob.com/numpy/numpy-dtype.html)

### Numpy数组属性

NumPy 数组的维数称为秩（rank），一维数组的秩为 1，二维数组的秩为 2，以此类推。

在 NumPy中，每一个线性的数组称为是一个轴（axis），也就是[维度](https://so.csdn.net/so/search?q=%E7%BB%B4%E5%BA%A6&spm=1001.2101.3001.7020)（dimensions）。比如说，二维数组相当于是两个一维数组，其中第一个一维数组中每个元素又是一个一维数组。所以一维数组就是 NumPy 中的轴（axis），第一个轴相当于是底层数组，第二个轴是底层数组里的数组。而轴的数量——秩，就是数组的维数。

很多时候可以声明 axis。axis=0，表示沿着第 0 轴进行操作，即对每一列进行操作；axis=1，表示沿着第1轴进行操作，即对每一行进行操作。

 ndarray 对象属性有：

![](https://img-blog.csdnimg.cn/20181105142400357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**常见的属性有下面几种 ：**

**ndarray.shape** ：  这一数组属性返回一个包含数组纬度的元组，它也可以用于调整数组大小 

```
import numpy as np      
a=np.array([[1,2,3],[4,5,6]])      
print(a.shape)           #打印shape属性      
a.shape=(3,2)            #修改shape属性      
print(a)      
#######################################      
(2, 3)      
[[1 2]      
 [3 4]      1
 [5 6]]
```


 **ndarray.ndim**： 这一数组属性返回数组的维数

```
import numpy as np      
a=np.arange(24)     #np.arange返回0-23的列表类型的数据      
print(a.ndim)      
b=a.reshape(2,3,4)      
print(b)      
print(b.ndim)      
############################      
1      
[[[ 0  1  2  3]      1
  [ 4  5  6  7]      1
  [ 8  9 10 11]]       1
 [[12 13 14 15]      1
  [16 17 18 19]      1
  [20 21 22 23]]]      1
3
```


**ndarray.itemsize** ： 这一数组属性返回数组中每个元素的字节单位长度

```
import numpy as np      
a=np.array([1,2,3])  #默认是四个字节      
print(a.itemsize)      
#########################################      
4
```


### numpy.mat()函数的用法 

该函数用来创建[矩阵](https://so.csdn.net/so/search?q=%E7%9F%A9%E9%98%B5&spm=1001.2101.3001.7020)

```
import numpy as np       
#将列表转为矩阵      
a=[7,8,9]      
b=np.mat(a)      
print(b)      
print("#"*10)       
#创建一行的矩阵      1
m=np.mat([1,2,3])         1
print(m)      1
print("打印出第一行的第三个值：",m[0,2])  		#取第一行的第三个值      1
print("#"*10)       1
x=np.mat([[3,2,1],[6,5,4]])      1
print(x)      1
print("打印出矩阵的第二行：",x[1])      1
print("打印出矩阵的第二行：",x[1,:])      2
print("打印出矩阵的行列数：",x.shape)  	#获得矩阵的行列数      2
print("打印出矩阵的行数  ：",x.shape[0])   #获得矩阵的行数      2
print("打印出矩阵的列数  ：",x.shape[1])   #获得矩阵的列数      2
x.sort()               #对矩阵的每一行进行排序      2
print("对矩阵的每一行进行排序:")      2
print(x)      2
print("#"*10)
```


 ![](https://img-blog.csdnimg.cn/2020092713431549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### numpy.zeros()函数的用法 

该函数用于返回给定形状和类型的新数组。返回的数据类型为 numpy.ndarray，具有给定形状，类型和顺序的0的数组。

```
numpy.zeros(shape，dtype=float，order = 'C')
```


**参数：**

*   shape：int 或 int 的元组。新阵列的形状，例如：（2，3）或2。
*   dtype：数据类型，可选。、例如numpy.int8。默认是numpy.float64
*   order：{'C'，'F'}，可选，默认：'C' 。是否在内容中以行（C）或列（F）顺序存储多维数据。

```
import numpy as np       
a=np.zeros(5)      
print(a,type(a))       
b=np.zeros([1,2],dtype='int8')      
print(b)       
c=np.zeros([1,2,3],dtype='int8')      1
print(c)
```


 ![](https://img-blog.csdnimg.cn/20200927140043680.png)

相关文章：[NumPy教程](http://www.runoob.com/numpy/numpy-tutorial.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-195)[科学计算基础软件包NumPy](https://edu.csdn.net/skill/python/python-3-195)[NumPy概述](https://edu.csdn.net/skill/python/python-3-195)84711 人正在系统学习中