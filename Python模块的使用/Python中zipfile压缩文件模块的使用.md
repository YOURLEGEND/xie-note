**目录**

[zipfile](#t0)

[压缩一个文件](#t1)

[解压文件](#t2)

[高级应用](#t3)

[利用 zipfile 模块破解压缩文件口令：Python脚本破解压缩文件口令](https://blog.csdn.net/qq_36119192/article/details/83615327)

* * *

zipfile
-------

Python 中 zipfile 模块提供了对 zip 压缩文件的一系列操作。

```
f=zipfile.ZipFile("test.zip",mode="")    //解压是 r , 压缩是 w  追加压缩是 a
```


mode的几种：

*   解压：r
*   压缩：w
*   追加压缩：a 

### 压缩一个文件

创建一个压缩文件 test.zip（如果test.zip文件不存在） ，然后将 test.txt 文件加入到压缩文件 test.zip 中，如果原来的压缩文件中有内容，会清除原有的内容

```
import zipfile      
try:      
    with zipfile.ZipFile("c://users//17250//desktop//test.zip",mode="w") as f:      
        f.write("c://users//17250//desktop//test.txt")                    #写入压缩文件，会把压缩文件中的原有覆盖      
except Exception as e:      
        print("异常对象的类型是:%s"%type(e))      
        print("异常对象的内容是:%s"%e)      
finally:      
        f.close()
```


如果要压缩的文件的路径是 c://users//17250//desktop//test.txt 这样的话，

那么最后压缩文件里面压缩的就是  users//17250//desktop//test.txt  文件了

![](https://img-blog.csdnimg.cn/20181101084045515.png)

向已存在的压缩文件中追加内容

```
import zipfile      
try:      
    with zipfile.ZipFile("c://users//17250//desktop//test.zip",mode="a") as f:      
        f.write("e://test.txt")                    #追加写入压缩文件      
except Exception as e:      
        print("异常对象的类型是:%s"%type(e))      
        print("异常对象的内容是:%s"%e)      
finally:      
        f.close()
```


虽然原文件里面压缩的文件的路径是 users//17250//desktop//test.txt  ，但是追加进去的是 e://test2.txt 文件，那么test2.txt 文件压缩是在 users 那一级的目录。

![](https://img-blog.csdnimg.cn/20181101084027728.png)

### 解压文件

将test.zip文件解压

在python3中，解压文件的密码参数 pwd 接收的是二进制的值，所以要在前面加一个 **b** 。python2中接受的是str字符串的值。

```
import zipfile      
try:      
    with zipfile.ZipFile("c://users//17250//desktop//test.zip",mode="a") as f:      
         f.extractall("c://users//17250//desktop//",pwd=b"root")  ##将文件解压到指定目录，解压密码为root      
except Exception as e:      
         print("异常对象的类型是:%s"%type(e))      
         print("异常对象的内容是:%s"%e)      
finally:      
         f.close()
```


### 高级应用

zipfile.is\_zipfile(filename)   
判断一个文件是不是压缩文件   
ZipFile.namelist()   
返回文件列表 

```
if zipfile.is_zipfile('test.zip'): #is_zipfile() 判断是否似zip文件      
   f = zipfile.ZipFile('test.zip')      
   files = f.namelist() #namelist() 返回zip压缩包中的所有文件      
   print(files)      
   f.close()
```


### 利用 zipfile 模块破解压缩文件口令：[Python脚本破解压缩文件口令](https://blog.csdn.net/qq_36119192/article/details/83615327)