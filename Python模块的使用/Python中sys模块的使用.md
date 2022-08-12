**目录**

[sys模块](#t0)

[sys.argv()](#t1)

[sys.exit(0)](#t2)

[sys.path](#t3)

[sys.modules](#t4)

* * *

sys模块负责程序与python解释器的交互，提供了一系列的函数和变量，用于操控python的运行时环境。

sys模块
-----

### **sys.argv()**

该方法实现从程序外部向程序传递参数 ，sys.argv 变量是一个包含了[命令行](https://so.csdn.net/so/search?q=%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020)参数的字符串列表,，利用命令行向程序传递参数。 其中，脚本的名称总是 sys.argv 列表的第一个参数，也就是sys.argv\[0\]。

```
import sys      
print(sys.argv)      
print(sys.argv[0])      
print(sys.argv[1])      
print(type(sys.argv))       
def Test(num):      
    num=int(num)      
    print(num**2)      1
if __name__=='__main__':      1
    Test(sys.argv[1])       1
我们执行该文件:  python  test.py  10      1
['test.py','10']      1
test.py      1
10      1
<class 'list'>      1
100      1
可以看到，sys.argv[0]永远是模块的名字，从sys.argv[1]开始就是我们输入的参数
```


### sys.exit(0)

功能：执行到主程序末尾，解释器自动退出，但是如果需要中途退出程序，可以调用 sys.exit() 函数，带有一个可选的整数参数返回给调用它的程序，表示你可以在主程序中捕获对 sys.exit 的调用。（0是正常退出，其他为异常）

### sys.path

功能：获取指定模块搜索路径的[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)集合，可以将写好的模块放在得到的某个路径下，就可以在程序中import时正确找到。也就是环境变量。返回一个列表

```
import sys      
print(sys.path)
```


### sys.modules

功能：`sys.modules`是一个全局字典，该字典是python启动后就加载在内存中。每当程序员导入新的模块，`sys.modules`将自动记录该模块。当第二次再导入该模块时，python会直接到字典中查找，从而加快了程序运行的速度。它拥有字典所拥有的一切方法

### sys.stdin、sys.stdout、sys.stderr

stdin、stdout 和 stderr 在Python中都是文件属性对象, 他们在python启动时自动与shell环境中的标准输入, 输出, 出错相关. 而python程序在shell中的I/O重定向是由shell来提供的,与python本身没有关系。python程序内部将stdin, stdout, stderr读写操作重定向到一个内部对象.

```
import sys      
sys.stdout.write("hello,word!")      
#################      
hello,word!
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-25)[进阶语法](https://edu.csdn.net/skill/python/python-3-25)[常用标准库](https://edu.csdn.net/skill/python/python-3-25)84711 人正在系统学习中