**目录**

[JAVA中的基本语法](#t0)

[Java 标识符](#t1)

[Java默认关键字](#t2)

[Java注释符](#t3)

[Java 源程序与编译型运行区别](#t4)

[Java源文件声明规则](#t5)

* * *

### JAVA中的基本语法

编写 Java 程序时，应注意以下几点：

*   大小写敏感：Java 是大小写敏感的，这就意味着标识符 Hello 与 hello 是不同的。
*   类名命名规则：对于所有的类来说，类名的首字母应该大写。如果类名由若干单词组成，那么每个单词的首字母应该大写，例如 MyFirstJavaClass。
*   方法名：所有的方法名都应该以小写字母开头。如果方法名含有若干单词，则后面的每个单词首字母大写。
*   源文件名：源文件名必须和类名相同。当保存文件的时候，你应该使用类名作为文件名保存（切记 Java 是大小写敏感的），文件名的后缀为 .java。（如果文件名和类名不相同则会导致编译错误）。
*   主方法入口：所有的 Java 程序由 public static void main(String \[\]args) 方法开始执行。
*   java的每一行后面都必须有 ；。

![](https://img-blog.csdnimg.cn/20190928215816426.png)

### Java 标识符

Java 所有的组成部分都需要名字。类名、变量名以及方法名都被称为[标识符](https://so.csdn.net/so/search?q=%E6%A0%87%E8%AF%86%E7%AC%A6&spm=1001.2101.3001.7020)。

关于 Java 标识符，有以下几点需要注意：

*   所有的标识符都应该以字母（A-Z 或者 a-z）,美元符（$）、或者下划线（\_）开始
*   首字符之后可以是字母（A-Z 或者 a-z）,美元符（$）、下划线（\_）或数字的任何字符组合
*   关键字不能用作标识符
*   标识符是大小写敏感的
*   合法标识符举例：age、$salary、\_value、\_\_1\_value
*   非法标识符举例：123abc、-salary

### Java默认关键字

这些保留关键字不能用于常量、变量、和任何标识符的名称。

| 类别 | 关键字 | 说明 |
| --- | --- | --- |
| 访问控制 | private | 私有的 |
| protected | 受保护的 |
| public | 公共的 |
| 类、方法和变量修饰符 | abstract | 声明抽象 |
| class | 类 |
| extends | 扩充,继承 |
| final | 最终值,不可改变的 |
| implements | 实现（接口） |
| interface | 接口 |
| native | 本地，原生方法（非 Java 实现） |
| new | 新,创建 |
| static | 静态 |
| strictfp | 严格,精准 |
| synchronized | 线程,同步 |
| transient | 短暂 |
| volatile | 易失 |
| 程序控制语句 | break | 跳出循环 |
| case | 定义一个值以供 switch 选择 |
| continue | 继续 |
| default | 默认 |
| do | 运行 |
| else | 否则 |
| for | 循环 |
| if | 如果 |
| instanceof | 实例 |
| return | 返回 |
| switch | 根据值选择执行 |
| while | 循环 |
| 错误处理 | assert | 断言表达式是否为真 |
| catch | 捕捉异常 |
| finally | 有没有异常都执行 |
| throw | 抛出一个异常对象 |
| throws | 声明一个异常可能被抛出 |
| try | 捕获异常 |
| 包相关 | import | 引入 |
| package | 包 |
| 基本类型 | boolean | 布尔型 |
| byte | 字节型 |
| char | 字符型 |
| double | 双精度浮点 |
| float | 单精度浮点 |
| int | 整型 |
| long | 长整型 |
| short | 短整型 |
| 变量引用 | super | 父类,超类 |
| this | 本类 |
| void | 无返回值 |
| 保留关键字 | goto | 是关键字，但不能使用 |
| const | 是关键字，但不能使用 |
| null | 空 |

### Java注释符

类似于 C/C++、Java 也支持单行以及多行注释。注释中的字符将被 Java 编译器忽略。 空白行或者有注释的行，Java 编译器都会忽略掉。

```
public class HelloWorld {      
   /* 这是第一个Java程序      
    *它将打印Hello World      
    * 这是一个多行注释的示例      
    */      
    public static void main(String []args){      
       // 这是单行注释的示例      
       /* 这个也是单行注释的示例 */      
       System.out.println("Hello World");       1
    }      1
}
```


### Java中的枚举enum

java里的三种类型定义：

*   class  
*   interface   
*   enum

用enum来定义枚举类型，即定义一些常量

```
package com.company;       
public class Main {      
    public enum weekday{      
        SUNDAY,MONDAY,TUESDAY,WEDNESDAY,THURSDAY,FRIDAY,SATURDAY      
    }      
    public static void main(String[] args) {      
        weekday day=weekday.MONDAY;      
        if(day==weekday.MONDAY){      1
            System.out.println("相等");      1
        }      1
        //switch中，在case语句中不加weekday前缀      1
        switch (day){      1
            case SUNDAY:;      1
            case SATURDAY:      1
                System.out.println("SATURDAY");      1
                break;      1
            default:      1
                System.out.println("default");      2
                break;      2
        }      2
        //enum to string      2
        System.out.println("the day is:"+day.toString());       2
        //String to enum      2
        weekday day2=weekday.valueOf("TUESDAY");      2
        System.out.println(day2);       2
        //enum to int      3
        int index=day.ordinal();      3
        System.out.println(index);       3
        //int to enum      3
        weekday[] options=weekday.values();      3
        weekday which = options[index];      3
        System.out.println(which);       3
        //取得所有可能的值      3
        for(weekday d:weekday.values()){      4
            System.out.println(d);      4
        }      4
    }      4
}
```


### Java 源程序与编译型运行区别

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxMy8xMi9aU1NETWxkLnBuZw?x-oss-process=image/format,png)

### Java源文件声明规则

**Java包：**包主要用来对类和接口进行分类。当开发Java程序时，可能编写成百上千的类，因此很有必要对类和接口进行分类。

**import语句**：在Java中，如果给出一个完整的限定名，包括包名、类名，那么Java编译器就可以很容易地定位到源代码或者类。Import语句就是用来提供一个合理的路径，使得编译器可以找到某个类。

当在一个源文件中定义多个类，并且还有import语句和package语句时，要特别注意这些规则。

*   一个源文件中只能有一个public类
*   一个源文件可以有多个非public类
*   源文件的名称应该和public类的类名保持一致。例如：源文件中public类的类名是Employee，那么源文件应该命名为Employee.java。
*   如果一个类定义在某个包中，那么package语句应该在源文件的首行。
*   如果源文件包含import语句，那么应该放在package语句和类定义之间。如果没有package语句，那么import语句应该在源文件中最前面。
*   import语句和package语句对源文件中定义的所有类都有效。在同一源文件中，不能给不同的类不同的包声明。

![](https://img-blog.csdnimg.cn/201909302052064.png)

参考文章：[Java基础语法|菜鸟教程](https://www.runoob.com/java/java-basic-syntax.html)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-964a698a506e45ce8d6976667d80f479)[Java的基本语法](https://edu.csdn.net/skill/java/java-964a698a506e45ce8d6976667d80f479)[Java的基本语法格式](https://edu.csdn.net/skill/java/java-964a698a506e45ce8d6976667d80f479)44190 人正在系统学习中