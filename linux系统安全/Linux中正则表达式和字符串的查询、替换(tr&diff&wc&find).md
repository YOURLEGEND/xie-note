**目录**

[正则表达式](#t0)

[基本正则表达式](#t1)

[扩展正则表达式](#t2)

[grep](#t3)

[tr](#t4)

[diff](#t5)

[du](#t6)

[wc](#t7)

[find](#t8)

* * *

正则表达式
-----

**正则表达式**，又称正规表示法、常规表示法（ Regular Expression，在代码中常简写为[regex](https://so.csdn.net/so/search?q=regex&spm=1001.2101.3001.7020)、regexp或RE），计算机科学的一个概念。正则表达式使用单个字符串来描述、匹配一系列符合某个句法规则的字符串。正则表达式可以用来检查一个串是否含有某种子串、将匹配的子串替换或者从某个串中取出符合某个条件的子串等。在很多文本编辑器里，正则表达式通常被用来检索、替换那些符合某个模式的文本。

[正则表达式](https://so.csdn.net/so/search?q=%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1001.2101.3001.7020)分为 基本正则表达式 和 扩展正则表达式。

元字符就是指那些在正则表达式中具有特殊意义的专用字符

### **基本正则表达式**

**字符匹配**

| 元字符 | 含义 |
| --- | --- |
| **.** | 匹配单个任意字符 |
| \[a-z\] | 字符范围，只匹配a-z内的任意字符 |
| \[ ^a-z\] | 匹配a-z之外的任意字符 |

![](https://img-blog.csdn.net/20181009150127284?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**次数匹配**

| 元字符 | 含义 |
| --- | --- |
| \* | 匹配0或任意多个单字符，一般结合 . 使用 |
| \\? | 匹配前面的字符0次或1次，即前面的字符可有可无 |
| \\+ | 匹配前面的字符至少一次，即前面的字符至少出现一次 |
| x\\{n\\} | 精确匹配 x 出现次数为 n |
| x\\{n,\\} | 匹配 x 出现次数至少为 n |
| x\\{n,m\\} | 
匹配 x 出现次数在 n,m 之间

 |

![](https://img-blog.csdn.net/20181009202811798?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**位置匹配**

| 元字符 | 含义 |
| --- | --- |
| ^ | 只匹配行首 |
| $ | 只匹配行尾 |
| \\< |  锚定词首，用于单词的最左侧 |
| \\> |  锚定词尾，用于单词的最右侧 |

![](https://img-blog.csdn.net/20181009203032627?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**分组及引用**

| 元字符 | 含义 |
| --- | --- |
|  | 将一个或多个字符捆绑在一起，当做一个整体 |
| \\1,\\2,\\3..... | 
向后引用前面被匹配到的第1个、第2个，第3个.....

 |

```
1、过滤 /etc/passwd 中以s或S开头的行      
grep "^[sS]" /etc/passwd      
2、过滤 /etc/passwd　中不以小写字母开头的行      
grep "^[^a-z]" /etc/passwd      
3、过滤 /etc/passwd 中以nologin结尾的行      
grep "nologin$" /etc/passwd      
4、过滤 /etc/passwd 中以空格开头的行      
grep "^ " /etc/passwd      
5、过滤 /etc/passwd 中的空行      1
grep "^$" /etc/passwd       1
6、过滤 /etc/passwd 中以r开头的行，r后面可以跟0个或任意多个o(也就是说这行的第一个字符必须是r，这行还可以有0个或任意个o)      1
grep "^ro*" /etc/passwd      1
7、过滤 /etc/passwd 中以r开头的行，r后面必须跟1个或1个以上的字符(也就是这行第一个字符必须是r，而且这行必须还有其他字符，空格也算字符)      1
grep "^r." /etc/passwd      1
8、过滤 /etc/passwd 中r后跟了任意字符，任意字符后跟了 h 的行 (也就是这一行必须得有r和h，而且h必须在r的后面)      1
grep "r.*h" /etc/passwd       1
9、过滤/etc/passwd 中，包含 root 单词的行，并且root不和其他字符相邻(空格不算)      2
grep "\<root\>" /etc/passwd      2
10、过滤 /etc/passwd 中 r后跟了o  o出现0次或者1此的行      2
grep "ro\?" /etc/passwd      2
11、过滤 /etc/passwd 中 r后跟了o  o出现至少一次的行      2
grep "ro\+" /etc/passwd      2
12、过滤 /etc/passwd 中， r后跟了o，o只出现2次的行      2
grep "ro\{2\}" /etc/passwd      2
13、过滤 /etc/passwd 中, r 后跟了o ，o出现至少1次至多2次的行      2
grep "ro\{1,2\}" /etc/passwd      2
14、 过滤 /etc/passwd　中，ｒ后跟了o,  o 至少出现2次的行      3
grep "ro\{2,\}" /etc/passwd
```


![](https://img-blog.csdn.net/20181009093245342?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181009092909809?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181009084436515?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **扩展正则表达式**

扩展正则表达式使用 egrep 或者 [grep](https://so.csdn.net/so/search?q=grep&spm=1001.2101.3001.7020)  -E 

| 元字符 | 含义 |
| --- | --- |
| ? | 匹配紧挨在前面的字符 0 次或 1 次 |
| {m,n} | 匹配紧挨着前面的字符至少 m 次，至多 n 次 |
| + | 至少匹配前面的字符一次 |
| a|b | 匹配 a 或者 b |
| () | 分组 |

```
1、匹配 /etc/passwd 中r后紧跟了o，o出现0次或者1次的行      
egrep "ro?" /etc/passwd      
2、匹配 /etc/passwd 中r后紧跟了o，o出现至少1次至多2次的行      
egrep "ro{1,2}" /etc/passwd      
3、匹配 /etc/passwd 中r后紧跟了o，o出现至少1次的行      
egrep "ro+" /etc/passwd      
4、匹配 /etc/passwd 中出现root或者halt的行      
egrep "root|halt" /etc/passwd      
5、匹配 /etc/passwd 中出现root on或者halt failed的行      1
egrep "(root on|halt failed)" /etc/passwd
```


![](https://img-blog.csdn.net/2018100909593460?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

grep
----

[Linux中grep工具的使用](https://blog.csdn.net/qq_36119192/article/details/82974781)

**tr**
------

| 命令 | 含义 |
| --- | --- |
| tr   set1  set2 | 用 set2 的字符替换 set1 |
| tr -d set1 | 删除 set1 中指定的字符 |
| tr  -s  set1 | 压缩 set1 中指定的字符 |
| tr -s  set1  set2 | 用 set2 的字符替换 set1 ,然后压缩 set2 中指定的字符 |
| tr  -ds  set1  set2 | 
先删除 set1 的字符，再压缩 set1 中的字符

 |

eg:  tr  'A-Z'  'a-z'   <  passwd   将passwd中 A-Z 的大写字符全部替换成 a-z 的小写字符，但是并不会改变passwd中的数据

tr  'A-Z'  'a-z'  < passwd  >  passwd2   将passwd中 A-Z 的大写字符全部替换成 a-z 的小写字符，然后写入到 passwd2 中

diff
----

**diff**   passwd   passwd2     比较两个文件的不同

这里表示文件passwd和文件passwd2第2行、第4、5行和第7行都不同，并且分别打印出文件1和文件3的第2、4 、5、7 行

![](https://img-blog.csdn.net/20180930102610825?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

diff  passwd    passwd2   > diff.path               将 passwd 和 passwd2 的不同写入到 diff.path文件中

patch  -b  passwd2  diff.path                        用 diff.path文件修改 passwd2 中的不同，最后得到的 passwd2 文件和 passwd 文件一模一样

du
--

**du** ： 查看目录或文件占用大小

*            不加参数时，显示当前文件夹下的所有文件夹的磁盘使用情况(包括递归文件夹内的文件夹)
*            -a  给指定文件夹内的所有文件显示磁盘使用情况( 包括层层递归文件 )
*            -s  仅显示总计，即当前目录的大小
*            -h 以人性化方式显示
*            du -sh    /tmp/test     或者      du -sh  /tmp/test/                      显示test文件夹的占用大小
*            du -sh   /tmp/test/\*      显示test文件夹内的所有文件和文件夹的占用大小 (不包括递归文件)
*            du -ah  /tmp/test         显示test文件夹内的所有文件的占用大小（包括递归文件）

wc
--

**wc** ： 统计文件中的数据 

*           -  l 统计行数
*           - c 统计字节数
*   　     - w 统计字数
*           - m 统计字符数

统计文件夹内文件的个数:         ls -l | grep "^-" | wc -l  
统计文件夹内包括递归文件的个数:   ls -lR | grep "^-" | wc -l  
统计文件夹内文件夹的个数 :     ls -l | grep "^d" | wc -l  
统计文件夹内包括递归文件夹的个数:   ls -lR | grep "^d" | wc -l

find
----

**find** ：查找文件或文件夹

*     根据文件名查找     find  /tmp  -name  abc     查找/tmp目录下名字叫做abc的文件，它会遍历tmp目录下的所有文件及文件夹
*     根据文件大小查找   find /tmp   -size    -1M       +号表示大于  -号表示小于
*     -name   通过文件名查找
*     -size     通过文件大小查找
*     -type    通过文件类型查找
*     -user    通过文件用户查找
*     -perm  通过权限查找
*      因为find对管道支持比较弱，所以可以借助exec扩展选项
*     find /etc  -size  +1M  -exec  ls  -lh  {}  \\;               {}代表find查找到的内容
*     find /etc -size +1M   -exec  cp  {}   /tmp/test/  \\;
*     find  /  -type  -newermt  '2018 '  !   -newermt   ''

find命令按时间查找文件

```
find /etc -atime  3    #查找/etc目录三天之内查看过的文件      
find /etc -mtime -3    #查找/etc目录三天之内修改过的文件       
find /etc mtime  +3    #查找三天前修改过的文件        
find /etc -type f -newermt '2014-1-1 00:00:00' ! -newermt '2015-1-1 00:00:00'  #查找在2014-1-1 00:00:00和 2015-1-1 00:00:00 之间的文件
```


![](https://img-blog.csdnimg.cn/20190715215430359.png)

相关文章：[Linux中grep工具的使用](https://blog.csdn.net/qq_36119192/article/details/82974781)

                 [Linux中awk工具的使用](https://blog.csdn.net/qq_36119192/article/details/82982732)

                 [Linux中sed工具的使用](https://blog.csdn.net/qq_36119192/article/details/82982708)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中