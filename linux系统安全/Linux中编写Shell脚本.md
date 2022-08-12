**目录**

[Shell](#t0)

[Shell脚本的执行](#t1)

[Shell脚本编写规范](#t2)

[Shell 中的变量](#t3)

[变量的算术运算](#t4)

[双小括号 (())  数值运算命令的用法](#t5)

[let 运算命令的用法](#t6)

[expr 命令的用法](#t7)

[br 命令的用法](#t8)

[$\[\] 符号的运算示例](#t9)

[Shell脚本的条件测试](#t10)

[几种条件测试语句](#t11)

[文件测试操作符](#t12)

[字符串测试操作符](#t13)

[整数二元比较操作符](#t14)

[逻辑操作符](#t15)

[测试表达式 test 、\[\] 、\[\[\]\] 、 (()) 的区别](#t16)

[if 条件判断语句](#t17)

[case 条件判断语句](#t18)

[for循环语句](#t19)

[while循环语句](#t20)

[Break、Continue、exit 循环控制语句](#t21)

[Shell脚本执行scrapy爬虫和python脚本](#t22)

* * *

Shell
-----

**Shell**是一个命令解释器，它的作用是解释执行用户输入的命令及程序等。 用户每输入一条命令，Shell就执行一条。这种从键盘输入命令，就可以立即得到回应的对话方式，称为交互的方式。

![](https://img-blog.csdn.net/20181008103540293?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当命令或程序语句不在命令行下执行，而是通过一个程序文件来执行时，该程序文件就被称为**Shell脚本**。 在Shell脚本里内置了很多命令、语句及循环控制，然后将这些命令一次性执行完毕，这种通过文件执行脚本的方式称为非交互的方式。 Shell脚本语言很适合用于处理纯文本型的数据，而Linux系统中几乎所有的配置文件、日志文件，以及绝大对数的启动文件都是纯文本类型的文件。  
**Shell 脚本的类型**  
Shell脚本语言是弱类型语言（无须定义变量的类型即可使用），在Unix/Linux中主要有两大类shell:   
一类是 Bourne  shell ,另一类是 C shell

1.  Bourne shell 包括 Bourne shell (sh)、Korn shell(ksh)、Bourne Again Shell 三种类型。 
2.  C shell包括csh、tcsh两种类型

查看系统默认的shell： echo  $SHELL  
查看系统支持的shell： cat  /etc/shells

![](https://img-blog.csdnimg.cn/20191227104702642.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Shell脚本的执行
----------

Shell脚本的执行通常可以采用以下几种方式。

1、bash script-name 或 sh script-name     这是当脚本文件本身没有执行权限时常使用的方法

2、path/script-name 或./script-name    在当前路径下执行脚本，需要将脚本文件的权限改为可执行。然后使用脚本的绝对路径或相对路径就可以直接执行脚本了。

3、source script-name 或 .  script-name 这种方法通常是使用source或 “.”(点号）读入或加载指定的Shell脚本文件，然后依次执行指定的Shell脚本中的语句。这些语句将在当前父 shell 脚本进程中执行（其他几种模式都会启用新的进程执行该脚本进程）。

Shell脚本编写规范
-----------

Shell脚本的开发规范及习惯非常重要，虽然这些规范不是必须要遵守的，但有了好的规范和习惯，可以大大提升开发效率，并能在后期降低对脚本的维护成本。

1、一个规范的Shell脚本在第一行会指出由哪个程序（解释器）来执行脚本中的内容，这一行内容在Linux bash的编程一般为：#！/bin/bash  或 #!  /bin/sh     bash 与 sh 的区别 , sh 为 bash的软连接，大多数情况下，脚本使用“#！/bin/bash”和“#！/bin/sh”是没有区别的，但更规范的写法是在脚本的开头使用    #！/bin/bash 

2、在shell脚本中，跟在 # 后面的内容表示注释，用来对脚本进行注释说明，注释部分不会被当做程序来执行，仅仅是给开发者和使用者看的，系统解释器是看不到的，更不会执行。注释可以自成一行，也可以跟在脚本命令的后面与命令在同一行。 注释尽量不要使用中文，在脚本中最好也不要有中文。

3、Shell脚本的开头会加版本、版权等信息

```
# Date：16:29 2018-10-20       
# Author: Create by xiaoxie      
# Description: This script function is ……       
# Version： 1.1
```


4、在shell脚本中尽量不用中文注释，尽量用英文注释，防止本机或切换系统环境后中文乱码的困扰。

5、Shell脚本的命名应以.sh为扩展名 例如：1.sh

6、成对的符号应尽量一次性写出来，然后退格在符号内增加内容，以防止遗漏。这些成对的符号包括： {}、\[\]、‘’、“”  等

7、中括号\[\]两端至少要有1个空格，因此，键入中括号时即留出空格\[  \]，然后在退格键入中间内容，并确保两端都至少由一个空格。

8、对于流程控制语句，应一次性将格式写完，再添加内容。 如：一次性完成for循环语句的格式

```
for      
do      
     内容      
done
```


9、通过缩进让代码更易读，如：

```
if 条件内容           
       then               
            内容       
fi
```


10、对于常规变量的[字符串](https://so.csdn.net/so/search?q=%E5%AD%97%E7%AC%A6%E4%B8%B2&spm=1001.2101.3001.7020)定义变量值应加双引号，并且等号前后不能有空格，需要强引用的，则用单引号（‘’），如果是命令的引用，则用反引号（\`\`）。

![](https://img-blog.csdnimg.cn/20191227105417336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

11、脚本中的单引号、双引号及反引号必须为英文状态下的符号。

Shell 中的变量
----------

简单地说，变量就是用一个固定的字符串（也可能是字符、数字等的组合）代替更多、更复杂的内容，该内容里可能还会包含变量、路径、字符串等其他内容。 变量是暂时存储数据的地方及数据标记，所存储的数据存在于内存空间中，通过正确地调用内存中变量的名字就可以读取出与变量对应的数据。

变量的赋值方法为： 先写变量名称，紧接着是 "=" ，最后是值。中间无任何空格。 通过echo命令加上  $变量名，即可输出变量的值。 双引号，以防止出错变量的值一般要加上。

定义变量时变量名建议用大写，如  A=xie     B=99

read  -p  “提示信息”   变量名      #交互式赋值方法

查看变量内容 echo $A  或  echo ${A}

![](https://img-blog.csdnimg.cn/20191227105729956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**赋值时使用引号的作用**

*   双引号：允许通过$符号引用其他变量值
*   单引号：禁止引用其他变量值，$视为普通字符
*   反撇号：命令替换，提取命令执行后的输出结果 全局变量的定义方法 export 变量名

![](https://img-blog.csdn.net/20181010193422821?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**位置参数**

位置参数是一种在调用 Shell 程序的命令行中按照各自的位置决定的变量，是在程序名之后输入的参数。 位置参数之间用空格分隔，Shell取第一个位置参数替换程序文件中的 $1，第二个替换 $2 , 依次类推。$0 是一个特殊变量，它的内容是当前这个shell程序的文件名，所以 $0 不是一个位置参数。

假如我现在有一个 1.sh脚本文件，内容如下

```
#! /bin/bash      
echo $1      
echo $(($2+$3))
```


当我执行时，我在文件名后加3个参数

![](https://img-blog.csdn.net/20181008212407723?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**预定义变量**

预定义变量和环境变量相类似，也是在Shell一开始就定义的变量，不同的是，用户只能根据shell的定义来使用这些变量，所有预定义变量都是由符号“$”和另一个符号组成。 常见的Shell预定义变量有以下几种。

*   $# ：位置参数的数量
*   $\* ：所有位置参数的内容
*   $? ：命令执行后返回的状态，0表示没有错误，非0表示有错误
*   $$ ：当前进程的进程号
*   $! ：后台运行的最后一个进程号
*   $0 ：当前执行的进程名

假如我现在有一个 1.sh脚本文件，内容如下

```
#! /bin/bash       
echo $1      
echo ${2}+${3}       
echo $#             #打印出位置参数的数量      
echo $*             #打印出位置参数的内容      
echo $?             #打印出命令执行后返回的状态      
echo $$             #打印出当前进程的进程号      1
echo $0             #打印出当前进程的进程名
```


当我执行时，我在文件名后加3个参数

![](https://img-blog.csdn.net/20181008203947438?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

参考文章：[Linux中环境变量的设置](https://blog.csdn.net/qq_36119192/article/details/82970778)

变量的算术运算
-------

**Shell中常见的算术运算符**

![](https://img-blog.csdn.net/20181008211121249?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**Shell 中常见的算术运算命令**

![](https://img-blog.csdn.net/2018100821123171?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **双小括号 (())  数值运算命令的用法**

双小括号 (()) 的作用是进行数值运算与数值比较，它的效率很高，用法灵活，是Linux下常用的运算操作符。其操作方法如下：

![](https://img-blog.csdn.net/20181008212712307?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181008213351642?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **let 运算命令的用法**

let运算命令的语法格式为： let 赋值[表达式](https://so.csdn.net/so/search?q=%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1001.2101.3001.7020)  

let 赋值表达式的功能等同于“((赋值表达式))” 范例：  给变量 i 加8

  ![](https://img-blog.csdn.net/20181008213530225?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **expr 命令的用法**

1、expr 用于运算   
   语法：expr 表达式   
   范例：expr 2 + 2 ; expr 2 – 2 ; expr 2  \\\*  2 ;  expr 2  /  2      
   注意：运算符号和数字之间要有空格！！

![](https://img-blog.csdn.net/20181008213856982?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2、expr配合变量计算

expr在Shell中可配合变量进行计算，但需要用反引号将计算表达式括起来。

![](https://img-blog.csdn.net/20181008214132952?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3、利用 expr 计算字符串的长度

![](https://img-blog.csdn.net/20181008214334655?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **br 命令的用法**

bc 是UNIX/Linux下的计算器，除了作为计算器来使用，还可以作为命令行计算工具使用

交互模式 在shell命令行直接输入 bc 及能进入bc语言的交互模式

![](https://img-blog.csdn.net/20181008214548964?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

bc也可以进行非交互式的运算，方法是与 echo 一起使用，所以我们就可以写在脚本里面

![](https://img-blog.csdn.net/2018100821473192?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### **$\[\] 符号的运算示例**

![](https://img-blog.csdn.net/20181008214956300?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Shell脚本的条件测试
------------

通常，在shell的各种条件结构和流程控制结构中都要进行各种测试，然后根据测试结果执行不同的操作，有时候也会与 if 等条件语句相结合，来完成测试判断，以减少程序运行错误。

### 几种条件测试语句

![](https://img-blog.csdn.net/20181010094446536?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 文件测试操作符

| 常用文件测试操作符 | 说明 |
| --- | --- |
| \-d   ， d的全拼为 directory | 文件存在且为目录则为真 |
| \-f   ,   f的全拼为  file | 文件存在且为文件则为真 |
| \-e ， e的全拼为 exists | 文件存在则为真 |
| \-s ，s的全拼为 size | 文件存在且大小不为0则为真 |
| \-r ，r的全拼为 read | 文件存在且可读则为真 |
| \-w ，w的全拼为write | 文件存在且可写则为真 |
| \-x ，x的全拼为executable | 文件存在且可执行则为真 |
| \-L ，L的全拼为link | 文件存在且为链接文件则为真 |
| f1 -nt  f2  ，nt的全拼为 newer than | 文件f1比文件f2新则为真 |
| f1 -ot f2 ，ot的全拼为older than | 文件f1比文件f2旧则为真 |

注：（()）不能用于文件测试，文件测试一般常用的是 \[\]

![](https://img-blog.csdn.net/20181010113625863?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 字符串测试操作符

| 常用字符串测试操作符 | 说明 |
| --- | --- |
| \-n   | 若字符串长度不为0，则为真 |
| \-z | 若字符串长度为0，则为真 |
| “字符串1”  ==  “字符串2” | 若字符串1等于字符串2，则为真 |
| “字符串1”  !=  “字符串2” | 若字符串1不等于字符串2，则为真 |

注： == 和 !=  两端要有空格  ，（()）不能用于字符测试

![](https://img-blog.csdn.net/20181010115256894?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 整数二元比较操作符

| 在\[\]以及test中使用的比较符号 | 在(())和\[\[\]\]中使用的比较符号 | 说明 |
| --- | --- | --- |
| \-eq | \== 或 = | 相等，全拼为  equal |
| \-ne | != | 
不相等，全拼为 not equal

 |
| \-gt | \> | 大于，全拼为 greater than |
| \-ge | \>= | 大于等于，全拼为 greater equal |
| \-lt | < | 小于，全拼为 less than |
| \-le | <= | 小于等于 ,全拼为less equal |

*   "="和"!="也可以在\[\]中作比较使用，但在\[\]中使用包含"<"和">"的符号时，需要用反斜线转义，有时不转义虽然语法不会报错，但是结果可能不对。
*   也可以在\[\[\]\]中使用包含“-gt”和“-lt”的符号，但是不建议使用
*   比较符号两端也要有空格，\[\] （()）  \[\[\]\]  两端都要有空格

![](https://img-blog.csdn.net/20181010102616949?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 逻辑操作符

| 在\[\]和test中使用的操作符 | 在\[\[\]\]和(())中使用的操作符 | 说明 |
| --- | --- | --- |
| \-a | && | and ，与，两端都为真，才为真 |
| \-o | || | or ，或， 两端有一个为真，就为真 |
| ! | ! | not ，非， 两端相反，则结果为真 |

![](https://img-blog.csdn.net/20181010103546925?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 测试表达式 test 、\[\] 、\[\[\]\] 、 (()) 的区别

| 测试表达式符号 | test | \[\] | \[\[\]\] | (()) |
| --- | --- | --- | --- | --- |
| 边界是否需要空格 | 需要 | 需要 | 需要 | 不需要 |
| 逻辑操作符 | ! 、-a、 -o | ! 、-a、 -o | ! 、&& 、 || | ! 、&& 、 || |
| 整数比较操作符 | \-eq 、 -gt 、-lt、-ge 、-le | \-eq 、 -gt 、-lt、-ge 、-le | \-eq 、 -gt 、-lt、-ge 、-le 或  = 、>  、< 、 >= 、 <= | \= 、>  、< 、 >= 、 <= |
| 字符串比较操作符 | \= 、 == 、!= | \= 、 == 、!= | \= 、 == 、!= | 不支持 |
| 文件操作 | \-d、-f、-e、-r、-s、-w、-x、-L、-nt、-ot | \-d、-f、-e、-r、-s、-w、-x、-L、-nt、-ot | \-d、-f、-e、-r、-s、-w、-x、-L、-nt、-ot | 不支持 |
| 是否支持通配符匹配 | 
不支持

 | 不支持 | 支持 | 不支持 |

if 条件判断语句
---------

```
#####单条件判断##############      
if  条件判断      
  then       
      命令      
else      
      命令      
fi       
#或       1
if  条件判断;then       1
     命令      1
else      1
     命令      1
fi
```


![](https://img-blog.csdn.net/20181010121335874?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181010121418549?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
###双条件判断#####      
if 条件判断      
  then      
      命令      
elif 条件判断      
  then       
      命令      
else      
   命令      1
fi       1
##或      1
if 条件判断;then      1
    命令      1
elif 条件判断;then       1
    命令      1
else      1
    命令      1
fi
```


![](https://img-blog.csdn.net/2018101012105128?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
----------------------------------------------------------------------------------------------------------------------------------------------------------------------

![](https://img-blog.csdn.net/20181010121150241?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

if语句对于字符串的匹配

![](https://img-blog.csdn.net/20181012220033255?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

case 条件判断语句
-----------

case条件语句相当于多分支的if/elif/ellse条件语句，但是它比这些条件语句看起来更规范更工整，常被应用于实现系统服务启动脚本等企业应用场景中。

```
case  变量  in      
        one)      
            命令      
;;      
        two)      
             命令      
;;      
         *)       
             命令      1
esac
```


![](https://img-blog.csdn.net/20181010122219577?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181013112216124?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

for循环语句
-------

```
for  条件      
do      
   命令      
done       
##或       
for  条件;do      
   命令      1
done
```


 ![](https://img-blog.csdn.net/20181010142228892?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181010142254358?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

while循环语句
---------

```
while  条件      
do      
   命令      
done
```


![](https://img-blog.csdn.net/20181010192208166?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Break、Continue、exit 循环控制语句
--------------------------

break 、continue在条件语句及循环语句（for、while、if等）中用于控制程序走向；而exit则用于终止所有语句并退出当前脚本。

| 命令 | 说明 |
| --- | --- |
| break   n | 如果省略 n ，则表示跳出整个循环，n 表示跳出循环的成熟 |
| continue n | 如果省略 n ，则表示跳过本次循环，忽略本次循环的剩余代码，进行循环的下一次循环。n表示退到第 n 层继续循环 |
| exit n | 退出当前 shell 程序，n 为上一次程序执行的状态返回值。n 也可以省略，在下一个 shell 里可通过  $?  接收 exit  n 的n值 |

![](https://img-blog.csdn.net/20181010194715629?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181010194834438?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181010194848500?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Shell脚本执行scrapy爬虫和python脚本
--------------------------

```
#! /bin/bash      
cd /opt/project/      
scrapy crawl xx      
python3 test.py
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中