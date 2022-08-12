**目录**

[Datetime](#t0)

[获取当前时间](#t1)

[获取当前日期](#t2)

[获取当前时间的tuple元组](#t3)

[格式化日期和时间](#t4)

[时间移动](#t5)

[获取两个时间的时间差](#t6)

[时间格式转换](#t7)

[Time](#t8)

[获取距元年(1970.1.1)的秒数](#t9)

[当时时间](#t10)

[格式化时间和日期](#t11)

[休眠](#t12)

[Calendar](#t13)

[打印某一年和某一个月的月历](#t14)

[时间戳](#t15)

[python3实现时间戳](#t16)

[python2实现时间戳](#t17)

[时间戳+秘钥生成sign验证](#t18)

[python2实现](#t19)

[python3实现](#t20)

* * *

Python提供了[datetime](https://so.csdn.net/so/search?q=datetime&spm=1001.2101.3001.7020)、time和 calendar 模块用来处理日期和时间，时间间隔是以秒为单位的浮点数。每个时间戳都是自1970年1月1日来算。

Datetime
--------

**datetime**在python中是用来处理时间的一个模块

datetime模块下又包含了几个类

| 类名 | 功能 |
| --- | --- |
| date | 日期对象,常用的属性有year, month, day |
| time | 时间对象 |
| datetime | 日期时间对象,常用的属性有hour, minute, second, microsecond |
| timedelta | 时间间隔，即两个时间点之间的长度 |
| datetime\_CAPI | 日期时间对象C语言接口 |
| tzinfo | 时区信息对象 |

下面介绍datetime中比较常用的一些函数

### **获取当前时间**

```
from datetime import datetime      
>>> print ( datetime.now() )      
2018-10-08 08:08:13.296957      
>>> print ( datetime.now().time() )      
08:08:13.296957      
>>> print ( type(datetime.now()) )      
<class 'datetime.datetime'>
```


### **获取当前日期**

```
from datetime import datetime      
>>> print ( datetime.now().date() )      
2018-10-08      
>>> print ( type(datetime.now().date() ) )      
<class 'datetime.date'>
```


### **获取当前时间的tuple元组**

```
from datetime import datetime      
print(datetime.now().timetuple())         
//time.struct_time(tm_year=2018, tm_mon=11, tm_mday=1, tm_hour=16, tm_min=21, tm_sec=5, tm_wday=3, tm_yday=305, tm_isdst=-1)      
print(datetime.now().timetuple().tm_year)  //2018  年      
print(datetime.now().timetuple().tm_mon)   //11    月      
print(datetime.now().timetuple().tm_mday)  //1     日      
print(datetime.now().timetuple().tm_hour)  //16    时      
print(datetime.now().timetuple().tm_min)   //21    分      
print(datetime.now().timetuple().tm_sec)   //5     秒      1
print(datetime.now().timetuple().tm_wday)  //3     0-6 0是周一，6是周日      1
print(datetime.now().timetuple().tm_yday)  //305   一年中的第几天      1
print(datetime.now().timetuple().tm_isdst) //-1    是否为下令时 1为是 0为不是 -1为未知，默认为-1
```


### 格式化日期和时间

```
from datetime import datetime      
print(datetime.now().strftime("%Y-%m-%d %H:%M:%S"))      
#############      
2018-11-01 19:25:45
```


### **时间移动**

使用datetime.timedelta这个方法来前后移动时间，可以用的参数有weeks，days，hours，minutes，seconds，microseconds。使用 days=1代表后一天，days=-1代表前一天，其他参数也一样

```
from datetime import datetime      
from datetime import timedelta      
>>>print( datetime.now() )      
2018-10-08 08:25:49.546137      
>>>print( datetime.now()+ timedelta(days=1))      
2018-10-09 08:25:49.547196
```


### **获取两个时间的时间差**

```
from datetime import datetime      
>>>nowtime=datetime.now()      
>>>utctime=datetime.utcnow()      
>>>print( nowtime-utctime )      
8:00:00      
>>>print((nowtime-utctime).total_seconds())         ##以秒计算      
28800.0
```


### **时间格式转换**

datetime转str格式

```
from datetime import datetime      
>>>print( datetime.now() )      
2018-10-08 08:33:37.369422      
>>>print( datetime.now().strftime("%Y-%m-%d %H:%M:%S") )      
2018-10-08 08:33:37      
>>>print( datetime.now().strftime("%Y-%m-%d") )      
2018-10-08       
>>>print( datetime.now().strftime("%H-%M:%S") )      
08:33:37
```


str格式转datetime

```
from datetime import datetime      
>>>print( datetime.strptime("2018-10-08 09:00:00","%Y-%m-%d %H:%M:%S"))      
2018-10-08 09:00:00      
>>>print( type(datetime.strptime("2018-10-08 09:00:00","%Y-%m-%d %H:%M:%S")) )      
<class 'datetime.datetime'>
```


datetime转timestamp

```
from datetime import datetime      
import time      
>>>now=datetime.now()      
>>>print(now)      
2018-10-08 08:51:42.215168      
>>>print( time.mktime(now.timetuple()) )      
1538959902.0                  ##现在距离1970-01-01 00:00:00的秒数
```


timestamp转datetime 

```
from datetime import datetime      
>>>print( datetime.fromtimestamp(1538959902.0))      
2018-10-08 08:51:42
```


将2020 Feb 14转换为2020-02-14格式

```
from datetime import datetime      
c_time="2020 Feb 14"      
ctime = datetime.strptime(c_time,"%Y %b %d")      
ctime = str(ctime).split(" ")[0]      
print(ctime)
```


![](https://img-blog.csdnimg.cn/20200217131417186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Time
----

### 获取距元年(1970.1.1)的秒数

```
>>import time      
>>print(time.time())      
1541121353.1594238      
>>print(time.ctime(time.time()))      
Fri Nov  2 09:15:53 2018
```


### 当时时间

```
import time      
localtime=time.localtime()  #返回结构时间      
asctime=time.asctime(localtime)    #将结构时间转换为asctime      
print(localtime)      
print(asctime)      
############      
time.struct_time(tm_year=2018, tm_mon=11, tm_mday=1, tm_hour=19, tm_min=13, tm_sec=5, tm_wday=3, tm_yday=305, tm_isdst=0)      
Thu Nov  1 19:13:05 2018
```


### 格式化时间和日期

```
import time      
formattime1=time.strftime("%Y-%m-%d %H:%M:%S",time.localtime())      
formattime2=time.strftime("%a-%b-%d %H:%M:%S:%Y",time.localtime())      
print(formattime1)      
print(formattime2)      
#############################      
2018-11-01 19:16:30      
Thu-Nov-01 19:16:30:2018
```


![](https://img-blog.csdnimg.cn/201811011916370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 休眠

```
import time      
time.sleep(10)   ##休眠10秒
```


Calendar
--------

[Calendar](https://so.csdn.net/so/search?q=Calendar&spm=1001.2101.3001.7020)模块有很广泛的方法用来处理年历和月历

### 打印某一年和某一个月的月历

```
import calendar       
print("2018年的年历是：")      
print(calendar.prcal(2018))      
###################################      
2018年的年历是：      
                                  2018       
      January                   February                   March      1
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      1
 1  2  3  4  5  6  7                1  2  3  4                1  2  3  4      1
 8  9 10 11 12 13 14       5  6  7  8  9 10 11       5  6  7  8  9 10 11      1
15 16 17 18 19 20 21      12 13 14 15 16 17 18      12 13 14 15 16 17 18      1
22 23 24 25 26 27 28      19 20 21 22 23 24 25      19 20 21 22 23 24 25      1
29 30 31                  26 27 28                  26 27 28 29 30 31       1
       April                      May                       June      1
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      1
                   1          1  2  3  4  5  6                   1  2  3      2
 2  3  4  5  6  7  8       7  8  9 10 11 12 13       4  5  6  7  8  9 10      2
 9 10 11 12 13 14 15      14 15 16 17 18 19 20      11 12 13 14 15 16 17      2
16 17 18 19 20 21 22      21 22 23 24 25 26 27      18 19 20 21 22 23 24      2
23 24 25 26 27 28 29      28 29 30 31               25 26 27 28 29 30      2
30       2
        July                     August                  September      2
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      2
                   1             1  2  3  4  5                      1  2      2
 2  3  4  5  6  7  8       6  7  8  9 10 11 12       3  4  5  6  7  8  9      3
 9 10 11 12 13 14 15      13 14 15 16 17 18 19      10 11 12 13 14 15 16      3
16 17 18 19 20 21 22      20 21 22 23 24 25 26      17 18 19 20 21 22 23      3
23 24 25 26 27 28 29      27 28 29 30 31            24 25 26 27 28 29 30      3
30 31       3
      October                   November                  December      3
Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      Mo Tu We Th Fr Sa Su      3
 1  2  3  4  5  6  7                1  2  3  4                      1  2      3
 8  9 10 11 12 13 14       5  6  7  8  9 10 11       3  4  5  6  7  8  9      3
15 16 17 18 19 20 21      12 13 14 15 16 17 18      10 11 12 13 14 15 16      4
22 23 24 25 26 27 28      19 20 21 22 23 24 25      17 18 19 20 21 22 23      4
29 30 31                  26 27 28 29 30            24 25 26 27 28 29 30      4
                                                    31       4
cal=calendar.month(2018,10)      4
print("2018年10月的月历是：")      4
print(cal)      4
#########################      4
2018年10月的月历是：      5
    October 2018      5
Mo Tu We Th Fr Sa Su      5
 1  2  3  4  5  6  7      5
 8  9 10 11 12 13 14      5
15 16 17 18 19 20 21      5
22 23 24 25 26 27 28      5
29 30 31       5
print(calendar.leapdays(2000,2018))  #返回2000~2018内的闰年数      6
print(calendar.weekday(2018,11,2))  ##返回2018.11.2是星期几，0-6
```


时间戳
---

### **python3实现时间戳**

```
import time      
timestamp=round(time.time()*1000)      
print(timestamp)
```


![](https://img-blog.csdnimg.cn/20200113114546805.png)

### **python2实现时间戳**

```
import time      
timestamp = long(round(time.time()*1000))      
print timestamp
```


![](https://img-blog.csdnimg.cn/20200113114841512.png)

时间戳+秘钥生成sign验证
--------------

以下 [时间戳](https://so.csdn.net/so/search?q=%E6%97%B6%E9%97%B4%E6%88%B3&spm=1001.2101.3001.7020)+秘钥生成sign验证，广泛用于网站等进行验证。

第一步：把 timestamp+"\\n"+密钥 当做签名字符串 string\_to\_sign

第二步：使用HmacSHA256算法计算签名，得到 hmac\_code

第三步：将hmac\_code进行Base64 encode，最后再把签名参数再进行urlEncode，得到最终的签名sign（需要使用UTF-8字符集）

### python2实现

```
#python 2.7      
import time      
import hmac      
import hashlib      
import base64      
import urllib       
timestamp = long(round(time.time() * 1000))      
secret = 'xiegongzi'     	#秘钥      1
secret_enc = bytes(secret).encode('utf-8')      1
string_to_sign = '{}\n{}'.format(timestamp, secret)     #把timestamp+"\n"+密钥 当做签名字符串 string_to_sign      1
string_to_sign_enc = bytes(string_to_sign).encode('utf-8')      1
hmac_code = hmac.new(secret_enc, string_to_sign_enc, digestmod=hashlib.sha256).digest()  #使用HmacSHA256算法计算签名，得到 hmac_code      1
hmac_code_base64=base64.b64encode(hmac_code)    #将hmac_code进行Base64 encode      1
sign = urllib.quote_plus(hmac_code_base64)      #进行urlEncode，得到最终的签名sign      1
print("sign = "+sign)
```


![](https://img-blog.csdnimg.cn/20200113145614489.png)

### python3实现

```
#python 3.7      
import time      
import hmac      
import hashlib      
import base64      
import urllib      
from urllib import parse       
timestamp = round(time.time() * 1000)      1
secret = 'xiegongzi'     	#秘钥      1
secret_enc = bytes(secret.encode('utf-8'))      1
string_to_sign = '{}\n{}'.format(timestamp, secret)     #把 timestamp+"\n"+密钥 当做签名字符串 string_to_sign      1
string_to_sign_enc = bytes(string_to_sign.encode('utf-8'))      1
hmac_code = hmac.new(secret_enc, string_to_sign_enc, digestmod=hashlib.sha256).digest()  #使用HmacSHA256算法计算签名，得到 hmac_code      1
hmac_code_base64=base64.b64encode(hmac_code)  #将hmac_code进行Base64 encode      1
sign = urllib.parse.quote(hmac_code_base64)   #进行urlEncode，得到最终的签名sign      1
print("sign = "+sign)
```


![](https://img-blog.csdnimg.cn/20200113145532167.png)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-6)[预备知识](https://edu.csdn.net/skill/python/python-3-6)[模块管理](https://edu.csdn.net/skill/python/python-3-6)84711 人正在系统学习中