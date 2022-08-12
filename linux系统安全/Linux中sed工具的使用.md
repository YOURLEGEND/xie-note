**目录**

[Sed](#t0)

[以行为单位的替换](#t1)

[定界符 /](#t2)

[删除操作： d](#t3)

[多点编辑：-e](#t4)

[从文件读入： r 命令](#t5)

[写入文件： w 命令](#t6)

[插入](#t7) 

[相关文章：Linux Sed命令](#t8)

* * *

### Sed

**sed** 是一种流编辑器，它是文本处理中非常重要的工具，能够完美的配合[正则表达式](https://so.csdn.net/so/search?q=%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1001.2101.3001.7020)使用，功能不同凡响。 处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。

**注：sed命令不对原文件进行改变，除非使用 -i 参数；sed命令是一行一行处理的**

**sed命令的使用**

sed  参数   命令    文件

参数

*   \-f     脚本文件   以选项中指定的脚本文件来处理输入的文本文件
*   \-e    脚本   以选项中指定的脚本来处理输入的文本文件
*   \-n    仅显示处理了的行

命令

*   a\\    在当前行下面插入文本
*   i\\     在当前行上面插入文本
*   d     删除选择的行
*   s    替换指定字符
*   p    打印模板块的行
*   r  file     从file中读行
*   w file    写入file文件中
*   i   修改原文件内容

### 行的打印显示

默认显示文件所有的内容

```
sed  '/root/ROOT/p'  /etc/passwd
```


只显示发生了改变的行

```
sed  -n '/root/ROOT/p'  /etc/passwd
```


查看指定行或指定几行

```
查看第10行的内容      
sed  -n  '10p'  /etc/passwd         
查看5-10行的内容             
sed  -n '5,10p'  /etc/passwd
```


### 以字符为单位的替换

将文件中的root替换为ROOT，只替换每行的第一个

```
sed  's/root/ROOT/' /etc/passwd
```


将文件中的所有root替换为ROOT

```
sed  's/root/ROOT/g' /etc/passwd
```


将文件中每行的第2个root开始替换为ROOT

```
sed  's/root/ROOT/2g'  /etc/passwd
```


### 以行为单位的替换

将文件的1-5行替换为 i very happy 

```
sed '1,5c  i very  happy'   test
```


将以root开头的行替换为自己定义的行(在原位置进行行的替换)

```
sed '/^root/c\root user login' /etc/passwd
```


![](https://img-blog.csdn.net/20181011144212888?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 数据的搜寻并执行命令

搜索/etc/passwd,找到含有 root 的行，执行后面花括号中的一组命令，这里把bash替换为nologin

```
这里会找到所有含有root的行，然后将第一个root换成ROOT      
sed -n '/root/{s/bash/ROOT/p}' /etc/passwd      
如果要将所有的root都换成ROOT      
sed -n '/root/{s/bash/ROOT/pg}' /etc/passwd       
找到root开头的行，将bash换成nologin      
sed -n '/^root/{s/bash/ROOT/p}' /etc/passwd      
如果要将所有的root都换成ROOT      1
sed -n '/^root/{s/bash/ROOT/pg}' /etc/passwd
```


### 定界符 /

在sed中使用 / 作为定界符，也可以使用任意的定界符，如：

*   sed  's:root:ROOT:g'
*   sed  's|root|ROOT|g'

当定界符出现在样式内部时，需要对其进行转义：

### 删除操作： d

*   删除空白行：  sed  '/^$/d'   test
*   删除文件的第2行： sed '2d' test
*   删除文件的第2行到末尾所有的行：sed '2,$d'  test
*   删除文件最后一行： sed  '$d'   test
*   删除文件中所有开头是root的行： sed  '/^root/d'  test
*   删除文件中含有root的行： sed '/root/d'  test
*   删除奇数行显示偶数行： sed '1~2d'  test

### 多点编辑：-e

\-e 选项允许在同一行里执行多条命令

sed -e '1,5d'  -e  's/root/ROOT/g'  /etc/passwd            先删除文件中的1-5行，然后将剩余的行中所有的root替换为ROOT

### 从文件读入： r 命令

file里的内容被读进来，显示在与test匹配的行后面，如果匹配多行，则file的内容将显示在所有匹配行的下面：

sed '/test/r file' filename

### 写入文件： w 命令

在example中所有包含test的行都被写入file里： sed  -n  '/test/w  file'  example

### 插入 

插入(行下)  a\\  或 a

将 this is a test line 追加到 以test 开头的行后面：

```
sed '/^test/a\this is a test line'    test         
sed '/^test/a this is a test line'    test
```


在 test 文件第2行之后插入 this is a test line：

```
sed   '2a\this is a test line'     test        
sed   '2a  this is a  test  line'  test
```


插入(行上)  i\\  或   i

将 this is a test line 追加到以test开头的行前面：

```
sed '/^test/i\this is a test line'    test      
sed '/^test/i this is a test line'    test
```


在文件第5行之前插入this is a test line：

```
sed  '5i\this is  a  test line'    test         
sed  '5i this is  a  test line'    test
```


插入 （行首和行尾）

在所有行的行首和行尾添加

```
sed 's/^/HEAD/g' test   #在test文件中所有行的行首加HEAD      
sed 's/$/TAIL/g' test   #在test文件中所有行的行尾加TAIL
```


在指定行的行首和行尾添加

```
sed '5s/^/HEAD/'  test     #在test文件中第5行的行首添加HEAD      
sed '5s/$/TAIL/'  test     #在test文件中第5行的行尾添加TAIL
```


过滤指定行，然后在过滤行的行首和行尾添加

```
sed '/^ONBOOT/s/^/HEAD/' ifcfg   #在以ONBOOT开头的行首加 HEAD      
sed '/^ONBOOT/s/$/TAIL/' ifcfg   #在以ONBOOT开头的行尾加 TAIL
```


### 相关文章：[Linux Sed命令](http://www.runoob.com/linux/linux-comm-sed.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-950dfde5ccf94f15a5316cabdfa72da4)[Linux实用命令](https://edu.csdn.net/skill/gml/gml-950dfde5ccf94f15a5316cabdfa72da4)[sed命令](https://edu.csdn.net/skill/gml/gml-950dfde5ccf94f15a5316cabdfa72da4)10949 人正在系统学习中