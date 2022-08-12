PIL/Pillow
----------

**PIL**(Python Image Library)是Python的第三方[图像处理](https://so.csdn.net/so/search?q=%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86&spm=1001.2101.3001.7020)库，功能强大，使用人数众多。PIL历史悠久，原来是只支持 python2.x 的版本，后来出现了移植到python3的库**Pillow，**其功能和PIL差不多，但是支持python3。

PIL和Pillow的调用都是 import PIL。所以，以下Pillow称为PIL，因为我的环境是python3.7

PIL可以做很多和图像处理相关的事情:

*   图像归档(Image Archives)**：**PIL非常适合于图像归档以及图像的批处理任务。你可以使用PIL创建缩略图，转换图像格式，打印图像等等。
*   图像展示(Image Display)**：**PIL较新的版本支持包括Tk PhotoImage，BitmapImage还有Windows DIB等接口。PIL支持众多的GUI框架接口，可以用于图像展示。
*   图像处理(Image Processing)**：**PIL包括了基础的图像处理函数，包括对点的处理，使用众多的卷积核(convolution kernels)做过滤(filter)，还有颜色空间的转换。PIL库同样支持图像的大小转换，图像旋转，以及任意的仿射变换。PIL还有一些直方图的方法，允许你展示图像的一些统计特性。这个可以用来实现图像的自动对比度增强，还有全局的统计分析等。

PIL中有很多类，比较常用的有下面几个：

### Image

```
from PIL import Image      
im=Image.open("1.jpg")  #打开一张图片       
#图片的属性      
print(im.mode)   #图片的模式，如RGB      
print(im.size)   #图片的尺寸      
print(im.format) #图片的格式      
print(im.info)   #图片的信息      
print(im.histogram())   #图片的颜色直方图       1
#图片的操作      1
im.rotate(45).show()    #将图片向左旋转45度并显示      1
im.copy().show()        #将图片复制并且显示      1
im=im.convert("P")      #将图片模式变为8位像素模式，即灰度模式      1
im.save("test.png")     #保存为test.png图片
```


未完待续。。。

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-25)[进阶语法](https://edu.csdn.net/skill/python/python-3-25)[常用标准库](https://edu.csdn.net/skill/python/python-3-25)84711 人正在系统学习中