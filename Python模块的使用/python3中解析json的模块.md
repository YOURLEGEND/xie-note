**目录**

[python数据与json数据互转](#t0)

 [对request请求的json数据格式化](#t1)

* * *

json模块是python中用来解析json数据格式的模块。

![](https://img-blog.csdnimg.cn/20201006185507327.png)

json中有两种[数据结构](https://so.csdn.net/so/search?q=%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84&spm=1001.2101.3001.7020)：对象和数组。

*   对象：用大括号表示，由键值对组成，每个键值对用逗号隔开。其中key必须为字符串且是双引号，value可以是多种数据类型。
*   数组：用中括号表示，每个元素之间用逗号隔开。

json中的[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)都要用双括号表示，json数据可以嵌套出结构更加复杂的数据。

关于json数据格式，传送门：[JSON](https://xie1997.blog.csdn.net/article/details/83047334)

**python 原始类型向 json 类型的转化对照表**

| 
**Python——>Json**

 | 

**Json——>Json**

 |
| --- | --- |
| 

dict

 | 

object

 | 

object

 | 

dict

 |
| 

list, tuple

 | 

array

 | 

array

 | 

list

 |
| 

str, unicode

 | 

string

 | 

string

 | 

unicode

 |
| 

int, long, float

 | 

number

 | 

number (int)

 | 

int, long

 |
| 

True

 | 

true

 | 

number (real)

 | 

float

 |
| 

False

 | 

false

 | 

true

 | 

True

 |
| 

None

 | 

null

 | 

false

 | 

False

 |

| 函数 | 描述 |
| --- | --- |
| json.dumps | 将 Python 对象编码成 JSON 字符串对象 |
| json.loads | 将已编码的 JSON 字符串解码为 Python 对象 |

### python数据与json数据互转

```
import json       
#字典格式      
data={ "name":"xie",      
       "sex" :"man",      
       "id"  : "66" }       
print(data,type(data))      
print(data['name'])      1
print("*"*50)       1
#将字典转为字符串格式      1
data2=json.dumps(data)         1
print(data2,type(data2))      1
print("*"*50)       1
data3=json.loads(data2)  #将字符串对象转为字典格式      1
print(data3,type(data3))      1
print(data['name'])      2
print("*"*50)
```


![](https://img-blog.csdnimg.cn/20201116225052225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

###  对request请求的json数据格式化

```
resp=requests.get(url)      
data=json.loads(resp.text)      
result=data['result']      
count=data['result]['count']
```


文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-115)[其他](https://edu.csdn.net/skill/python/python-3-115)[自动化任务](https://edu.csdn.net/skill/python/python-3-115)84785 人正在系统学习中