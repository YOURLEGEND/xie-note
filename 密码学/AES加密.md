**目录**

[AES](#t0)

[node.js实现AES加密](#t1)

[CBC模式](#t2)

[ECB模式](#t3)

[Python实现AES加密](#t4)

[CBC模式](#CBC%E6%A8%A1%E5%BC%8F)

[ECB模式](#ECB%E6%A8%A1%E5%BC%8F)

* * *

在渗透测试过程中，经常会碰到[AES加密](https://so.csdn.net/so/search?q=AES%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)算法，所以趁着有时间学习下AES加密算法。

AES
===

     AES(Advanced Encryption Standars)高级[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)标准，又称 Rijndael 加密法，是美国联邦政府采用的一种区块加密标准。AES是用来替换DES的，已经被广泛应用于各行各业。AES由美国国家标准与技术研究院 （NIST）于2001年11月26日发布于FIPS PUB 197，并在2002年5月26日成为有效的标准。2006年，高级加密标准已然成为对称密钥加密中最流行的算法之一。

     AES算法为比利时密码学家Joan Daemen和Vincent Rijmen所设计，结合两位作者的名字，以Rijdael之名命之，投稿高级加密标准的甄选流程。（Rijdael的发音近于 "Rhine doll"。）

    AES是对称加密算法。AES算法基于排列和置换运算。排列是对数据重新进行安排，置换是将一个数据单元替换为另一个。AES使用几种不同的方法来执行排列和置换运算。AES是一个迭代的、对称[密钥](https://so.csdn.net/so/search?q=%E5%AF%86%E9%92%A5&spm=1001.2101.3001.7020)分组的密码。AES 加密有 AES-128、AES-192、AES-256 三种。分别对应三种密钥长度 128bits（16字节）、192bits（24字节）、256bits（32字节）。当然，密钥越长，安全性越高，加解密花费时间也越长。默认的是AES-128，其安全性完全够用。与公共密钥加密使用密钥对不同，对称密钥密码使用相同的密钥加密和解密数据。通过分组密码返回的加密数据的位数与输入数据相同。迭代加密使用一个循环结构，在该循环中重复置换和替换输入数据。

![](https://img-blog.csdnimg.cn/2020101923225789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 AES是基于数据块的加密方式，也就是说，每次处理的数据是一块（16字节），当数据不是16字节的倍数时会进行填充，这就是所谓的分组密码（区别于基于比特位的流密码），16字节是分组长度。

在用脚本实现AES加密算法过程中，有几个重要的参数：

*   秘钥key：有三种长度，分别是 16字节、24字节、32字节
*   偏移量iv：ECB模式不需要偏移量iv。偏移量的取值，至少 16 位，或者为 16 的倍数。
*   填充模式：有五个，分别为**Pkcs7、Iso97971、AnsiX923、Iso10126、ZeroPadding**。其中Pkcs7是默认的填充模式。
*   加密模式：有五个，分别为：ECB、CBC、CTR、CFB、 OFB

AES的加密模式有五个：

*   **电码本模式**（ECB：Electronic Codebook Book)：是一种基础的加密方式，密文被分割成分组长度相等的块（不足补齐），然后单独一个个加密，一个个输出组成密文。该模式不需要偏移量iv
*   **密码分组链接模式**（CBC：Cipher Block Chaining)：是一种循环模式，前一个分组的密文和当前分组的明文异或操作后再加密，这样做的目的是增强破解难度。
*   **计算器模式**（CTR：Counter）；
*   **密码反馈模式**（CFB：Cipher FeedBack）：实际上是一种反馈模式，目的也是增强破解的难度
*   **输出反馈模式**（OFB：Output FeedBack）：实际上是一种反馈模式，目的也是增强破解的难度

其中 ECB 和 CBC 模式用的比较多，并且 ECB 模式不需要偏移量iv。

node.js实现AES加密
--------------

node.js实现AES需要安装 crypto-js 库，安装方法：cnpm install  crypto-js

![](https://img-blog.csdnimg.cn/20200626011116147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### CBC模式

```
var CryptoJS = require("crypto-js");      
//AES加密      
function encrypt(text) {      
    return CryptoJS.AES.encrypt(      
        text,      
        CryptoJS.enc.Utf8.parse(key),      
        {      
            iv: CryptoJS.enc.Utf8.parse('0102030405060708'),      //偏移向量，如果加密模式是ECB的话，则不需要iv      
            mode: CryptoJS.mode.CBC,     //加密模式      1
            padding: CryptoJS.pad.Pkcs7   //填充      1
        }).toString()      1
}       1
//AES解密      1
function decrypt(text) {      1
    var result = CryptoJS.AES.decrypt(      1
        text,      1
        CryptoJS.enc.Utf8.parse(key),      1
        {      2
            iv: CryptoJS.enc.Utf8.parse('0102030405060708'),   //偏移向量，如果加密模式是ECB的话，则不需要iv      2
            mode: CryptoJS.mode.CBC,      //加密模式      2
            padding: CryptoJS.pad.Pkcs7    //填充      2
        })      2
    return result.toString(CryptoJS.enc.Utf8)      2
}       2
var key = "8Vh1Py0Eg8Ks8Ji7";         //秘钥key      2
var text = "123456";                  //要加密的字符串      2
var text_encode = encrypt(text);      //加密后的字符串      3
console.log("加密的字符串为：",decrypt(text_encode));    //打印出解密后的字符串      3
console.log("加密后的字符串为：",text_encode);             //打印出加密后的字符串
```


### ECB模式

```
var CryptoJS = require("crypto-js");      
//AES加密      
function encrypt(text) {      
    return CryptoJS.AES.encrypt(      
        text,      
        CryptoJS.enc.Utf8.parse(key),      
        {      
            // iv: "0102030405060708",      //偏移向量，如果加密模式是ECB的话，则不需要iv      
            mode: CryptoJS.mode.ECB,     //加密模式      1
            padding: CryptoJS.pad.Pkcs7   //填充      1
        }).toString()      1
}       1
//AES解密      1
function decrypt(text) {      1
    var result = CryptoJS.AES.decrypt(      1
        text,      1
        CryptoJS.enc.Utf8.parse(key),      1
        {      2
            // iv: "0102030405060708",       //偏移向量，如果加密模式是ECB的话，则不需要iv      2
            mode: CryptoJS.mode.ECB,      //加密模式      2
            padding: CryptoJS.pad.Pkcs7    //填充      2
        })      2
    return result.toString(CryptoJS.enc.Utf8)      2
}       2
var key = "8Vh1Py0Eg8Ks8Ji7";         //秘钥key      2
var text = "123456";                  //要加密的字符串      2
var text_encode = encrypt(text);      //加密后的字符串       3
console.log("加密的字符串为：",decrypt(text_encode));    //打印出解密后的字符串      3
console.log("加密后的字符串为：",text_encode);             //打印出加密后的字符串
```


Python实现AES加密
-------------

python需要安装 pycryptodome 库，装方法：pip install   pycryptodome

![](https://img-blog.csdnimg.cn/20200626011208429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### CBC模式

```
import base64      
from Crypto.Cipher import AES      
# 密钥（key）, 密斯偏移量（iv） CBC模式加密       
def AES_Encrypt(key, data):      
    vi = '0102030405060708'      
    pad = lambda s: s + (16 - len(s)%16) * chr(16 - len(s)%16)      
    data = pad(data)      
    # 字符串补位      1
    cipher = AES.new(key.encode('utf8'), AES.MODE_CBC, vi.encode('utf8'))      1
    encryptedbytes = cipher.encrypt(data.encode('utf8'))      1
    # 加密后得到的是bytes类型的数据      1
    encodestrs = base64.b64encode(encryptedbytes)      1
    # 使用Base64进行编码,返回byte字符串      1
    enctext = encodestrs.decode('utf8')      1
    # 对byte字符串按utf-8进行解码      1
    return enctext       2
def AES_Decrypt(key, data):      2
    vi = '0102030405060708'      2
    data = data.encode('utf8')      2
    encodebytes = base64.decodebytes(data)      2
    # 将加密数据转换位bytes类型数据      2
    cipher = AES.new(key.encode('utf8'), AES.MODE_CBC, vi.encode('utf8'))      2
    text_decrypted = cipher.decrypt(encodebytes)      2
    unpad = lambda s: s[0:-s[-1]]      2
    text_decrypted = unpad(text_decrypted)      2
    # 去补位      3
    text_decrypted = text_decrypted.decode('utf8')      3
    return text_decrypted       3
key = '8Vh1Py0Eg8Ks8Ji7'      3
data = '123456'      3
AES_Encrypt(key, data)      3
enctext = AES_Encrypt(key, data)   #加密后的字符串      3
text_decrypted = AES_Decrypt(key, enctext)  #加密的字符串      3
print("加密的字符串为：",text_decrypted)      4
print("加密后的字符串为：",enctext)
```


### ECB模式

```
from Crypto.Cipher import AES      
import base64       
BLOCK_SIZE = 16  # Bytes      
pad = lambda s: s + (BLOCK_SIZE - len(s) % BLOCK_SIZE) * chr(BLOCK_SIZE - len(s) % BLOCK_SIZE)      
unpad = lambda s: s[:-ord(s[len(s) - 1:])]       
#加密函数      
def aesEncrypt(key, data):      1
    '''      1
    AES的ECB模式加密方法      1
    :param key: 密钥      1
    :param data:被加密字符串（明文）      1
    :return:密文      1
    '''      1
    key = key.encode('utf8')      1
    # 字符串补位      1
    data = pad(data)      1
    cipher = AES.new(key, AES.MODE_ECB)      2
    # 加密后得到的是bytes类型的数据，使用Base64进行编码,返回byte字符串      2
    result = cipher.encrypt(data.encode())      2
    encodestrs = base64.b64encode(result)      2
    enctext = encodestrs.decode('utf8')      2
    return enctext      2
#解密函数      2
def aesDecrypt(key, data):      2
    '''       2
    :param key: 密钥      3
    :param data: 加密后的数据（密文）      3
    :return:明文      3
    '''      3
    key = key.encode('utf8')      3
    data = base64.b64decode(data)      3
    cipher = AES.new(key, AES.MODE_ECB)       3
    # 去补位      3
    text_decrypted = unpad(cipher.decrypt(data))      3
    text_decrypted = text_decrypted.decode('utf8')      4
    return text_decrypted       4
if __name__ == '__main__':      4
    key = '8Vh1Py0Eg8Ks8Ji7'      4
    data = '123456'       4
    enctext = aesEncrypt(key, data)      4
    text_decrypted = aesDecrypt(key, enctext)      4
    print("加密的字符串为：",text_decrypted)      4
    print("加密后的字符串为：",enctext)
```


在线AES加密解密：[http://tool.chacuo.net/cryptaes/](http://tool.chacuo.net/cryptaes/)