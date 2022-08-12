在学习之前，我们先了解[OCR](https://so.csdn.net/so/search?q=OCR&spm=1001.2101.3001.7020)、tesseract、tesserocr、pytesseract和opencv这几个跟图片处理有关的库。

**OCR(**Optical Character Recognition)光学字符识别，是指通过扫描字符，然后通过其形状将其翻译成电子文本的过程。

**tesseract**是一个OCR，在Windows、Linux和Mac OS下均可安装。

**tesserocr** 和 **pytesseract** 是对tesseract封装的一个Python库，可以用来识别图片中的字符。pytesseract是Google的Tesseract-OCR引擎包装器。所以在使用tesserocr和pytesseract之前，我们需要安装tesseract。至于怎么安装我就不说了，网上一搜一大把。

**opencv**是一个基于BSD许可（开源）发行的跨平台计算机视觉库，也可以用于图片处理。

既然是破解图形验证码，我们就得先了解一下图片的有关知识。

绝大部分图片是 RGB 类型的，即是用RGB(红绿蓝)三原色组成的图片。在图像处理中，用RGB三个分量（R：Red，G：Green，B：Blue），即红、绿、蓝三原色来表示真彩色，R分量，G分量，B分量的取值范围均为0~255，比如电脑屏幕上的一个红色的像素点的三个分量的值分别为：255，0，0

### 像素点

![](https://img-blog.csdnimg.cn/20190111095705697.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们的图片是由一个一个的像素点组成的，像素是图像的最小单元，我们右键——>属性去查看图片的信息，看到图片的尺寸1024\*1024

![](https://img-blog.csdnimg.cn/20190111095626733.png)

即宽度是1024个像素，高度也是1024个像素，也就是说这张图片是由一个1024 \* 1024的像素点矩阵构成的，这个矩阵是1024行，1024列，共有1024 \* 1024 = 1048576个像素点。

因为一个像素点的颜色是由RGB三个值来表现的，所以一个像素点矩阵对应三个颜色向量矩阵，分别是R矩阵，G矩阵，B矩阵，它们也都是1024 \*1024大小的矩阵。我们可以理解为这张图片是由3个1024\*1024的矩阵重和起来的，才形成了这张图片最后的效果。

![](https://img-blog.csdnimg.cn/20190111100941940.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下：

这个是R矩阵中的一部分  
[![图像处理的灰度化和二值化](https://imgconvert.csdnimg.cn/aHR0cDovL3M1LnNpbmFpbWcuY24vbXc2OTAvMDA1TmVta0F6eTc3MGVXS1JhazQ0JjY5MA?x-oss-process=image/format,png)](http://photo.blog.sina.com.cn/showpic.html#blogid=13c6397540102wqtt&url=http://album.sina.com.cn/pic/005NemkAzy770eWKRak44)

这个是G矩阵中的一部分  
[![图像处理的灰度化和二值化](https://imgconvert.csdnimg.cn/aHR0cDovL3M0LnNpbmFpbWcuY24vbXc2OTAvMDA1TmVta0F6eTc3MGZzM2VNMzIzJjY5MA?x-oss-process=image/format,png)](http://photo.blog.sina.com.cn/showpic.html#blogid=13c6397540102wqtt&url=http://album.sina.com.cn/pic/005NemkAzy770fs3eM323)

这个是B矩阵中的一部分

[![图像处理的灰度化和二值化](https://imgconvert.csdnimg.cn/aHR0cDovL3MxNS5zaW5haW1nLmNuL213NjkwLzAwNU5lbWtBenk3NzBmelRMajA2ZSY2OTA?x-oss-process=image/format,png)](http://photo.blog.sina.com.cn/showpic.html#blogid=13c6397540102wqtt&url=http://album.sina.com.cn/pic/005NemkAzy770fzTLj06e)  
比如每个矩阵的第一行第一列的值分别为：R：240，G：223，B：204，所以这个像素点的颜色就是（240,223,204）。如果图片某个地方显示的是红色，那么这个地方的像素值就是：R：255  G：0 B：0   (255,0,0) 。

### 图片的灰度化

在理解了一张图片是由一个像素点矩阵构成之后，我们就知道我们对图像的处理就是对这个像素点矩阵的操作，想要改变某个像素点的颜色，我们只要在这个像素点矩阵中找到这个像素点的位置，比如第x行，第y列，所以这个像素点在这个像素点矩阵中的位置就可以表示成（x，y）,因为一个像素点的颜色由红、绿、蓝三个颜色变量表示，所以我们通过给这三个变量赋值，来改变这个像素点的颜色，比如改成红色（255，0，0），可以表示为（x，y，（R=255，G=0，B=0））x行y列的像素值为(255,0,0)。

      那么什么叫图片的灰度化呢？其实很简单，就是让像素点矩阵中的每一个像素点都满足下面的关系：R=G=B（就是红色变量的值，绿色变量的值，和蓝色变量的值，这三个值相等），此时的这个值叫做灰度值。

一般灰度处理经常使用下面几种方法来进行处理。

**方法1 平均值法：**

*   灰度化后的R=（处理前的R + 处理前的G +处理前的B）/ 3
*   灰度化后的G=（处理前的R + 处理前的G +处理前的B）/ 3
*   灰度化后的B=（处理前的R + 处理前的G +处理前的B）/ 3

**方法2 加权平均法(处理效果最好)：**

*   灰度化后的R =  处理前的R \* 0.3+ 处理前的G \* 0.59 +处理前的B \* 0.11
*   灰度化后的G =  处理前的R \* 0.3+ 处理前的G \* 0.59 +处理前的B \* 0.11
*   灰度化后的B =  处理前的R \* 0.3+ 处理前的G \* 0.59 +处理前的B \* 0.11

**方法3 最大值发：**

   灰度化后的R、G、B=处理前的R、G、B中的最大值

[https://blog.csdn.net/L0\_o\_0f/article/details/80180111](https://blog.csdn.net/L0_o_0f/article/details/80180111)

如下是利用python将图片进行灰度化处理后的效果，直接利用的PIL库Image类的方法(应该用的是方法2来进行灰度化)

```
from PIL import Image      
img=Image.open("1.jpg")      
img.convert("L").show()  #将图片进行灰度化处理并且显示
```


![](https://img-blog.csdnimg.cn/2019011110273774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 图片的二值化

什么叫图像的二值化？二值化就是让图像的像素点矩阵中的每个像素点的灰度值为0（黑色）或者255（白色），也就是让整个图像呈现只有黑和白的效果。在灰度化的图像中灰度值的范围为0~255，在二值化后的图像中的灰度值范围是0或者255。

*   黑色：二值化后的R = 二值化后的G = 二值化后的B =  0
*   白色：二值化后的R = 二值化后的G = 二值化后的B =  255

那么一个像素点在灰度化之后的灰度值怎么转化为 0 或者255呢？比如灰度值为120，那么在二值化后到底是0还是255?

这就涉及到取一个阀值的问题。

常用的二值化方法：

**方法1：**

          取阀值为127（相当于0~255的中数），让灰度值小于等于127的变为0（黑色），灰度值大于127的变为255（白色），这样做的好处是计算量小速度快，但是缺点也是很明显的，因为这个阀值在不同的图片中均为127。但是不同的图片，他们的颜色 分布差别很大，所以用127做阀值，效果肯定是不好的。

**方法2：**

          取阀值为计算像素点矩阵中的所有像素点的灰度值的平均值

        （像素点1灰度值+...+像素点n灰度值）/ n = 像素点平均值avg

         然后让每一个像素点与像素点平均值比较，小于等于avg的像素点就为0（黑色），大于avg的 为255（白色）

**方法3：**

          使用直方图方法（也叫双峰法）来寻找阀值，直方图是图像的重要特质。直方图方法认为图像由前景和背景组成，在灰度直方图上，前景和背景都形成高峰，在双峰之间的最低谷处就是阀值所在。取到阀值之后再一 一比较就可以了。

下面的代码是我们自己写的二值化的处理，我们取得阈值为127。

```
# 图片二值化      
from PIL import Image      
img = Image.open('1.jpg')      
Img = img.convert('L')   #灰度化处理       
threshold = 127           #我们自己定义的阈值      
table = []      
for i in range(256):      
    if i < threshold:      1
        table.append(0)      1
    else:      1
        table.append(1)      1
# 图片二值化      1
photo = Img.point(table, '1')      1
photo.save("test.jpg")  #得到二值化处理后图片test.jpg      1
Image.open("test.jpg").show()   #显示二值化处理后的图片
```


![](https://img-blog.csdnimg.cn/2019011111312531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下面是利用[opencv](https://so.csdn.net/so/search?q=opencv&spm=1001.2101.3001.7020)库里面的方法来进行二值化处理

```
import cv2 as cv      
import numpy as np      
#全局阈值      
def threshold_demo(image):      
    gray = cv.cvtColor(image, cv.COLOR_RGB2GRAY)  #把输入图像灰度化      
    #直接阈值化是对输入的单通道矩阵逐像素进行阈值分割。      
    ret, binary = cv.threshold(gray, 0, 255, cv.THRESH_BINARY | cv.THRESH_TRIANGLE)      
    print("threshold value %s"%ret)      
    cv.namedWindow("binary0", cv.WINDOW_NORMAL)      1
    cv.imshow("binary0", binary)       1
#局部阈值      1
def local_threshold(image):      1
    gray = cv.cvtColor(image, cv.COLOR_RGB2GRAY)  #把输入图像灰度化      1
    #自适应阈值化能够根据图像不同区域亮度分布，改变阈值      1
    binary =  cv.adaptiveThreshold(gray, 255, cv.ADAPTIVE_THRESH_GAUSSIAN_C,cv.THRESH_BINARY, 25, 10)      1
    cv.namedWindow("binary1", cv.WINDOW_NORMAL)      1
    cv.imshow("binary1", binary)       2
#用户自己计算阈值      2
def custom_threshold(image):      2
    gray = cv.cvtColor(image, cv.COLOR_RGB2GRAY)  #把输入图像灰度化      2
    h, w =gray.shape[:2]      2
    m = np.reshape(gray, [1,w*h])      2
    mean = m.sum()/(w*h)  #阈值取平均值      2
    ret, binary =  cv.threshold(gray, mean, 255, cv.THRESH_BINARY)      2
    cv.namedWindow("binary2", cv.WINDOW_NORMAL)      2
    cv.imshow("binary2", binary)       3
src = cv.imread('1.jpg')      3
cv.namedWindow('input_image', cv.WINDOW_NORMAL)   #设置为WINDOW_NORMAL可以任意缩放      3
cv.imshow('input_image', src)      3
threshold_demo(src)      3
local_threshold(src)      3
custom_threshold(src)      3
cv.waitKey(0)      3
cv.destroyAllWindows()
```


![](https://img-blog.csdnimg.cn/20190111113654450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 图形验证码破解

未完待续。。。

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-158)[网络爬虫](https://edu.csdn.net/skill/python/python-3-158)[验证码处理](https://edu.csdn.net/skill/python/python-3-158)84853 人正在系统学习中