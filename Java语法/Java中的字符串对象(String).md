**目录**

[字符串的定义](#t0)

[字符串的常见操作](#t1)

[将整数转换为字符串](#t2)

[格式化字符串](#t3)

[字符串的比较](#t4)

[将字符串转为ASCII码](#t5)

* * *

Java 中字符串是个对象，是引用数据类型。

### 字符串的定义

```
//字符串的定义      
String a = "hello,word!";      
String b = new String(" hello,word!");      
String z = "\"ok\"";   //转义字符串
```


### 字符串的常见操作

```
//字符串的基本操作      
int len = a.length();      //取得字符串的长度      
char c = a.charAt(4);      //提取出a中索引为4的字符，也就是o      
int d = a.indexOf("w");    //取得指定字符的索引      
String e = b.trim();       //去掉字符串首尾的空格      
String f = a + ",hha";     //字符串的拼接      
String g = a.substring(1,4);       //字符串的子串      
String h = a.replace("llo","he");  //字符串的替换      
a.getBytes();             //将字符串转为字节流
```


### 将整数转换为字符串

```
//将整数转换为字符串      
int i = 123;      
String j = String.valueOf(i);
```


### 格式化字符串

```
//格式化字符串      
int k = 456;      
double l = 123.456;      
String m = "hello,word!";      
String n = String.format("k=%d,l=%.2f,%s",k,l,m);
```


### 字符串的比较

```
//字符串的比较                                             
if(a.equals(b)){                                     
    System.out.println("字符串a和字符串b相等");               
}else{                                               
    System.out.println("字符串a和字符串b不相等");              
}                                                    
if(a.equals("hello,word!")){                         
    System.out.println("字符串a和 hello,word! 相等");      
}else{                                               1
    System.out.println("字符串a和 hello,word! 不相等")      1
}                                                    1
//字符串的大小比较                                           1
int q = a.compareTo(b);  //相等为0，大于整数，小于负数            1
if(q>0){                                             1
    System.out.println("a大于b");                      1
}else if(q<0){                                       1
    System.out.println("a小于b");                      1
}else{                                               1
    System.out.println("a等于b");                      2
}
```


**字符串的比较**

*   基本数据类型，也称原始数据类型。byte、short、char、int、long、float、double、boolean 他们之间的比较，应用双等号（==），比较的是他们的值。
*   复合数据类型（类），当他们用（==）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地址，但在一些类库当中这个方法被覆盖掉了，如String、Integer、Date在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。 对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（==）进行比较的，所以比较后的结果跟双等号（==）的结果相同。

### 将字符串转为ASCII码

```
//将字符串转换为ASCII码      
System.out.println("---将字符串转换为ASCII码---");      
String o = "abcdABCD";      
System.out.println(String.format("原字符串为:%s",o));      
String r = "";      
byte[] p = o.getBytes();      
for(byte t:p){      
   r = r+t+"-";   //拼接，最后为 97-98-99-100-65-66-67-68-      
}      1
r=r.substring(0,r.length()-1);  //去除最末尾的-      1
System.out.println(String.format("转为ASCII值为：%s",r));
```


**全文代码**

```
package com.company;       
public class Main {      
    public static void main(String[] args) {      
        //字符串的定义      
        String a = "hello,word!";      
        String b = new String(" hello,word!");      
        String z = "\"ok\"";   //转义字符串      
        System.out.println("---字符串的定义---");      1
        System.out.println(a);      1
        System.out.println(b);      1
        System.out.println(z);       1
        //字符串的基本操作      1
        int len = a.length();  //取得字符串的长度      1
        char c = a.charAt(4);  //提取出a中索引为4的字符，也就是o      1
        int d = a.indexOf("w");  //取得指定字符的索引      1
        String e = b.trim();   //去掉字符串首尾的空格      1
        String f = a + ",hha";  //字符串的拼接      2
        String g = a.substring(1,4);  //字符串的子串      2
        String h = a.replace("llo","he"); //字符串的替换       2
        System.out.println("---字符串的基本操作---");      2
        System.out.println(len);      2
        System.out.println(c);      2
        System.out.println(d);      2
        System.out.println(e);      2
        System.out.println(f);      2
        System.out.println(g);      3
        System.out.println(h);       3
        //将整数转换为字符串      3
        int i = 123;      3
        String j = String.valueOf(i);      3
        System.out.println("---将整数转为字符串---");      3
        System.out.println(j);       3
        //格式化字符串      3
        int k = 456;      4
        double l = 123.456;      4
        String m = "hello,word!";      4
        String n = String.format("k=%d,l=%.2f,%s",k,l,m);      4
        System.out.println("---格式化字符串---");      4
        System.out.println(n);       4
        //字符串的比较      4
        System.out.println("---字符串的比较---");      4
        if(a.equals(b)){      4
            System.out.println("字符串a和字符串b相等");      5
        }else{      5
            System.out.println("字符串a和字符串b不相等");      5
        }      5
        if(a.equals("hello,word!")){      5
            System.out.println("字符串a和 hello,word! 相等");      5
        }else{      5
            System.out.println("字符串a和 hello,word! 不相等");      5
        }      5
        //字符串的大小比较      5
        int q = a.compareTo(b);  //相等为0，大于整数，小于负数      6
        if(q>0){      6
            System.out.println("a大于b");      6
        }else if(q<0){      6
            System.out.println("a小于b");      6
        }else{      6
            System.out.println("a等于b");      6
        }      6
        //将字符串转换为ASCII码      6
        System.out.println("---将字符串转换为ASCII码---");      6
        String o = "abcdABCD";      7
        System.out.println(String.format("原字符串为:%s",o));      7
        String r = "";      7
        byte[] p = o.getBytes();      7
        for(byte t:p){      7
            r = r+t+"-";   //拼接，最后为 97-98-99-100-65-66-67-68-      7
        }      7
        r=r.substring(0,r.length()-1);  //去除最末尾的-      7
        System.out.println(String.format("转为ASCII值为：%s",r));      7
    }      7
}
```


**运行截图**

![](https://img-blog.csdnimg.cn/20200713231315907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

本文代码：链接: [https://pan.baidu.com/s/1iHSFSktzBWOJp210Gcsrkw](https://pan.baidu.com/s/1iHSFSktzBWOJp210Gcsrkw) 提取码: itk8