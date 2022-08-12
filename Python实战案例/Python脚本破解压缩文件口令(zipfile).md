> 环境：Windows python版本2.7.15

Python中操作zip压缩文件的模块是 zipfile 。

相关文章：[Python中zipfile压缩文件模块的使用](https://blog.csdn.net/qq_36119192/article/details/83548355)

我们破解压缩文件的口令也是用的暴力破解方法。我们提前准备好[密码字典](https://so.csdn.net/so/search?q=%E5%AF%86%E7%A0%81%E5%AD%97%E5%85%B8&spm=1001.2101.3001.7020)用来爆破，如果密码字典中存在密码，则会打印出该密码，否则提示密码字典中无密码。

main()函数用来打开密码字典 key.txt ，然后读取其中每一行的内容，调用Test()函数去一个个的试密码，如果密码错误，Test函数就会抛出异常，如果密码正确，则不会抛出异常。所以我们在main()函数中以是否接收到异常判断密码是否正确。如果没有接收到异常，说明密码正确！

以下代码是python2.7环境！

```
# -*- coding: utf-8 -*-      
"""      
Created on Thu Nov  1 09:00:26 2018      
@author: 小谢      
"""       
import zipfile      
import os      
def Test(line):      1
    try:      1
        with zipfile.ZipFile("c://users//17250//desktop//test.zip","r") as f:      1
            f.extractall("c://users//17250//desktop//",pwd=line)  #利用密码字典中的密码解压缩      1
    except Exception as e:      1
        return e      1
    finally:      1
        f.close()      1
def main():      1
    try:      1
        with open("c://users//17250//desktop//key.txt") as file:      2
            lines=file.readlines()      2
            for line in lines:      2
                line=line.strip("\n")      2
                e=Test(line)      2
                if e:      2
                    pass      2
                else:      2
                    print("************压缩文件的密码是：%s"%line)      2
                    return line      2
    except Exception as e:      3
        print("异常对象的类型是：%s"%type(e))      3
        print("异常对象的内容是：%s"%e)      3
    finally:      3
        file.close()      3
if __name__=='__main__':      3
    re=main()      3
    if re:      3
        pass      3
    else:      3
        print("对不起，密码字典中未匹配到密码！")
```
