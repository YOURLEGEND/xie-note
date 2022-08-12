**目录**

[Matplotlib](#t0)

[pyplot类](#t1)

[pyplot.plot()](#t2)

[配置属性](#t3)

[pyplot.subplot()](#t4)

* * *

Matplotlib
==========

[Matplotlib](https://so.csdn.net/so/search?q=Matplotlib&spm=1001.2101.3001.7020) 是 Python 2D 绘图领域使用最广泛的套件。它能让使用者很轻松地将数据图形化，并且提供多样化的输出格式。

pyplot类
-------

matplotlib.pyplot类提供一个类似[matlab](https://so.csdn.net/so/search?q=matlab&spm=1001.2101.3001.7020)的绘图框架

### pyplot.plot()

> **`matplotlib.pyplot.``plot`**( _x , y ,' color  marker  line'_)

对于样式 'color   marker  line' ，每一个都是可选的。如果没有提供，则使用样式周期的值。例外情况：如果`line`是给的，但不是`marker`，数据将是一条没有标记的行。

**color颜色**

![](https://img-blog.csdnimg.cn/20181105221134547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**marker标记**

![](https://img-blog.csdnimg.cn/20181105221237102.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**line线条样式**

![](https://img-blog.csdnimg.cn/20181105221254414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
import numpy as np      
from matplotlib import pyplot as plt      
x=np.array([1,2,3,4])      
y=np.array([1,2,3,4])      
z=np.array([2,4,6,8])      
i=np.array([5,6,7,8])      
plt.plot(x,y,'r.')  #x轴 y轴 样式      
plt.plot(x,z,'g*--',lw=5)  #x轴 y轴 样式 宽度      
plt.plot(x,i,'bD:')  #x轴 y轴 样式      1
plt.show()
```


 ![](https://img-blog.csdnimg.cn/20181105221452464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**画正弦余弦函数**

```
#画正弦函数      
import numpy as np      
import matplotlib.pyplot as plt          #画图的库      
x=np.arange(-6,6,0.1) #第一个参数是X轴的start 第二个参数是X轴的end      
y=np.sin(x)      
z=np.cos(x)      
plt.plot(x,y)      
plt.plot(x,z)   #不设置颜色时，系统默认设置      
plt.show()
```


![](https://img-blog.csdnimg.cn/20181105152750287.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 配置属性

因为是面向对象的绘图库，我们可以为每个对象配置它们的属性，有三种方法可以配置：

1.  一是通过对象的方法 set\_属性名（）函数
2.  二是通过对象的 set() 函数
3.  三是通过 pylot 模块提供的 setp() 函数

```
import matplotlib.pyplot as plt      
plt.figure()      
line=plt.plot(range(2))[0]    # plot函数返回的是一个列表,因为可以同时画多条线      
line.set_color('r')          #设置颜色      
line.set_linewidth(10)       #设置宽度      
plt.show()       
########################################################       1
import matplotlib.pyplot as plt      1
plt.figure()      1
line=plt.plot(range(2))[0]      1
line.set(color='r',linewidth=10)      1
plt.show()       1
########################################################       1
plt.figure()      1
line=plt.plot(range(2))[0]      2
plt.setp(line,color='r',linewidth=10)      2
plt.show()
```


以上三种设置绘图属性都是下面同一种效果 

 ![](https://img-blog.csdnimg.cn/20181105155753506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### pyplot.subplot()

plt.subplot作用是把一个绘图区域（可以理解成画布）分成多个小区域，用来绘制多个子图。

> **`matplotlib.pyplot.``subplot`**(nrows, ncols, plot\_number）

nrows和ncols表示将画布分成（nrows\*ncols）个小区域，每个小区域可以单独绘制图形；plot\_number表示将图绘制在第plot\_number个子区域。

举例： plt1 = subplot(222)，

表示画布分成（2\*2=4）个小区域，并将图 plt1 绘制在画布中的第二个子区域，也就是右上角位置。

```
import numpy as np      
from matplotlib import pyplot as plt      
x=[1,2,3]      
y=[1,2,3]      
z=[3,2,1]      
a=np.arange(-2*np.pi,2*np.pi,0.1)      
b=np.sin(a)      
c=np.cos(a)      
plt1=plt.subplot(221)    #将画布划分成2*2的区域 ，我们画第1个区域      1
plt1.plot(x,y,'b')       #画第一个图      1
plt2=plt.subplot(222)    #将画布划分成2*2的区域 ，我们画第2个区域      1
plt2.plot(x,z,'r--')    #画第二个图      1
plt3=plt.subplot(223)   #将画布划分成2*2的区域 ，我们画第3个区域      1
plt3.plot(a,b,'c:')     #画第三个图      1
plt4=plt.subplot(224)   #将画布划分成2*2的区域 ，我们画第4个区域      1
plt4.plot(a,c,'y--')    #画第四个图      1
plt.show()
```


![](https://img-blog.csdnimg.cn/20181105220818757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-210)[绘图库Matplotlib](https://edu.csdn.net/skill/python/python-3-210)[风格和样式](https://edu.csdn.net/skill/python/python-3-210)84711 人正在系统学习中