**目录**

[循环语句](#t0)

[While循环](#t1)

[do...While循环](#t2)

[for循环](#t3)

[增强型for语句](#t4)

[条件语句](#t5)

[if..else语句](#t6)

[if...else if...else 语句](#t7)

[嵌套的 if…else 语句](#t8)

[switch case 语句](#t9)

[break 关键字](#t10)

[continue 关键字](#t11)

* * *

循环语句
----

Java中[循环语句](https://so.csdn.net/so/search?q=%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5&spm=1001.2101.3001.7020)有三种

*   **while** 循环
*   **do…while** 循环
*   **for** 循环

### While循环

```
while( 布尔表达式，为真执行循环内容，为假退出循环 ) {      
  //循环内容      
}
```


![](https://img-blog.csdnimg.cn/20191011223732760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### do...While循环

对于 while 语句而言，如果不满足条件，则不能进入循环。但有时候我们需要即使不满足条件，也至少执行一次。

do…while 循环和 while 循环相似，不同的是，do…while 循环至少会执行一次。

```
do {      
       //代码语句      
}while(布尔表达式，为真执行循环内容，为假退出循环);
```


### ![](https://img-blog.csdnimg.cn/20191011224208956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### for循环

虽然所有循环结构都可以用 while 或者 do...while表示，但 Java 提供了另一种语句 —— for 循环，使一些循环结构变得更加简单。

关于 for 循环有以下几点说明：

*   最先执行初始化步骤。可以声明一种类型，但可初始化一个或多个循环控制变量，也可以是空语句。
*   然后，检测布尔表达式的值。如果为 true，循环体被执行。如果为false，循环终止，开始执行循环体后面的语句。
*   执行一次循环后，更新循环控制变量。
*   再次检测布尔表达式。循环执行上面的过程。

for循环执行的次数是在执行前就确定的。语法格式如下：

```
for(初始化; 布尔表达式; 更新) {       
    //代码语句      
}
```


![](https://img-blog.csdnimg.cn/20191011225209120.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 增强型for语句

Java5 引入了一种主要用于数组的增强型 for 循环。

Java 增强 for 循环语法格式如下:

```
for(声明语句 : 表达式)      
{      
   //代码句子      
}
```


*   声明语句**：**声明新的局部变量，该变量的类型必须和数组元素的类型匹配。其作用域限定在循环语句块，其值与此时数组元素的值相等。
*   表达式**：**表达式是要访问的数组名，或者是返回值为数组的方法。

![](https://img-blog.csdnimg.cn/20191011225516797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

条件语句
----

### if..else语句

```
if(布尔表达式){      
   //如果布尔表达式的值为true      
}else{      
   //如果布尔表达式的值为false      
}
```


![](https://img-blog.csdnimg.cn/20191016220036633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### if...else if...else 语句

if 语句后面可以跟 else if…else 语句，这种语句可以检测到多种可能的情况。

使用 if，else if，else 语句的时候，需要注意下面几点：

*   if 语句至多有 1 个 else 语句，else 语句在所有的 else if 语句之后。
*   if 语句可以有若干个 else if 语句，它们必须在 else 语句之前。
*   一旦其中一个 else if 语句检测为 true，其他的 else if 以及 else 语句都将跳过执行。

```
if(布尔表达式 1){      
   //如果布尔表达式 1的值为true执行代码      
}else if(布尔表达式 2){      
   //如果布尔表达式 2的值为true执行代码      
}else if(布尔表达式 3){      
   //如果布尔表达式 3的值为true执行代码      
}else {      
   //如果以上布尔表达式都不为true执行代码      
}
```


![](https://img-blog.csdnimg.cn/20191016220326312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 嵌套的 if…else 语句

使用[嵌套](https://so.csdn.net/so/search?q=%E5%B5%8C%E5%A5%97&spm=1001.2101.3001.7020)的 if…else 语句是合法的。也就是说你可以在另一个 if 或者 else if 语句中使用 if 或者 else if 语句。

```
if(布尔表达式 1){      
   如果布尔表达式 1的值为true执行代码      
   if(布尔表达式 2){      
      如果布尔表达式 2的值为true执行代码      
   }      
}
```


### ![](https://img-blog.csdnimg.cn/20191016220502240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### switch case 语句

switch case 语句判断一个变量与一系列值中某个值是否相等，每个值称为一个分支。

```
switch(expression){      
    case value :      
       //语句      
       break; //可选      
    case value :      
       //语句      
       break; //可选      
    //你可以有任意数量的case语句      
    default : //可选      1
       //语句      1
}
```


switch case 语句有如下规则：

*   switch 语句中的变量类型可以是： byte、short、int 或者 char。从 Java SE 7 开始，switch 支持字符串 String 类型了，同时 case 标签必须为字符串常量或字面量。
    
*   switch 语句可以拥有多个 case 语句。每个 case 后面跟一个要比较的值和冒号。
    
*   case 语句中的值的数据类型必须与变量的数据类型相同，而且只能是常量或者字面常量。
    
*   当变量的值与 case 语句的值相等时，那么 case 语句之后的语句开始执行，直到 break 语句出现才会跳出 switch 语句。
    
*   当遇到 break 语句时，switch 语句终止。程序跳转到 switch 语句后面的语句执行。case 语句不必须要包含 break 语句。如果没有 break 语句出现，程序会继续执行下一条 case 语句，直到出现 break 语句。
    
*   switch 语句可以包含一个 default 分支，该分支一般是 switch 语句的最后一个分支（可以在任何位置，但建议在最后一个）。default 在没有 case 语句的值和变量值相等的时候执行。default 分支不需要 break 语句。
    

**switch case 执行时，一定会先进行匹配，匹配成功返回当前 case 的值，再根据是否有 break，判断是否继续输出，或是跳出判断。**

![](https://img-blog.csdnimg.cn/20191016222445677.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 参考文章：[Java switch case 语句](https://www.runoob.com/java/java-switch-case.html)

break 关键字
---------

break 主要用在循环语句或者 switch 语句中，用来跳出整个语句块。

break 跳出最里层的循环，并且继续执行该循环下面的语句。

![](https://img-blog.csdnimg.cn/20191011225828599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

continue 关键字
------------

continue 适用于任何循环控制结构中。作用是让程序立刻跳转到下一次循环的迭代。

在 for 循环中，continue 语句使程序立即跳转到更新语句。

在 while 或者 do…while 循环中，程序立即跳转到布尔表达式的判断语句。

![](https://img-blog.csdnimg.cn/20191011225933103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-6593228b35c043ffb4616789d1b13d18)[选择结构语句](https://edu.csdn.net/skill/java/java-6593228b35c043ffb4616789d1b13d18)[if条件语句](https://edu.csdn.net/skill/java/java-6593228b35c043ffb4616789d1b13d18)44190 人正在系统学习中