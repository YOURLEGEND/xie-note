**目录**

[Python库包模块](#t0)

[import 语句](#t1)

[from…import 语句](#t2)

[搜索路径](#t3) 

[PYTHONPATH 变量](#t4)

[命名空间和作用域](#t5)

[查看模块中所有变量和函数，以及查看具体函数的用法](#t6)

[globals() 和 locals() 函数](#t7)

[reload() 函数](#t8)

* * *

Python是一个很强大的脚本语言，最主要的一点是Python具有非常强大的库包模块。

那么，如何导入其他库包模块，以及当我们导入一个库包模块后，如何知道该库包模块怎么使用呢？

### Python库包模块

Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句。

模块让你能够有逻辑地组织你的 Python 代码段。把相关的代码分配到一个模块里能让你的代码更好用，更易懂。模块能定义函数，类和变量，模块里也能包含可执行的代码。

### import 语句

**模块的引入**

模块定义好后，我们可以使用 import 语句来引入模块。

比如要引用模块 requests，就可以在文件最开始的地方用 **import requests** 来引入。在调用 requests 模块中的函数时，必须这样引用：模块名.函数名  ，如：requests.get()

当解释器遇到 import 语句，如果模块在当前的搜索路径就会被导入。

搜索路径是一个解释器会先进行搜索的所有目录的列表。如想要导入模块 requests.py，需要把命令放在脚本的顶端。

一个模块只会被导入一次，不管你执行了多少次import。这样可以防止导入模块被一遍又一遍地执行。

### from…import 语句

Python 的 from 语句让你从模块中导入一个指定的部分到当前命名空间中。语法如下：

```
from modname import name1,name2,name3
```


这个声明不会把整个模块导入到当前的命名空间中，它只会将模块里的指定函数引入到执行这个声明的模块的全局符号表。

把一个模块的所有内容全都导入到当前的命名空间也是可行的，只需使用如下声明，然而这种声明不该被过多地使用。

```
from modname import *
```


### 搜索路径 

当你导入一个模块，Python 解析器对模块位置的搜索顺序是：

*   当前目录
*   如果不在当前目录，Python 则搜索在 shell 变量 PYTHONPATH 下的每个目录。
*   如果都找不到，Python会查看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/。

模块搜索路径存储在 system 模块的 sys.path 变量中。变量里包含当前目录，PYTHONPATH和由安装过程决定的默认目录。

### PYTHONPATH 变量

作为环境变量，PYTHONPATH 由装在一个列表里的许多目录组成。PYTHONPATH 的语法和 shell 变量 PATH 的一样。

在 Windows 系统，典型的 PYTHONPATH 如下：

```
set PYTHONPATH=c:\python27\lib;
```


在 UNIX 系统，典型的 PYTHONPATH 如下：

```
set PYTHONPATH=/usr/local/lib/python
```


### 命名空间和作用域

变量是拥有匹配对象的名字（标识符）。命名空间是一个包含了变量名称们（键）和它们各自相应的对象们（值）的字典。

一个 Python 表达式可以访问局部命名空间和全局命名空间里的变量。如果一个局部变量和一个全局变量重名，则局部变量会覆盖全局变量。

每个函数都有自己的命名空间。类的方法的作用域规则和通常函数的一样。

Python 会智能地猜测一个变量是局部的还是全局的，它假设任何在函数内赋值的变量都是局部的。

因此，如果要给函数内的全局变量赋值，必须使用 global 语句。

global VarName 的表达式会告诉 Python， VarName 是一个全局变量，这样 Python 就不会在局部命名空间里寻找这个变量了。

例如，我们在全局命名空间里定义一个变量 Money。我们再在函数内给变量 Money 赋值，然后 Python 会假定 Money 是一个局部变量。然而，我们并没有在访问前声明一个局部变量 Money，结果就是会出现一个 UnboundLocalError 的错误。取消 global 语句前的注释符就能解决这个问题。

```
#!/usr/bin/python      
# -*- coding: UTF-8 -*-       
Money = 2000      
def AddMoney():      
   # 想改正代码就取消以下注释:      
   # global Money      
   Money = Money + 1       1
print Money      1
AddMoney()      1
print Money
```


### 查看模块中所有变量和函数，以及查看具体函数的用法

dir() 函数返回模块中定义的变量和函数。如下一个简单的实例：

```
import math      
dir(math)
```


![](https://img-blog.csdnimg.cn/20200504221710627.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   特殊字符串变量 \_\_name\_\_ 指向模块的名字
*   特殊字符串变量\_\_doc\_\_说明模块的功能
*   特殊字符串变量\_\_loader\_\_是由加载器在导入的模块上设置的属性，访问它时将会返回加载器对象本身。
*   特殊字符串变量\_\_package\_\_如果存在，相对导入将基于此属性而不是模块\_\_name\_\_属性
*   特殊字符串变量\_\_file\_\_ 指向该模块的导入文件名(如果有的话)。 

![](https://img-blog.csdnimg.cn/2020050422183876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果要查看某个具体函数的使用，比如要查看math模块下 exp 函数的用法，可以在ipython中  math.exp??

![](https://img-blog.csdnimg.cn/20200504222232467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### globals() 和 locals() 函数

根据调用地方的不同，globals() 和 locals() 函数可被用来返回全局和局部命名空间里的名字。

如果在函数内部调用 locals()，返回的是所有能在该函数里访问的命名。

如果在函数内部调用 globals()，返回的是所有在该函数里能访问的全局名字。

两个函数的返回类型都是字典。所以名字们能用 keys() 函数摘取。

### reload() 函数

当一个模块被导入到一个脚本，模块顶层部分的代码只会被执行一次。

因此，如果你想重新执行模块里顶层部分的代码，可以用 reload() 函数。该函数会重新导入之前导入过的模块。语法如下：

```
reload(module_name)
```


在这里，module\_name要直接放模块的名字，而不是一个字符串形式。比如想重载 hello 模块，如下：

```
reload(hello)
```


参考文章：[Python 模块](https://www.runoob.com/python/python-modules.html)

                  [python3所有库列表](https://www.jianshu.com/p/07441ac78776)