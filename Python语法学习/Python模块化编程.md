**目录**

[模块化](#t0)

[自定义模块](#t1)

[模块的内置属性](#t2)

[导入模块](#t3)

[安装第三方模块](#t4) 

[查看模块的属性和方法](#%E6%9F%A5%E7%9C%8B%E6%A8%A1%E5%9D%97%E7%9A%84%E5%B1%9E%E6%80%A7%E5%92%8C%E6%96%B9%E6%B3%95)

* * *

模块化
===

在Python中，一个.py文件就称之为一个模块（Module）,为了避免模块名冲突，Python又引入了按目录来组织模块的方法，称为包（Package）。举个例子，一个abc.py的文件就是一个名字叫 abc 的模块，一个 xyz.py 的文件就是一个名字叫xyz的模块.现在，假设我们的abc和xyz这两个模块名字与其他模块冲突了，于是我们可以通过包来组织模块，避免冲突。选择一个顶层包名，比如mycompany，按照如下目录存放：  
mycompany  
├─ \_\_init\_\_.py  
├─ abc.py  
└─ xyz.py  
引入了包以后，只要顶层的包名不与别人冲突，那所有模块都不会与别人冲突。现在，abc.py 模块的名字就变成了mycompany.abc，类似的，xyz.py的模块名变成了mycompany.xyz。  
请注意，每一个包目录下面都会有一个\_\_init\_\_.py的文件，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。\_\_init\_\_.py可以是空文件，也可以有Python代码，因为 \_\_init\_\_.py本身就是一个模块，而它的模块名就是mycompany

自定义模块
-----

我们可以自己写一个模块，但是注意模块命名的时候要注意以下几点：

*   模块名要遵循Python变量命名规范，不要使用中文、特殊字符
*   模块名不要和系统模块名冲突，最好先查看系统是否已存在该模块，检查方法是在Python交互环境执行 import abc，若成功则说明系统存在此模块

我们现在自己写了一个模块，circles.py

```
pi=3.14159      
def area(radius):      
    return pi*(radius**2)      
def circumference(radius):      
    return 2*pi*radius      
def sphereSurface(radius):      
    return 4.0*area(radius)      
def sphereVolume(redius):      
    return (4.0/3.0)*pi*(radius**3)       1
if __name__=='__main__':              ##测试运行代码      1
    print(area(3))
```


 这里我们要提到 \_\_name\_\_ 属性。每个模块都有 \_\_name\_\_ 属性。如果我们是在本模块运行的话，\_\_name\_\_属性的值为\_\_main\_\_，如果是其他模块导入该模块的话，该模块的\_\_name\_\_属性值为包名。

所以我们这里判断了\_\_name\_\_=='\_\_main\_\_'，如果相等的话，就测试运行代码。当其他模块导入我们模块的话，这里面的测试代码不会执行

### 模块的内置属性

*   \_\_doc\_\_：模块中用于描述的文档字符串
*   \_\_name\_\_：模块名
*   \_\_file\_\_：模块保存的路径

```
"""      
Created on Wed Oct 31 19:46:54 2018      
@author: 小谢      
"""      
print(__doc__)      
print(__file__)      
print(__name__)      
###############      
Created on Wed Oct 31 19:46:54 2018      1
@author: 小谢       1
G:/Python3/untitled26.py      1
__main__
```


导入模块
----

假如我们现在要在其他模块导入我们自己写的模块的话，可以有下面几种方法。我们得把我们的包放在python能找到的环境变量的路径下面。然后就可以导入了

```
import circles    这样我们用这个模块里面的函数的话，就得这样： circles.area()、circles.circumference()、circles.sphereVolume()       
from circles import area    这样我们要用area方法的话就直接使用：area()，但是其他几个方法不能用       
from circles import area,circumference   这样是导入多个方法       
from circles import *   这样是导入这个模块内的所有方法       
给模块设置别名      1
import circles as xie   那么我们在调用的时候就可以直接使用这个别名了： xie.area()
```


安装第三方模块 
--------

我们可以直接安装 anaconda，这个程序里面默认安装了很多常用的python包，如果还是没有安装的话，我们可以打开CMD命令窗口：pip  install   package 

### 查看模块的属性和方法

dir(模块名)

```
import circles      
print(dir(circles))      
################      
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'area', 'circumference', 'pi', 'sphereSurface', 'sphereVolume']
```
