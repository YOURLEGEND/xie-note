**目录**

[PHP面向对象](#t0 "PHP面向对象")

[PHP的序列化与反序列化](#t1 "PHP的序列化与反序列化")

[序列化](#t2 "序列化")

[反序列化](#t3 "反序列化")

[Phar反序列化](#t4 "Phar反序列化")

[POP链构造](#t5 "POP链构造")

* * *

PHP面向对象
-------

PHP是面向对象的程序设计语言。

在现实世界里我们所面对的事情都是对象，如计算机、电视机、自行车等。比如 Animal(动物) 是一个抽象类，我们可以具体到一只狗跟一只羊，而狗跟羊就是具体的对象，他们有颜色属性，可以写，可以跑等行为状态。

**对象的主要三个特性：**

*   **对象的行为**：可以对对象施加那些操作，开灯，关灯就是行为。
*   **对象的形态**：当施加那些方法是对象如何响应，颜色，尺寸，外型。
*   **对象的表示**：对象的表示就相当于身份证，具体区分在相同的行为与状态下有什么不同。

**PHP面向对象**

*   **类**：定义了一件事物的抽象特点。类的定义包含了数据的形式以及对数据的操作。
*   **对象**：类的实例。
*   **成员变量**：定义在类内部的变量。该变量的值对外是不可见的，但是可以通过成员函数访问，在类被实例化为对象后，该变量即可称为对象的属性。
*   **成员函数**：定义在类的内部，可用于访问对象的数据。
*   **继承**：继承性是子类自动共享父类数据结构和方法的机制，这是类之间的一种关系。在定义和实现一个类的时候，可以在一个已经存在的类的基础之上来进行，把这个已经存在的类所定义的内容作为自己的内容，并加入若干新的内容。
*   **父类**：一个类被其他类继承，可将该类称为父类，或基类，或超类。
*   **子类**：一个类继承其他类称为子类，也可称为派生类。
*   **多态**：多态性是指相同的函数或方法可作用于多种类型的对象上并获得不同的结果。不同的对象，收到同一消息可以产生不同的结果，这种现象称为多态性。
*   **重载**：简单说，就是函数或者方法有同样的名称，但是参数列表不相同的情形，这样的同名不同参数的函数或者方法之间，互相称之为重载函数或者方法。
*   **抽象性**：抽象性是指将具有一致的数据结构（属性）和行为（操作）的对象抽象成类。一个类就是这样一种抽象，它反映了与应用有关的重要性质，而忽略其他一些无关内容。任何类的划分都是主观的，但必须与具体的应用有关。
*   **封装**：封装是指将现实世界中存在的某个客体的属性与行为绑定在一起，并放置在一个逻辑单元内。
*   **构造函数**：主要用来在创建对象时初始化对象， 即为对象成员变量赋初始值，总与new运算符一起使用在创建对象的语句中。
*   **析构函数**：析构函数(destructor) 与构造函数相反，当对象结束其生命周期时（例如对象所在的函数已调用完毕），系统自动执行析构函数。析构函数往往用来做"清理善后" 的工作(例如在建立对象时用new开辟了一片内存空间，应在退出前在析构函数中用delete释放）

**PHP类的定义**

类使用 class 关键字后加上类名定义

```
<?php       
class Site {       
  /* 成员变量 */       
  var $url;       
  var $title;       
  /* 成员函数 */       
  function setUrl($par){       
     $this->url = $par;       
  }       1
  function getUrl(){       1
     echo $this->url . PHP_EOL;       1
  }       1
  function setTitle($par){       1
     $this->title = $par;       1
  }       1
  function getTitle(){       1
     echo $this->title . PHP_EOL;       1
  }       1
}        2
$runoob = new Site;       2
$taobao = new Site;       2
$google = new Site;        2
// 调用成员函数，设置标题和URL       2
$runoob->setTitle( "菜鸟教程" );       2
$taobao->setTitle( "淘宝" );       2
$google->setTitle( "Google 搜索" );        3
$runoob->setUrl( 'www.runoob.com' );       3
$taobao->setUrl( 'www.taobao.com' );       3
$google->setUrl( 'www.google.com' );        3
// 调用成员函数，获取标题和URL       3
$runoob->getTitle();       3
$taobao->getTitle();       3
$google->getTitle();        3
$runoob->getUrl();       4
$taobao->getUrl();       4
$google->getUrl();       4
?>
```


**PHP中的魔术方法**

在面向对象编程中，PHP提供了一系列的魔术方法，这些魔术方法为编程提供了很多便利。PHP中的魔术方法通常以\_\_(两个下划线)开始，并且不需要显示的调用而是由某种特定的条件出发。那么何时可能使用魔术方法？  
属性重载：如果使用一个对象的未定义的属性，就构成属性重载。属性重载就是对一个“未定义”的属性，进行应对机制（处理办法）。每一个操作，都会自动各自去调用一个预先定义好的“魔术方法”。

**常见的魔术方法**

```
__construct()，类的构造函数      
__destruct()，类的析构函数      
__call()，在对象中调用一个不可访问方法时调用      
__get()，获得一个类的成员变量时调用      
__set()，设置一个类的成员变量时调用      
__isset()，当对不可访问属性调用isset()或empty()时调用      
__unset()，当对不可访问属性调用unset()时被调用。      
__sleep()，执行serialize()时，先会调用这个函数      
__wakeup()，执行unserialize()时，先会调用这个函数      1
__toString()，类被当成字符串时的回应方法      1
__invoke()，调用函数的方式调用一个对象时的回应方法
```


PHP的序列化与反序列化
------------

**什么是序列化？为什么要进行序列化？**

在对一个变量赋值后，若重新打开一个shell，或当本次程序运行完成后，变量值就会从内存中清除掉，而序列化的目的是把变量  
保存在硬盘中，在用到时能很方便的通过反序列化把之前序列化的内容变回为可用变量。简而言之，在PHP中，序列化是用在存储或传递 PHP 的值的过程中的，同时它能不丢失其类型和结构。

### **序列化**

**serialize()** 函数用于序列化对象或数组，并返回一个字符串。**serialize()** 函数序列化对象后，可以很方便的将它传递给其他需要它的地方，且其类型和结构不会改变。

```
string serialize ( 要序列化的对象或数组)
```


![](https://img-blog.csdnimg.cn/20191029102305814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
<?php      
  $number = 34;      
  $str = 'uusama';      
  $bool = true;      
  $null = NULL;      
  $arr = array('a' => 1, 'b' => 2);      
  var_dump(serialize($number));      
  var_dump(serialize($str));      
  var_dump(serialize($bool));      1
  var_dump(serialize($null));      1
  var_dump(serialize($arr));      1
?>       1
///      1
string(5) "i:34;"                        //i代表int类型      1
string(13) "s:6:"uusama";"               //s代表string类型，6代表字符串的长度      1
string(4) "b:1;"                         //b代表bool类型，1代表正确      1
string(2) "N;"                           //N代表NULL      2
string(30) "a:2:{s:1:"a";i:1;s:1:"b";i:2;}"     //a代表array数组，2代表有2个变量
```


### **反序列化**

**unserialize()** 函数用于将通过 serialize()  
函数序列化后的对象或数组进行反序列化，并返回原始的对象结构。返回的是转换之后的值，可为integer、float、string、array或object。若被反序列化的变量是一个对象，在成功重新构造对象之后，PHP会自动地试图去调用 \_\_wakeup() 成员函数（如果存在的话）。

```
mixed unserialize ( 序列化后的字符串 )
```


![](https://img-blog.csdnimg.cn/20191029102116808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果传递的字符串不可反序列化，则返回 FALSE，并产生一个E\_NOTICE。

![](https://img-blog.csdnimg.cn/20191029103439140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

关于php序列化和反序列化详细介绍：[深度剖析PHP序列化和反序列化](https://www.cnblogs.com/youyoui/p/8610068.html "深度剖析PHP序列化和反序列化")

**php bug 72663**

当 php<5.6.27 时，当反序列化对象时，如果表示对象属性个数的值大于真实的属性个数时就会跳过\_\_wakeup的执行。

Phar反序列化
--------

利用这种方法可以在不使用 unserialize() 函数的情况下触发PHP反序列化漏洞，漏洞触发点在使用 phar:// 协议读取文件的时候，文件内容会被解析成 phar 对象，然后 phar 对象内的Meta data 信息会被反序列化。该漏洞利用条件要在php.ini中把phar只读(phar.readonly) 给关闭

![](https://img-blog.csdnimg.cn/20191029104803641.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

POP链构造
------

**POP：** 面向属性编程（Property-Oriented Programing） 用于上层语言构造特定调用链的方法，与二进制利用中的面向返回编程（Return-Oriented Programing）的原理相似，都是从现有运行环境中寻找一系列的代码或者指令调用，然后根据需求构成一组连续的调用链。在控制代码或者程序的执行流程后就能够使用这一组调用链来执行一些操作。

**基本概念：**在二进制利用时，ROP 链构造中是寻找当前系统环境中或者内存环境里已经存在的、具有固定地址且带有返回操作的指令集，而 POP 链的构造则是寻找程序当前环境中已经定义了或者能够动态加载的对象中的属性（函数方法），将一些可能的调用组合在一起形成一个完整的、具有目的性的操作。 二进制中通常是由于内存溢出控制了指令执行流程，而反序列化过程就是控制代码执行流程的方法之一，前提：进行反序列化的数据能够被用户输入所控制。

**POP链利用：**一般的序列化攻击都在PHP魔术方法中出现可利用的漏洞，因为自动调用触发漏洞，但如果关键代码没在魔术方法中，而是在一个类的普通方法中。这时候就可以通过构造POP链寻找相同的函数名将类的属性和敏感函数的属性联系起来。

反序列化漏洞一般都是在白盒审计时发现并利用，需要构造PHP反序列化代码，利用条件比较苛刻。 总结一下PHP反序列化的挖掘思路，首先进行反序列化的数据点是用户可控的，然后反序列化类中需要有魔术方法，魔术方法中存在敏感操作，或者魔术方法中无敏感操作，但是其对象调用了其他类中的同名函数，可以通过构造POP链利用。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[原创干货 | php反序列化那些事](https://mp.weixin.qq.com/s/ziE367h-3_YTTl5EguWyrg "原创干货 | php反序列化那些事")

                  [原创干货 | 再谈 PHP反序列化](https://mp.weixin.qq.com/s/tlOQcgF95pJ3E1x-IbwLYg "原创干货 | 再谈 PHP反序列化")