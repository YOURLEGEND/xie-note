hashlib是 python 下一款与[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)相关的库包，提供摘要算法：md5、sha1、sha224、sha256、sha384、sha512、blake2b、blake2s、sha3\_224、sha3\_256、sha3\_384、sha3\_512、shake\_128、shake\_256。摘要算法通过摘要函数（单向函数）对任意长度的数据计算出固定长度的摘要，目的是为了验证原始数据是否被篡改。  
![](https://img-blog.csdnimg.cn/20191024182437126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**MD5函数的使用，其他函数类似**

```
import hashlib      
#方法一      
m=hashlib.md5()      
m.update(b"root")      #提供的字符需要字节类型的      
print(m.digest)        #返回一个btye类型的md5值      
print(m.hexdigest())   #返回一个str类型的md5值      
print("*"*30)      
#方法二      
print(hashlib.md5(b"root").hexdigest())      1
print("*"*30)      1
#方法三：      1
n=hashlib.new("md5")      1
n.update(b"root")      1
print(n.hexdigest())   #返回一个str类型的md5值      1
print("*"*30)      1
#方法四      1
x=hashlib.new("md5",b"root").hexdigest()      1
print(x)
```


![](https://img-blog.csdnimg.cn/20191024224305335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
import hashlib       
print(hashlib.md5(b"root").hexdigest())      
print(hashlib.sha1(b"root").hexdigest())      
print(hashlib.sha224(b"root").hexdigest())      
print(hashlib.sha256(b"root").hexdigest())      
print(hashlib.sha384(b"root").hexdigest())      
print(hashlib.sha512(b"root").hexdigest())      
print(hashlib.sha3_224(b"root").hexdigest())      1
print(hashlib.sha3_256(b"root").hexdigest())      1
print(hashlib.sha3_384(b"root").hexdigest())      1
print(hashlib.sha3_512(b"root").hexdigest())
```


![](https://img-blog.csdnimg.cn/20191025092426117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-94)[其他](https://edu.csdn.net/skill/python/python-3-94)[常用内建模块](https://edu.csdn.net/skill/python/python-3-94)84711 人正在系统学习中