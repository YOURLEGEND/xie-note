**目录**

[哈希摘要算法](#%E5%93%88%E5%B8%8C%E6%91%98%E8%A6%81%E7%AE%97%E6%B3%95)

[对称加密算法](#t0)

[其他加密算法](#t1)

[编码](#t2)

* * *

### **哈希摘要算法**

以  root [加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)为例。

```
MD4：32位的摘要算法。2add09183d0b1dc0428701df9838fba      
MD5：32位的摘要算法。63a9f0ea7bb98050796b649e8548184      
SHA1：40位的摘要算法。dc76e9f0c0006e8f919e0c515c66dbba3982f785      
SHA224：56位的摘要算法。871ce144069ea0816545f52f09cd135d1182262c3b235808fa5a3281      
SHA256：64位的摘要算法。4813494d137e1631bba301d5acab6e7bb7aa74ce1185d456565ef51d737677b      
SHA384：96位的摘要算法。7ed8c2c790aa83d6c3e404b5368f6832c18d46a0e98b9c7a7a5e3ef823e2c9f0e310abbf6f7ea9d9d883ccb64ec2736a      
SHA512：128位的摘要算法。99adc231b045331e514a516b4b7680f588e3823213abe901738bc3ad67b2f6fcb3c64efb93d18002588d3ccc1a49efbae1ce20cb43df36b38651f11fa75678e8      
SHA3_224：56位的摘要算法。3e42295e89a3a84ce7ee38e2ba317aeb57ca3164459bdf48f4da0e92      
SHA3_256：64位的摘要算法。a00e4d3b352e9d11979549b9eef5dc951592f594488451e6cd86fdc4bce76a53      1
SHA3_384：96位的摘要算法。aac595410801e93eadb099ac575ccc12e43be2c15e31e7991e908329e5beb0729881b3be9ccdb0eeb6eb79335ea38b6d      1
SHA3_512：128位的摘要算法。8cd824c700eb0c125fff40c8c185d14c5dfe7f32814afac079ba7c20d93bc3c082193243c420fed22ef2474fbb85880e7bc1ca772150a1f759f8ddebca77711f
```


在线网站：[http://encode.chahuo.com/](http://encode.chahuo.com/) 

### **对称加密算法**

以  root 加密为例，秘钥也为：root

```
DES: 32位的加密算法。U2FsdGVkX1/vZNNSBkEYw5JKncYaTnvJ      
AES：44位的加密算法。U2FsdGVkX1/moV5k51x4G3qFUeRbN1IiibVTnQablq4=      
3DES：32位的佳美算法，3DES的加密需要指定加密模式，分别有：ECB、CBC、CTR、OFB、CFB。U2FsdGVkX18gxTvUCfMeZlnPkH8WDtnQ
```


在线网站：[http://encode.chahuo.com/](http://encode.chahuo.com/) 

                  [http://tool.chacuo.net/crypt3des](http://tool.chacuo.net/crypt3des)

                  [https://the-x.cn/cryptography/Tripledes.aspx](https://the-x.cn/cryptography/Tripledes.aspx) 

### **其他加密算法**

*   栅栏密码：[https://www.qqxiuzi.cn/bianma/zhalanmima.php](https://www.qqxiuzi.cn/bianma/zhalanmima.php)
*   凯撒密码：[https://www.qqxiuzi.cn/bianma/kaisamima.php](https://www.qqxiuzi.cn/bianma/kaisamima.php)
*   猪圈密码：[http://www.nicetool.net/app/pigpen\_chiper\_decrypt.html](http://www.nicetool.net/app/pigpen_chiper_decrypt.html)
*   维吉尼亚密码：[https://www.qqxiuzi.cn/bianma/weijiniyamima.php](https://www.qqxiuzi.cn/bianma/weijiniyamima.php)
*   培根密码：[http://tool.bugku.com/peigen/](http://tool.bugku.com/peigen/)
*   摩斯密码：[https://www.jb51.net/tools/morse.htm](https://www.jb51.net/tools/morse.htm)
*   当铺密码：[https://www.cnblogs.com/liume/p/10126444.html](https://www.cnblogs.com/liume/p/10126444.html)
*   Rot5加密：只对数字进行编码，用当前数字往前数的第5个数字替换当前数字
*   Rot13加密：只对字母进行编码，用当前字母往前数的第13个字母替换当前字母，[https://www.jisuan.mobi/puzzm6z1B1HH6yXW.html](https://www.jisuan.mobi/puzzm6z1B1HH6yXW.html)
*   Rot18加密：这是一个异类，本来没有，它是将ROT5和ROT13组合在一起，将其命名为ROT18。
*   Rot47加密：对数字、字母、常用符号进行编码，按照它们的ASCII值进行位置替换，用当前字符ASCII值往前数的第47位对应字符替换当前字符，用于ROT47编码的字符其ASCII值范围是33－126

### **编码**

*   URL编码：[http://tool.oschina.net/encode?type=4](http://tool.oschina.net/encode?type=4)
*   UUencode：UUencode的加密方式和base64很相似。但他的编码表有很多是特殊字符:”!”#￥%&‘（）\*+=’” 等等。例：UUencode(1234567) = (,3(S-#4V-PH\`
*   XXencode：XXencode的加密方式也和base64相似。跟base64打印字符相比，就是UUencode多一个“-” 字符，少一个”/” 字符。例：XXencode（1234567）= 6AH6nB1IqBkc+
*   ook编码：[https://www.cnblogs.com/liume/p/10126445.html](https://www.cnblogs.com/liume/p/10126445.html)
*   brainfuck编码：[https://www.cnblogs.com/liume/p/10126445.html](https://www.cnblogs.com/liume/p/10126445.html)
*   Base16编码：[https://www.qqxiuzi.cn/bianma/base.php?type=16](https://www.qqxiuzi.cn/bianma/base.php?type=16)
*   Base32编码：是由（A-Z、2-7）32个可见字符构成，“=”符号用作后缀填充，举个例子：base32（1234567）= GEZDGNBVGY3Q====  。[https://www.qqxiuzi.cn/bianma/base.php](https://www.qqxiuzi.cn/bianma/base.php)
*   Base58编码：相比base64少了数字0，大写字母 I，O，小写字母 l (这个是L），以及符号 + 和  /  ，base58（1234567）= 2s8YYFs4Vc
*   Base64编码：由（A-Z、a-z、0-9、+、/）64个可见字符构成，“=”符号用作后缀填充，base64（1234567）= MTIzNDU2Nw==   。[https://base64.supfree.net/](https://base64.supfree.net/) 
*   ASCII编码：[http://tool.oschina.net/commons?type=4](http://tool.oschina.net/commons?type=4)
*   unicode编码：[http://tool.chinaz.com/tools/unicode.aspx](http://tool.chinaz.com/tools/unicode.aspx)

中文——>unicode：中国 ——> \\u4e2d\\u56fd

英文——>unicode:   root ——> root

相关文章：[CTF编码和加密总结](https://blog.csdn.net/Everywhere_wwx/article/details/71551035)