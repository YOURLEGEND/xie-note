**目录**

[Aggressor-Script语法基础](#Aggressor-Script%E8%AF%AD%E8%A8%80%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80 "Aggressor-Script语法基础")

[变量](#t0 "变量")

[数组](#t1 "数组")

[哈希表](#t2 "哈希表")

[比较运算符](#t3 "比较运算符")

[条件判断](#t4 "条件判断")

[循环](#t5 "循环")

[函数](#t6 "函数")

[彩色输出](#t7 "彩色输出")

[在CobaltStrike脚本控制台调试](#t8 "在CobaltStrike脚本控制台调试")

* * *

作为红队人员来说，CobaltStrike是必不可少的远控。其最大的优势在于能团队协作，并且可以灵活的进行功能扩展。而Aggressor-Script语言就是给CobaltStrike(3.0之后)扩展功能的首选语言。CS3.0之后我们看到的菜单，选项、事件等都是由默认的 [default.cna](https://www.cobaltstrike.com/aggressor-script/default.cna "default.cna") 文件创建。Aggressor-Script语言是由Sleep脚本解析，Sleep是基于Java的脚本语言，Sleep语言下载地址：[http://sleep.dashnine.org/download/sleep.jar](http://sleep.dashnine.org/download/sleep.jar "http://sleep.dashnine.org/download/sleep.jar")

![](https://img-blog.csdnimg.cn/20210126140214538.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者执行在CobaltStrike中调出脚本控制台，视图 ——> 脚本控制台

![](https://img-blog.csdnimg.cn/20210126140534293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Aggressor-Script语法基础
--------------------

Aggressor-Script 语言使用 # 作为注释符。

### 变量

Aggressor-Script语言变量使用 $ 符号开头。在为变量赋值的时候，= 号两边需要添加空格，如果不添加空格，[编译器](https://so.csdn.net/so/search?q=%E7%BC%96%E8%AF%91%E5%99%A8&spm=1001.2101.3001.7020)会报错。

```
$name = "xie";  #字符串类型的变量      
$age = 20;      #数字类型的变量
```


![](https://img-blog.csdnimg.cn/20210126144517857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 数组

**定义数组**

在定义[数组](https://so.csdn.net/so/search?q=%E6%95%B0%E7%BB%84&spm=1001.2101.3001.7020)的时候，需要添加 @ 符号。

```
@test = @("a","bb","cdfc","dddd","e","123");
```


![](https://img-blog.csdnimg.cn/202101261451197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**数组的遍历**

```
@test = @("a","bb","cdfc","dddd","e","123");      
foreach $var (@test){      
	println($var);      
}
```


![](https://img-blog.csdnimg.cn/20210126214659501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**数组push**

```
@test = @("a","bb","cdfc","dddd","e","123");      
push(@test,"hello,word!");      
println(@test[-1]);
```


![](https://img-blog.csdnimg.cn/20210126214827626.png)

**数组增加**

```
@a = @(1,2,3);      
@b = @(4,5,6);      
(@a) += @b;
```


![](https://img-blog.csdnimg.cn/20210126145000143.png)

### 哈希表

哈希表使用 % 开头，键与值之间用 => 连接

```
%boy = %(name => "xie", age => "20", sex => "man");      
println(%boy);      
println(%boy["name"]);       
%boy2["name"] = "wang";      
%boy2["age"] = "18";      
%boy2["sex"] = "woman";      
println(%boy2);       
println(%boy2['sex']);
```


![](https://img-blog.csdnimg.cn/20210126214423293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 比较运算符

*   eq：等于
*   ne：不等于
*   lt：小于
*   gt：大于
*   isin：一个字符串中是否包含另一个字符串
*   iswm：一个字符串使用通配符匹配另一个字符串
*   \=~：数组比较
*   is：引用是否相等

### 条件判断

```
$a = 1;      
$b = 2;      
$c = 3;      
if ($a eq $b){      
	println("a 等于 b");      
}else if($a eq $c){      
	println("a 等于 c");      
}else{      
	println("a既不等于b也不等于c");      1
}
```


![](https://img-blog.csdnimg.cn/20210126215343669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 循环

**for循环**

```
$a = 0;      
for ($i = 1; $i lt 5 ; $i++){      
	println($a);      
	$a = $a + 1;      
}
```


![](https://img-blog.csdnimg.cn/2021012621575262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**while表达式**

```
$a = 0;      
while ($a lt 5){      
	println($a);      
	$a = $a + 1;      
}
```


![](https://img-blog.csdnimg.cn/20210126220059828.png)

**foreach语句**

```
@test = @("a","bb","cdfc","dddd","e","123");      
foreach $var (@test){      
	println($var);      
}
```


![](https://img-blog.csdnimg.cn/20210126214659501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 函数

函数用 sub 关键字申明。函数的参数为 $1、$2 等等。

```
sub add {      
	return $1."+".$2."=".($1 + $2);      
}      
$sum = add(1,2);      
println($sum);
```


### ![](https://img-blog.csdnimg.cn/20210126221135164.png)

### 彩色输出

```
println("\c0This is my color");      
println("\c1This is my color");      
println("\c2This is my color");      
println("\c3This is my color");      
println("\c4This is my color");      
println("\c5This is my color");      
println("\c6This is my color");      
println("\c7This is my color");      
println("\c8This is my color");      1
println("\c9This is my color");      1
println("\cAThis is my color");      1
println("\cBThis is my color");      1
println("\cCThis is my color");      1
println("\cDThis is my color");      1
println("\cEThis is my color");      1
println("\cFThis is my color");
```


![](https://img-blog.csdnimg.cn/20210126221650521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在CobaltStrike脚本控制台调试
--------------------

**查看帮助**

输入 help 可以查看到一些帮助。当我们自己写的cna脚本被加载后，我们自己写的功能名也会被help搜素到

```
help：查看帮助      
?：进行一个简单的判断，返回值为True或者False      
e：在执行我们的命令前加上 e      
x：执行一个计算，比如1+1什么的，这里需要注意，两个数字之间需要隔开，不然会报错      
load：加载cna脚本      
reload：重载cna脚本      
unload：卸载cna脚本      
ls：查看当前加载的cna脚本      
troff：关闭函数跟踪，也就是我们不显示函数运行的具体情况      1
tron：开启函数跟踪，显示我们运行时的具体情况      1
proff：禁止 cna 脚本运行Sleep的语法      1
profile：统计 cna 脚本使用了哪些 Sleep的语法      1
pron：运行 cna 脚本运行Sleep的语法
```


![](https://img-blog.csdnimg.cn/20210126141758723.png)

**条件判断**

```
? int(1) == int(1)
```


![](https://img-blog.csdnimg.cn/20210126141129977.png)

**执行代码**

执行我们的命令，注意，需要在我们的命令前面加上 e

```
e println("hello,word")
```


![](https://img-blog.csdnimg.cn/20210126141006563.png)

**加载、重载、卸载cna脚本**

```
load e:\test.cna  #加载的时候需要指定路径       
加载完以后的重载和卸载都不需要指定路径      
unload test.cna      
reload test.cna      
ls
```


![](https://img-blog.csdnimg.cn/20210126142406957.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**开启和关闭函数跟踪**

```
tron test.cna   #开启函数跟踪      
troff test.cna  #关闭函数跟踪
```


![](https://img-blog.csdnimg.cn/20210126142931646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：

[CobaltStrike 插件编写指南](https://mp.weixin.qq.com/s/i7QzwMAmUyxoBs0CwcGC-g "CobaltStrike 插件编写指南")

相关文章：[CobaltStrike加载插件](https://xie1997.blog.csdn.net/article/details/106088279 "CobaltStrike加载插件")

                 [Aggressor Script Tutorial and Reference](https://www.cobaltstrike.com/aggressor-script/index.html "Aggressor Script Tutorial and Reference")