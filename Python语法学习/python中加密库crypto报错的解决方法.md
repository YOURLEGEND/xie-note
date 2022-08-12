我们在一些脚本中经常会遇到python的[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)库：crypto，安装运行的时候经常会报错。现在让我们来看看crypto加密库的一些区别：

*   crypto：一个加密解密的库
*   pycrypto：crypto在python上面的名字是pycrypto它是一个第三方库，但是已经停止更新三年了，所以不建议安装这个库
*   pycrytodome：是pycrypto替代品，且可以兼容之前的pycrypto，与pycrypto不能同时安装
*   pycryptodomex：此版本为新版解密加密库

可以执行如下命令：pip install pycryptodomex ，即安装新版本的加密解密库

然后引入改成如下方式即可：

```
from Cryptodome.Hash import MD4      
from Cryptodome.Cipher import AES
```


参考文章：[https://www.cnblogs.com/miaomiaokaixin/p/11534630.html](https://www.cnblogs.com/miaomiaokaixin/p/11534630.html)