python中，使用 hashlib 库对[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)进行加密 

```
import hashlib       
string = input("请输入要加密的字符串：")      
a=string.encode("utf-8")               #对字符串进行utf-8编码       
print('MD5:',hashlib.md5(a).hexdigest())      
print('SHA1:',hashlib.sha1(a).hexdigest())      
print('SHA224:',hashlib.sha224(a).hexdigest())      
print('SHA256:',hashlib.sha256(a).hexdigest())      1
print('SHA384:',hashlib.sha384(a).hexdigest())      1
print('SHA512:',hashlib.sha512(a).hexdigest())      1
print('SHA3_224:',hashlib.sha3_224(a).hexdigest())      1
print('SHA3_256:',hashlib.sha3_256(a).hexdigest())      1
print('SHA3_384:',hashlib.sha3_384(a).hexdigest())      1
print('SHA3_512:',hashlib.sha3_512(a).hexdigest())
```


运行结果如下： 

![](https://img-blog.csdnimg.cn/20200623144455755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-25)[进阶语法](https://edu.csdn.net/skill/python/python-3-25)[常用标准库](https://edu.csdn.net/skill/python/python-3-25)84711 人正在系统学习中