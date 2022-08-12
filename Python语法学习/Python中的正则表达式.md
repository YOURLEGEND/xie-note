**目录**

[正则表达式](#t0)

[re.match()函数](#t1)

[re.search()函数](#t2)

[re.match()和re.search()的联系和区别](#t3) 

[检索和替换(re.sub)](#t4)

[re.compile()函数](#t5)

[re.findall()函数](#t6)

[re.finditer()函数](#t7)

[re.split()函数](#t8) 

[正则表达式对象](#t9) 

[re.RegexObject](#t10)

[re.MatchObject](#t11)

[正则表达式标志位](#t12)

[正则表达式模式](#t13)

[正则表达式实例](#t14) 

* * *

正则表达式
-----

[正则表达式](https://so.csdn.net/so/search?q=%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1001.2101.3001.7020)是一个特殊的字符序列，它能帮助你方便的检查一个字符串是否与某种模式匹配。

Python 使用 re 模块提供正则表达式功能，它提供 Perl 风格的正则表达式模式。

### re.match()函数

re.match 尝试从字符串的 起始位置 匹配一个模式，匹配成功的话返回一个匹配的对象，否则返回None。

**函数语法**：

>  re.match( pattern ,  string  \[,flags=0\] )

参数：

*   pattern：匹配的正则表达式
*   string：要匹配的字符串
*   flags：可选参数，标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等

```
>>import re      
>>print(re.match('www','www.baidu.com'))      
>>print(re.match('baidu','www.baidu.com'))      
>>print(re.match('www','www.baidu.com').span())  #返回匹配对象在字符串中的索引，返回元组类型的数据       
<re.Match object; span=(0, 3), match='www'>      
None      
(0, 3)
```


###  **re.search()函数**

re.search 扫描整个字符串并返回第一个成功的匹配。匹配成功 re.search 方法返回一个匹配的对象，否则返回None。

**函数语法**：

>  re.search( pattern , string   \[,flags=0\] )

参数：

*   pattern：匹配的正则表达式
*   string：要匹配的字符串
*   flags：可选参数，标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等

```
>>import re      
>>print(re.search('www','www.baidu.com').span())      
>>print(re.search('www','www.baidu.com').group(0)      
>>print(re.search('com','www.baidu.com').span())      
(0, 3)      
www      
(10, 13)
```


![](https://img-blog.csdnimg.cn/2020010816315834.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### re.match()和re.search()的联系和区别 

re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。

 不管是re.match() 还是 re.search() , 我们都可以使用 group(num) 或 groups() 匹配对象函数来获取匹配表达式。

| 匹配对象方法 | 描述 |
| --- | --- |
| group(num=0) | 匹配的整个表达式的字符串，group() 可以一次输入多个组号，在这种情况下它将返回一个包含那些组所对应值的元组。 |
| groups() | 返回一个包含所有小组字符串的元组，从 1 到 所含的小组号。 |

```
import re      
line = "Cats are smarter than dogs"      
matchObj = re.match( r'(.*) are (.*?) .*', line, re.M|re.I)      
或  matchObj = re.searcch( r'(.*) are (.*?) .*', line, re.M|re.I)       
if matchObj:      
   print ("matchObj.group() : ", matchObj.group())      
   print ("matchObj.group(1) : ", matchObj.group(1))      
   print ("matchObj.group(2) : ", matchObj.group(2))      1
else:      1
   print ("No match!!")      1
################################################################      1
matchObj.group() :  Cats are smarter than dogs      1
matchObj.group(1) :  Cats      1
matchObj.group(2) :  smarter
```


### 检索和替换(re.sub)

Python 的 re 模块提供了 re.sub 用于替换字符串中的匹配项

> re.sub(pattern , repl , string  \[, count=0\] )

参数：

*   pattern：正则中的模式字符串
*   repl：替换成的字符串，也可以是一个函数
*   string：要被替换的字符串
*   count：模式匹配后替换的最大次数，默认0表示替换所有的匹配

```
import re       
a="hello,word!hello,word!"      
ret=re.sub("ll","dd",a)              #将a中所有的ll替换成dd      
ret2=re.sub("ll","dd",a,count=1)     #将a中第一个ll替换成dd      
print(ret)      
print(ret2)      
########################################      
heddo,word!heddo,word!      1
heddo,word!hello,word!       1
#将./之间的任何数字替换成0      1
import re      1
ip="192.168.1.3/24"      1
ip=re.sub(r"\.([0-9]+)/",".0/",a)      1
print(ip)      1
#####################################      1
192.168.1.0/24
```


**re.compile()函数**

compile 函数用于编译正则表达式，生成一个正则表达式（ Pattern ）对象，供 match() 和 search() 这两个函数使用。

> re.compile( pattern \[, flags\] )

参数

*   pattern：一个字符串形式的正则表达式
*   flags：可选参数，表示匹配模式，比如忽略大小写，多行模式

```
import re       
pattern=re.compile(r'^h')  #匹配开头是h的      
m=pattern.match('hello')      
print(m)      
###################################      
<re.Match object; span=(0, 1), match='h'>
```


### **re.findall()函数**

在字符串中找到正则表达式所匹配的所有子串，并返回一个列表，如果没有找到匹配的，则返回空列表。

**注意：** match 和 search 是匹配一次， findall 是匹配所有。

> findall ( pattern , string  \[, pos \] \[, endpos \] )

*   pattern：正则中的模式字符串
*   string：待匹配的字符串
*   pos：可选参数，指定字符串的起始位置，默认为0
*   endpos：可选参数，指定字符串的结束位置，默认为字符串的长度

```
import re      
a="hello,word!hello,word!"      
m=re.findall("ll",a)      
print(m)      
################################      
['ll', 'll']       1
import re      1
pattern=re.compile('ll')      1
m=pattern.findall("hello,word!hello,word!")      1
print(m)      1
###################################      1
['ll', 'll']       1
#匹配./之间的所有数字      1
import re      2
ip="192.168.1.213/24"      2
a=re.findall("\.([0-9]+)/",ip)      2
print(a)      2
#####################      2
['213']
```


**findall和sub函数正则匹配时的区别**

![](https://img-blog.csdnimg.cn/20191015225029831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **re.finditer()函数**

和 findall 类似，在字符串中找到正则表达式所匹配的所有子串，并把它们作为一个迭代器返回。

> finditer( pattern , string  \[, flags\] )

参数

*   pattern：匹配的正则表达式
*   string：要匹配的字符串
*   flags：可选参数，标志位，用于控制正则表达式的匹配方法，如：是否区分大小写，多行匹配等等

```
import re      
it=re.finditer("ll","hello,word!hello,word!")      
for i in it:      
    print(i.group())      
########################################      
ll      
ll
```


### re.split()函数 

[split](https://so.csdn.net/so/search?q=split&spm=1001.2101.3001.7020) 方法按照能够匹配的子串将字符串分割后返回列表型数据

> finditer( pattern , string  \[,maxsplit\]  \[,flags=0\]  )

参数

*   pattern：匹配的正则表达式
*   string：要匹配的字符串
*   maxspliit：分隔次数，maxsplit=1分隔一次，默认为0，不限制次数
*   flags：标志位，用于控制正则表达式的匹配方法，如：是否区分大小写，多行匹配等等

```
import re      
a=re.split('ll',"hello,hello,hello")      
print(a)      
######################################      
['he', 'o,he', 'o,he', 'o']
```


正则表达式对象 
--------

### re.RegexObject

re.compile() 返回 RegexObject 对象。

### re.MatchObject

group() 返回被 RE 匹配的字符串。

*   start() 返回匹配开始的位置
*   end() 返回匹配结束的位置
*   span() 返回一个元组包含匹配 (开始,结束) 的位置

正则表达式标志位
--------

正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位 OR(|) 它们来指定。如 re.I | re.M 被设置成 I 和 M 标志

![](https://img-blog.csdnimg.cn/20181105190550183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

比如：

```
url=re.findall(r"window.location.href=['\"](.*?)['\"]",html,re.I|re.M)
```


 正则表达式模式
--------

模式字符串使用特殊的语法来表示一个正则表达式：

字母和数字表示他们自身。一个正则表达式模式中的字母和数字匹配同样的字符串。

多数字母和数字前加一个反斜杠时会拥有不同的含义。

标点符号只有被转义时才匹配自身，否则它们表示特殊的含义。

反斜杠本身需要使用反斜杠转义。

由于正则表达式通常都包含反斜杠，所以你最好使用原始字符串来表示它们。模式元素(如 r'\\t'，等价于 \\\\t )匹配相应的特殊字符。

下表列出了正则表达式模式语法中的特殊元素。如果你使用模式的同时提供了可选的标志参数，某些模式元素的含义会改变

| 模式 | 描述 |
| --- | --- |
| ^ | 匹配字符串的开头 |
| $ | 匹配字符串的末尾。 |
| . | 匹配任意字符，除了换行符，当re.DOTALL标记被指定时，则可以匹配包括换行符的任意字符。 |
| \[...\] | 用来表示一组字符,单独列出：\[amk\] 匹配 'a'，'m'或'k' |
| \[^...\] | 不在\[\]中的字符：\[^abc\] 匹配除了a,b,c之外的字符。 |
| re\* | 匹配0个或多个的表达式。 |
| re+ | 匹配1个或多个的表达式。 |
| re? | 匹配0个或1个由前面的正则表达式定义的片段，非贪婪方式 |
| re{ n} | 匹配n个前面表达式。例如，"o{2}"不能匹配"Bob"中的"o"，但是能匹配"food"中的两个o。 |
| re{ n,} | 精确匹配n个前面表达式。例如，"o{2,}"不能匹配"Bob"中的"o"，但能匹配"foooood"中的所有o。"o{1,}"等价于"o+"。"o{0,}"则等价于"o\*"。 |
| re{ n,m} | 匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式 |
| a| b | 匹配a或b |
| (re) | G匹配括号内的表达式，也表示一个组 |
| (?imx) | 正则表达式包含三种可选标志：i, m, 或 x 。只影响括号中的区域。 |
| (?-imx) | 正则表达式关闭 i, m, 或 x 可选标志。只影响括号中的区域。 |
| (?: re) | 类似 (...), 但是不表示一个组 |
| (?imx: re) | 在括号中使用i, m, 或 x 可选标志 |
| (?-imx: re) | 在括号中不使用i, m, 或 x 可选标志 |
| (?#...) | 注释. |
| (?= re) | 前向肯定界定符。如果所含正则表达式，以 ... 表示，在当前位置成功匹配时成功，否则失败。但一旦所含表达式已经尝试，匹配引擎根本没有提高；模式的剩余部分还要尝试界定符的右边。 |
| (?! re) | 前向否定界定符。与肯定界定符相反；当所含表达式不能在字符串当前位置匹配时成功。 |
| (?> re) | 匹配的独立模式，省去回溯。 |
| \\w | 匹配数字字母下划线 |
| \\W | 匹配非数字字母下划线 |
| \\s | 匹配任意空白字符，等价于 \[\\t\\n\\r\\f\]。 |
| \\S | 匹配任意非空字符 |
| \\d | 匹配任意数字，等价于 \[0-9\]。 |
| \\D | 匹配任意非数字 |
| \\A | 匹配字符串开始 |
| \\Z | 匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串。 |
| \\z | 匹配字符串结束 |
| \\G | 匹配最后匹配完成的位置。 |
| \\b | 匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。 |
| \\B | 匹配非单词边界。'er\\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。 |
| \\n, \\t, 等。 | 匹配一个换行符。匹配一个制表符, 等 |
| \\1...\\9 | 匹配第n个分组的内容。 |
| \\10 | 匹配第n个分组的内容，如果它经匹配。否则指的是八进制字符码的表达式。 |

### 正则表达式实例 

字符匹配

| 实例 | 描述 |
| --- | --- |
| python | 匹配 "python". |

字符类

| 实例 | 描述 |
| --- | --- |
| \[Pp\]ython | 匹配 "Python" 或 "python" |
| rub\[ye\] | 匹配 "ruby" 或 "rube" |
| \[aeiou\] | 匹配中括号内的任意一个字母 |
| \[0-9\] | 匹配任何数字。类似于 \[0123456789\] |
| \[a-z\] | 匹配任何小写字母 |
| \[A-Z\] | 匹配任何大写字母 |
| \[a-zA-Z0-9\] | 匹配任何字母及数字 |
| \[^aeiou\] | 除了aeiou字母以外的所有字符 |
| \[^0-9\] | 匹配除了数字外的字符 |

特殊字符类

| 实例 | 描述 |
| --- | --- |
| . | 匹配除 "\\n" 之外的任何单个字符。要匹配包括 '\\n' 在内的任何字符，请使用象 '\[.\\n\]' 的模式。 |
| \\d | 匹配一个数字字符。等价于 \[0-9\]。 |
| \\D | 匹配一个非数字字符。等价于 \[^0-9\]。 |
| \\s | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 \[ \\f\\n\\r\\t\\v\]。 |
| \\S | 匹配任何非空白字符。等价于 \[^ \\f\\n\\r\\t\\v\]。 |
| \\w | 匹配包括下划线的任何单词字符。等价于'\[A-Za-z0-9\_\]'。 |
| \\W | 匹配任何非单词字符。等价于 '\[^A-Za-z0-9\_\]'。 |

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-148)[网络爬虫](https://edu.csdn.net/skill/python/python-3-148)[正则表达式](https://edu.csdn.net/skill/python/python-3-148)84785 人正在系统学习中