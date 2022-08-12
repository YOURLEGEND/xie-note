**目录**

[异常处理](#t0)

 [Python中常见的异常](#t1)

* * *

### 异常处理

在Python当中，若一个程序在运行的时候出错，Python解释器会自动的在出错的地方生成一个异常对象，而后Python解释器会自动的在出错地方的附近寻找有没有对这个异常对象处理的代码，所谓[异常处理](https://so.csdn.net/so/search?q=%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86&spm=1001.2101.3001.7020)代码就是 try……except 语句。如果没有，Python解释器会自动的将这个异常对象抛给其调用函数，就这样层层抛出，如果在 main 当中也没有对这个异常对象处理的代码，Python解释器（实际上是操作系统）最后会做一个简单粗暴的处理，将整个程序给终止掉，并将错误的信息在显示屏上输出。

异常是 python 对象，表示一个错误，当 Python 脚本发生异常时我们需要捕获处理它，否则程序会终止执行。

try的工作原理是：当开始一个 try 语句后，python就在当前程序的上下文中作标记，这样当异常出现时就可以回到这里，try子句先执行，接下来会发生什么依赖于执行时是否出现异常。

*   如果当 try 中的语句执行时发生异常，python就跳回到try并执行第一个匹配该异常的except子句，异常处理完毕，控制流就通过整个try语句（除非在处理异常时又引发新的异常）。
*   如果在 try 中的语句里发生了异常，却没有匹配的except子句，异常将被抛出到上层，如果上层也没有处理语句，再抛出到上层，就这样一层层抛出，如果在 main 当中也没有对异常处理的语句，程序将终止执行并报错。
*   如果在try子句执行时没有发生异常，python将执行else语句后的语句（如果有的话)，然后执行 finally 语句(如果有的话)。然后控制流通过整个try语句。

```
try:      
    可能出现异常的代码块      
except IOError:                    #捕获特定的异常      
    print("IO异常")      
except Exception as e:             #捕获所有异常信息并别名为e      
    print("异常的类型是:%s" % type(e))      
    print("异常的内容是:%s" % e)      
else:      
    print('如果代码块不抛出异常会执行此行代码!')      1
finally:      1
    print('不管代码块是否抛出异常都会执行此行代码!')
```


例如，打开一个文件，并且打印出其内容。正常情况下，如果文件路径正确的话，会打印出该文件的内容。如果路径错误，或者文件不存在的话，就会抛出异常。

```
try:      
    f=open('c://users//17250//desktop//test.txt','r')      
except Exception as e:      
    print("异常的类型是:%s"%type(e))      
    print("产生异常：%s"%e)      
else:      
    print(f.read())      
finally:      
    f.close()       1
#如果文件路径错误，或者文件不存在，抛出的异常      1
异常的类型是:<class 'FileNotFoundError'>      1
产生异常：[Errno 2] No such file or directory: 'c://users//17250//deskop//test.txt'
```


我们也可以自己手动触发异常，使用 raise 语句。

```
try:      
    if True:      
        raise Exception("异常")      
except Exception as e:      
    print("异常对象的类型是:%s"%type(e))      
    print("异常对象的内容是：%s"%e)      
###################################      
异常对象的类型是:<class 'Exception'>      
异常对象的内容是：异常
```


###  Python中常见的异常

1.  AttributeError 试图访问一个对象没有的树形，比如foo.x，但是foo没有属性x
2.  IOError 输入/输出异常；基本上是无法打开文件
3.  ImportError 无法引入模块或包；基本上是路径问题或名称错误
4.  IndentationError 语法错误（的子类） ；代码没有正确对齐
5.  IndexError 下标索引超出序列边界，比如当x只有三个元素，却试图访问x\[5\]
6.  KeyError 试图访问字典里不存在的键
7.  KeyboardInterrupt Ctrl+C被按下
8.  NameError 使用一个还未被赋予对象的变量
9.  SyntaxError Python代码非法，代码不能编译(个人认为这是语法错误，写错了）
10.  TypeError 传入对象类型与要求的不符合
11.  UnboundLocalError 试图访问一个还未被设置的局部变量，基本上是由于另有一个同名的全局变量，导致你以为正在访问它
12.  ValueError 传入一个调用者不期望的值，即使值的类型是正确的

文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-194)[其他](https://edu.csdn.net/skill/python/python-3-194)[异常处理及程序调试](https://edu.csdn.net/skill/python/python-3-194)84785 人正在系统学习中