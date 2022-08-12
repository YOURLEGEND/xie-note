### Grep

**grep**（Globel Search Regular Expression and Printing out the line）全面搜索[正则表达式](https://so.csdn.net/so/search?q=%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F&spm=1001.2101.3001.7020)并把行打印出来，是一种强大的文本搜索工具，是一个对行进行操作的搜索工作，它能使用正则表达式搜索文本，并把匹配的行打印出来。Unix 的 grep 家族包括 grep 、egrep 、 fgrep 。egrep 表示扩展的 [grep](https://so.csdn.net/so/search?q=grep&spm=1001.2101.3001.7020) ，相比 grep 支持更多的元字符。grep -E  相当于 egrep 。fgrep 是 fast grep ，不支持元字符，但是搜索速度更快。grep 过滤的结果被送到屏幕，不影响原文件内容。

grep的用法：**grep    选项     模式    文件**

grep不加引号直接过滤字符串 ；grep在进行模式匹配的时候必须加引号，单引号和双引号都可以 ；grep在引用变量的时候必须加双引号

grep的选项

*   \-r   ： 递归搜索
*   \-v   ：反向选取
*   \-o   ： 只显示被模式匹配到的字符串，而不是整个行
*   \-i   ：  匹配时不区分大小写
*   \-A  5 ： 显示匹配到的行时，显示后面的 5 行
*   \-B  5  ： 显示匹配到的行时，前面的 5 行
*   \-C  5  ： 显示匹配到的行时，前后的 5 行
*   \-E ： 使用扩展的正则表达式

![](https://img-blog.csdn.net/20181008224140859?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**grep支持基本正则表达式： [基本正则表达式](https://blog.csdn.net/qq_36119192/article/details/82905422#%E5%9F%BA%E6%9C%AC%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)**

**grep -E 也支持扩展正则表达式：[扩展正则表达式](https://blog.csdn.net/qq_36119192/article/details/82905422#%E6%89%A9%E5%B1%95%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)**

### **grep 支持的字符**

*   \[:digit:\] ：  所有数字，相当于 0-9  或者 \\d
*   \[:lower:\] ：所有的小写字母
*   \[:upper:\]：所有的大写字母
*   \[:alpha:\] ：所有的字母
*   \[:alnum:\] ：相当于\[0-9a-zA-Z\]
*   \[:space:\] ：空白字符，相当于 \\s
*   \[:punct:\] ：所有标点符号

### **grep -E 或  egrep 支持的字符**

*   \\s：匹配任何空白字符，包括空格、制表符、换页符等，与\[ \\f\\n\\r\\t\\v \] 等效
*   \\S：匹配任何非空白字符，与 \[ ^\\f\\n\\r\\t\\v \] 等效
*   \\w：匹配任何字类字符，包括下划线，与 \[A-Za-z0-9\_\] 等效
*   \\W：匹配任何非单词字符，与\[ ^A-Za-z0-9\_\] 等效

### **grep -P 支持的字符**

*   \\d ：数字字符匹配，等效于 \[0-9\]

**举例**

```
1、匹配 eg.text 中以 # 开头，且后面跟了至少一个空白字符，而后又跟了任意非空白字符的行      
grep "^#[[:space:]]\{1,\}[^[:space:]]" eg.text          
拆开 grep "^# [[:space:]] \{1,\} [^[:space:]]" eg.text      
grep -E "^#\s\{1,\}\S" eg.text      
拆开  grep -E "^# \s \{1,\} \S" eg.text       
2、匹配 eg.text 中以 包含了 :一个数字:  的行      
grep ":[[:digit:]]:" eg.text      
拆开 grep ": [[:digit:]] :" eg.text      1
grep -P ":\d:" eg.text      1
拆开 grep -P ": \d :" eg.text
```


![](https://img-blog.csdn.net/20181009115634403?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**分组的例子**

```
grep "l..e.*l..er" test.txt       
grep "\(l..e\).*\1r" test.txt
```


![](https://img-blog.csdn.net/20181009120554810?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**匹配 1-255 的数字**

```
grep -E "\<([1-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])\>" num.txt
```


![](https://img-blog.csdn.net/20181009121405598?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**匹配Email地址：任意长度数字字母@任意长度数字字母**

```
grep -E "^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$" email.txt
```


![](https://img-blog.csdn.net/20181009122236515?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**匹配手机号码，把手机号码是1\[3|4|5|8\]后面接9位数字的过滤出来**

```
grep -E "\<1[3|4|5|8][0-9]{9}\>" tel.txt
```


![](https://img-blog.csdn.net/20181009140314929?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-66baba204f5042a79ebc82bc2a26ae32)[Linux实用命令](https://edu.csdn.net/skill/gml/gml-66baba204f5042a79ebc82bc2a26ae32)[grep命令](https://edu.csdn.net/skill/gml/gml-66baba204f5042a79ebc82bc2a26ae32)10949 人正在系统学习中