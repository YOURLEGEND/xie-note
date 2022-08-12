当我们在运行python程序出错时，我们需要定位到出错的位置。有时候通过程序运行时的报错可以很容易的找到出错的位置，但是有时候必须得通过调试程序才能找出我们的错误。

PyCharm中要调试程序的话，在代码前面单击设置断点。

![](https://img-blog.csdnimg.cn/20181112105821588.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后 **运行-->调试** 。或者按 **alt+shift+F9**  进入调式模式。

控制台可以实时的看我们程序的输出。

![](https://img-blog.csdnimg.cn/20181112110147799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**F8是单步步过，F7是单步步入**

比如我们现在要执行一个函数，按F8的话，就会直接执行完该函数。而如果按F7的话，就会跟踪到函数内部去，如果函数内部调用了其他函数或模块，则又会跟踪到其他的函数或者模块当中。

当我们执行完某一行代码后，代码后面会有变量的值

![](https://img-blog.csdnimg.cn/20181112110320151.png)

当我们要停止调式的话，可以直接按 控制台左边的按钮，或者按 **ctrl+F2**

![](https://img-blog.csdnimg.cn/20181112110555415.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-4)[预备知识](https://edu.csdn.net/skill/python/python-3-4)[常用开发工具](https://edu.csdn.net/skill/python/python-3-4)84785 人正在系统学习中