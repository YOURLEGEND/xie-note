**目录**

[JAVA中的数据类型](#t0)

[内置数据类型](#t1)

[引用数据类型](#t2) 

[数组对象](#t3)

[字符串对象](#t4)

[int和Integer的区别](#t5)

[Java常量](#t6)

[Java中的自动类型转换](#t7)

* * *

JAVA中的数据类型
==========

变量就是申请[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)来存储值。也就是说，当创建变量的时候，需要在内存中申请空间。内存管理系统根据变量的类型为变量分配存储空间，分配的空间只能用来储存该类型数据。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxMy8xMi9tZW1vcnlwaWMxLmpwZw?x-oss-process=image/format,png)

因此，通过定义不同类型的变量，可以在内存中储存整数、小数或者字符。

Java 的两大数据类型:

*   内置数据类型：Java语言提供了八种基本类型：六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。
*   引用数据类型

内置数据类型
------

![](https://img-blog.csdnimg.cn/20190928221304737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Java中的整数数据类型有byte、short、int、long，分别对应1、2、4、8字节长度，范围与C语言一致，但是java中没有unsigned类型。Java中的小数数据类型有 float 和 double，与 C 语言一致，但是 float 有区别， 例如：float  b = 123.456f；Java中的char只能存储一个字符；Java里的布尔类型boolean 有两个字面常量 true 、false 。Java 中的 boolean 类型与整型不通用。

**byte：**

*   byte 数据类型是8位、有符号的，以二进制补码表示的整数；
*   最小值是 -128（-2^7）；
*   最大值是 127（2^7-1）；
*   默认值是 0；
*   byte 类型用在大型数组中节约空间，主要代替整数，因为 byte 变量占用的空间只有 int 类型的四分之一；
*   例子：byte a = 100，byte b = -50。

**short：**

*   short 数据类型是 16 位、有符号的以二进制补码表示的整数
*   最小值是 -32768（-2^15）；
*   最大值是 32767（2^15 - 1）；
*   Short 数据类型也可以像 byte 那样节省空间。一个short变量是int型变量所占空间的二分之一；
*   默认值是 0；
*   例子：short s = 1000，short r = -20000。

**int：**

*   int 数据类型是32位、有符号的以二进制补码表示的整数；
*   最小值是 -2,147,483,648（-2^31）；
*   最大值是 2,147,483,647（2^31 - 1）；
*   一般地整型变量默认为 int 类型；
*   默认值是 0 ；
*   例子：int a = 100000, int b = -200000。

**long：**

*   long 数据类型是 64 位、有符号的以二进制补码表示的整数；
*   最小值是 -9,223,372,036,854,775,808（-2^63）；
*   最大值是 9,223,372,036,854,775,807（2^63 -1）；
*   这种类型主要使用在需要比较大整数的系统上；
*   默认值是 0L；
*   例子： long a = 100000L，Long b = -200000L。  
    "L"理论上不分大小写，但是若写成"l"容易与数字"1"混淆，不容易分辩。所以最好大写。

**float：**

*   float 数据类型是单精度、32位、符合IEEE 754标准的浮点数；
*   float 在储存大型浮点数组的时候可节省内存空间；
*   默认值是 0.0f；
*   浮点数不能用来表示精确的值，如货币；
*   例子：float f1 = 234.5f。

**double：**

*   double 数据类型是双精度、64 位、符合IEEE 754标准的浮点数；
*   浮点数的默认类型为double类型；
*   double类型同样不能表示精确的值，如货币；
*   默认值是 0.0d；
*   例子：double d1 = 123.4。

**boolean：**

*   boolean数据类型表示一位的信息；
*   只有两个取值：true 和 false；
*   这种类型只作为一种标志来记录 true/false 情况；
*   默认值是 false；
*   例子：boolean one = true。

**char：**

*   char类型是一个单一的 16 位 Unicode 字符；
*   最小值是 \\u0000（即为0）；
*   最大值是 \\uffff（即为65,535）；
*   char 数据类型可以储存任何字符；
*   例子：char letter = 'A';。

| **数据类型** | **默认值** |
| --- | --- |
| byte | 0 |
| short | 0 |
| int | 0 |
| long | 0L |
| float | 0.0f |
| double | 0.0d |
| char | 'u0000' |
| boolean | false |

![](https://img-blog.csdnimg.cn/20190928222556195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

| **数据类型** | 包装类 | 长度 | **默认值** |
| --- | --- | --- | --- |
| byte | java.lang.Byte | 1个字节（-128-127） | 0 |
| short | java.lang.Short | 2个字节（-32768-32767） | 0 |
| int | java.lang.Integer | 4个字节（-2147483648-2147483647） | 0 |
| long | java.lang.Long | 8个字节（-9223372036854775808-9223372036854775807） | 0L |
| float | java.lang.Float | 4个字节（1.4E-45-3.4028235E38） | 0.0f |
| double | java.lang.Double | 8个字节（4.9E-324-1.7976931348623157E308） | 0.0d |
| char | java.lang.Character | 2个字节（0-65535） | 'u0000' |
| String (or any object) | java.lang.String |   | null |
| boolean | java.lang.Boolean |   | false |

所有的包装类**（Integer、Long、Byte、Double、Float、Short）**都是抽象类 Number 的子类，Number 类属于 java.lang 包。传送门：[Java Number & Math 类](https://www.runoob.com/java/java-number.html)  、 [Java Character 类](https://www.runoob.com/java/java-character.html)  、 [Java String 类](https://www.runoob.com/java/java-string.html)  、 [Java StringBuffer 和 StringBuilder 类](https://www.runoob.com/java/java-stringbuffer.html)

![Java Numberç±»](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxMy8xMi9udW1iZXIxLnBuZw?x-oss-process=image/format,png)

引用数据类型 
-------

*   在Java中，引用类型的变量非常类似于 C/C++ 的指针。引用类型指向一个对象，指向对象的变量是引用变量。这些变量在声明时被指定为一个特定的类型，比如 Employee、Puppy 等。变量一旦声明后，类型就不能被改变了。
*   对象、数组都是引用数据类型。
*   所有引用类型的默认值都是 null。
*   一个引用变量可以用来引用任何与之兼容的类型。

例子：Site site = new Site("Runoob")；

### 数组对象

相关文章：[Java中的数组对象](https://mp.csdn.net/console/editor/html/107171074)

### 字符串对象

相关文章：[Java中的字符串对象](https://xie1997.blog.csdn.net/article/details/107171358)

int和Integer的区别
==============

*   Integer是int的包装类，int 则是java的一种基本数据类型 
*   Integer变量必须实例化后才能使用，而int变量不需要 
*   Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向此对象；而int则是直接存储数据值 
*   Integer的默认值是null，int的默认值是0

关于Integer和int的比较   
1、由于Integer变量实际上是对一个Integer对象的引用，所以两个通过new生成的Integer变量永远是不相等的（因为new生成的是两个对象，其内存地址不同）。

```
Integer i = new Integer(100);      
Integer j = new Integer(100);      
if(i==j){      
    System.out.println("相等");      
}else{      
    System.out.println("不相等");      
}
```


![](https://img-blog.csdnimg.cn/20200708001653505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

2、Integer变量和int变量比较时，只要两个变量的值是向等的，则结果为true（因为包装类Integer和基本数据类型int比较时，java会自动拆包装为int，然后进行比较，实际上就变为两个int变量的比较）

```
Integer m = new Integer(100);      
int n = 100;      
if(i==j){      
   System.out.println("相等");      
}else{      
   System.out.println("不相等");      
}
```


![](https://img-blog.csdnimg.cn/20200708002111576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

3、非new生成的Integer变量和new Integer()生成的变量比较时，结果为false。（因为 ①当变量值在-128~127之间时，非new生成的Integer变量指向的是java常量池中的对象，而new Integer()生成的变量指向堆中新建的对象，两者在内存中的地址不同；②当变量值在-128~127之间时，非new生成Integer变量时，java API中最终会按照new Integer(i)进行处理（参考下面第4条），最终两个Interger的地址同样是不相同的）

```
Integer i = new Integer(100);      
Integer j = 100;      
System.out.print(i == j); //false
```


4、对于两个非new生成的Integer对象，进行比较时，如果两个变量的值在区间-128到127之间，则比较结果为true，如果两个变量的值不在此区间，则比较结果为false

```
Integer i = 100;      
Integer j = 100;      
System.out.print(i == j); //true
```

```
Integer i = 128;      
Integer j = 128;      
System.out.print(i == j); //false
```


对于第4条的原因：   
java在编译Integer i = 100 ;时，会翻译成为Integer i = Integer.valueOf(100)；，而java API中对Integer类型的valueOf的定义如下：

```
public static Integer valueOf(int i){      
    assert IntegerCache.high >= 127;      
    if (i >= IntegerCache.low && i <= IntegerCache.high){      
        return IntegerCache.cache[i + (-IntegerCache.low)];      
    }      
    return new Integer(i);      
}
```


java对于-128到127之间的数，会进行缓存，Integer i = 127时，会将127进行缓存，下次再写Integer j = 127时，就会直接从缓存中取，就不会new了

Java常量
======

常量在程序运行时是不能被修改的。

在 Java 中使用 final 关键字来修饰常量，声明方式和变量类似，虽然常量名也可以用小写，但为了便于识别，通常使用大写字母表示常量。

```
final double PI = 3.1415927;
```


Java中的自动类型转换
============

整型、实型（常量）、字符型数据可以混合运算。运算中，不同类型的数据先转化为同一类型，然后进行运算。

转换从低级到高级。

```
低  ------------------------------------>  高      
byte,short,char—> int —> long—> float —> double
```


数据[类型转换](https://so.csdn.net/so/search?q=%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2&spm=1001.2101.3001.7020)必须满足如下规则：

*   不能对boolean类型进行类型转换。
    
*   不能把对象类型转换成不相关类的对象。
    
*   在把容量大的类型转换为容量小的类型时必须使用强制类型转换。
    
*   浮点数到整数的转换是通过舍弃小数得到，而不是四舍五入，例如：
    
    ```
(int)23.7 == 23;              
(int)-45.89f == -45
```

    

**自动类型转换**

必须满足转换前的数据类型的位数要低于转换后的数据类型，例如: short数据类型的位数为16位，就可以自动转换位数为32的int类型，同样float数据类型的位数为32，可以自动转换为64位的double类型。![](https://img-blog.csdnimg.cn/2019092822413127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**强制类型转换**

*   条件是转换的数据类型必须是兼容的。
    
*   格式：(type)value type是要强制类型转换后的数据类型 
    

![](https://img-blog.csdnimg.cn/20190928224617394.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：强制类型转换后，如果超过类型的值，该数值就会发生变化。

本文代码：链接: [https://pan.baidu.com/s/1iHSFSktzBWOJp210Gcsrkw](https://pan.baidu.com/s/1iHSFSktzBWOJp210Gcsrkw) 提取码: itk8

参考文章：[Java基本数据类型 | 菜鸟教程](https://www.runoob.com/java/java-basic-datatypes.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-f6d0c186d3bc470c85f676ba83af9979)[Java中的变量与常量](https://edu.csdn.net/skill/java/java-f6d0c186d3bc470c85f676ba83af9979)[变量的数据类型](https://edu.csdn.net/skill/java/java-f6d0c186d3bc470c85f676ba83af9979)44190 人正在系统学习中