**目录**

[Math模块](#t0)

[Cmath模块](#t1)

* * *

Math模块
------

*   pi                数字常量，圆周率
*   e                 表示一个常量
*   sqrt(x)         求x的平方根
*   fabs(x)         返回x的绝对值
*   factorial(x)    取x的阶乘的值
*   fmod(x,y)     得到x/y的余数，其值是一个浮点数
*   pow(x, y)     返回x的y次方，即x\*\*y
*   isfinite(x)     如果x是正无穷大或负无穷大，则返回True，否则返回False
*   isinf(x)         如果x是正无穷大或负无穷大，则返回True，否则返回False
*   hypot(x)       如果x是不是无穷大的数字,则返回True,否则返回False
*   isnan(x)       如果x不是数字，则返回True，否则返回False
*   ldexp(x)       返回x\*(2\*\*i)的值
*   log(x, a)       返回x的自然对数，以a为基数（不写则默认以e为基数），a参数给定时，将x的对数返回给定的a，计算式为：log(x)/log(a
*   log10(x)       返回x的以10为底的对数
*   log1p(x)       返回x+1的自然对数(基数为e)的值
*   log2(x)         返回x的基2对数
*   modf(x)        返回由x的小数部分和整数部分组成的元组
*   trunc(x)        返回x的整数部分
*   ceil(x)           取大于等于x的最小的整数值，如果x是一个整数，则返回x
*   floor(x)        取小于等于x的最大的整数值，如果x是一个整数，则返回自身
*   radians(x)    把角度x转换成弧度，与degrees 为反函数
*   degrees(x)    把x从弧度转换成角度
*   sin(x)          求x(x为弧度)的正弦值
*   sinh(x)        求x(x为弧度)的双曲正弦值
*   cos(x)          求x的余弦，x必须是弧度
*   tan(x)          返回x(x为弧度)的正切值
*   tanh(x)        返回x(x为弧度)的双曲正切值
*   copysign(x, y)把y的正负号加到x前面，可以使用0
*   exp(x)        返回math.e,也就是2.71828的x次方
*   expm1(x)    返回math.e的x(其值为2.71828)次方的值减１
*   frexp(x)        返回一个元组(m,e),其计算方式为：x分别除0.5和1,得到一个值的范围
*   fsum(x)        对迭代器里的每个元素进行求和操作
*   gcd(x,y)          返回x和y的最大公约数

```
import math      
print(math.pi)      
print(math.e)      
print(math.sqrt(100))      
print(math.fabs(-5.5))      
print(math.factorial(5))      
print(math.pow(10,2))      
print(math.fmod(5,2)      
###############################      1
3.141592653589793      1
2.718281828459045      1
10.0      1
5.5      1
120      1
100.0      1
1.0
```


Cmath模块
-------

对于复数，Python 支持它的加减乘除运算，同时提供了 cmath 模块对其他复杂运算进行支持。cmath 模块和 Python 中的 math 模块对应， math提供对于实数的支持， 在这里主要讨论 cmath 模块中的几个函数的用法。

1）极坐标和笛卡尔坐标表示的转换  
C==c.real+c.imag\*j 的复数表示方法为复数的笛卡尔表示法， cmath 模块中的 polar() 方法和 rect() 方法可以对复数进行极坐标表示和笛卡尔表示方法的转换。 例：

```
>>> import cmath      
>>> Z=1+2j      
>>> print cmath.polar(Z)      
(2.23606797749979, 1.1071487177940904)      
>>> a,b=cmath.polar(Z)      
>>> print cmath.rect(a,b)      
(1+2j)
```


polar 函数对一个输入的笛卡尔形势的复数进行计算，输出为一个二元组，第一个值为Z的模值， 第二个为幅度值。 rect() 函数对输入的模和幅度值进行计算输出笛卡尔表示。

如果需要单独对一个复数进行幅度值的求解，可以调用 cmath.phrase(x) 函数，返回幅度值。

2）复数的幂指数与对数函数  
复数的指数函数为 cmath.exp(x), 用来求解 e^x 表达式。

cmath.log(x\[,base\]) 用来求以 Base 为底的 x 的对数。

cmath.log10(x) 用来求以 10 为底 x 的对数

cmath.sqrt(x) 用来求 x 的平方根。

3）复数的三角函数方程  
包括所有的三角函数计算 acos(x) asin(x) atan(x) sin(x) cos(x) tan(x)。

4）参数类判断  
cmath.isinf(x) 如果x的实部或者虚部为无穷大，则返回true。

cmath.isnan(x)如果x的实部或者虚步不是数字则返回true。

5）常量支持  
cmat.pi 浮点值， 表示圆周率的大小

cmat.e 浮点值， 表示自然对数的底