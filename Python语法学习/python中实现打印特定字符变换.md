首先需要将 lib文件 放在该文件同一目录

![](https://img-blog.csdnimg.cn/20190430084938226.png)

使用的时候，先导入 **from lib.common import print\_msg** ，然后调用里面的 print\_msg() 方法即可！

lib文件地址：[https://download.csdn.net/download/qq\_36119192/11152629](https://download.csdn.net/download/qq_36119192/11152629)

python2和python3皆可用！

**示例：**

test.py文件内容

```
# -*- coding: utf-8 -*-      
"""      
Created on Mon Apr 29 18:30:35 2019      
@author: 小谢      
"""      
from lib.common import print_msg      
import time      
for i in range(101):      
    a=str(i)      1
    time.sleep(1)      1
    msg="%s found!"%a      1
    print_msg(msg)
```


![](https://img-blog.csdnimg.cn/20190430085156855.png)