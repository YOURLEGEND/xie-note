**目录**

[异常Exception](#t0)

[Exception 类的层次](#t1)

[Java中的异常处理机制](#t2)

[Java 内置异常类](#t3)

[自定义异常类](#t4)

[异常方法](#t5)

[throws/throw 关键字](#t6)

[finally关键字](#t7)

* * *

异常Exception
-----------

异常是程序中的一些错误，但并不是所有的错误都是异常，并且错误有时候是可以避免的。

比如说，你的代码少了一个分号，那么运行出来结果是提示是错误 java.lang.Error；如果你用System.out.println(11/0)，那么你是因为你用0做了除数，会抛出 java.lang.ArithmeticException 的异常。

异常发生的原因有很多，通常包含以下几大类：

*   用户输入了非法数据。
*   要打开的文件不存在。
*   网络通信时连接中断，或者JVM内存溢出。

这些异常有的是因为用户错误引起，有的是程序错误引起的，还有其它一些是因为物理错误引起的。-

要理解Java[异常处理](https://so.csdn.net/so/search?q=%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86&spm=1001.2101.3001.7020)是如何工作的，你需要掌握以下三种类型的异常：

*   **检查性异常：**最具代表的检查性异常是用户错误或问题引起的异常，这是程序员无法预见的。例如要打开一个不存在文件时，一个异常就发生了，这些异常在编译时不能被简单地忽略。
*   **运行时异常：** 运行时异常是可能被程序员避免的异常。与检查性异常相反，运行时异常可以在编译时被忽略。
*   **错误：** 错误不是异常，而是脱离程序员控制的问题。错误在代码中通常被忽略。例如，当栈溢出时，一个错误就发生了，它们在编译也检查不到的。

### Exception 类的层次

所有的异常类是从 java.lang.Exception 类继承的子类。  
Exception 类是 Throwable 类的子类。除了Exception类外，Throwable还有一个子类Error 。  
Java 程序通常不捕获错误。错误一般发生在严重故障时，它们在Java程序处理的范畴之外。  
Error 用来指示运行时环境发生的错误。  
例如，JVM 内存溢出。一般地，程序不会从错误中恢复。  
异常类有两个主要的子类：IOException 类和 RuntimeException 类。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxMy8xMi8xMi0xMzBRMTIzNEk2MjIzLmpwZw?x-oss-process=image/format,png)

### Java中的异常处理机制

JAVA彻底的、完全地支持了[异常处理机制](https://so.csdn.net/so/search?q=%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86%E6%9C%BA%E5%88%B6&spm=1001.2101.3001.7020)。JAVA里的异常处理分为两个环节：

1：底层函数抛出异常对象，throw 语句用于抛出一个异常对象，该对象必须是 Throwable 的子类，我们通常使用Exception类，该类用于描述错误的具体信息

```
public int getMax(int[] data)throws Exception{      
       Exception e=new Exception("数组长度不能小于或等于0");      
       throw e;       //丢出这个异常后，立即退出这个函数      
}
```


2：上层调用时，监视代码、捕捉异常对象 try  catch 。如果一切正常，则运行完 try 中的代码后，忽略 catch 中的代码。如果有异常发生，则立即从 try 中退出并跳转到相应的 catch 中运行

```
try{      
    //正常情况下的代码      
   }catch(Exception e){      
         //错误情况下的代码      
       }
```


**捕获异常**

使用 try 和 catch 关键字可以捕获异常。try/catch 代码块放在异常可能发生的地方。

try/catch 代码块中的代码称为保护代码，使用 try/catch 的语法如下：

```
try      
{      
   // 程序代码      
}catch(ExceptionName e1)      
{      
   //Catch 块      
}
```


 Catch 语句包含要捕获异常类型的声明。当保护代码块中发生一个异常时，try 后面的 catch 块就会被检查。

如果发生的异常包含在 catch 块中，异常会被传递到该 catch 块，这和传递一个参数到方法是一样。

 **异常处理机制实例一：**

```
public class Test {      
  public static void main(String[] args) {      
    Test my=new Test();      
    my.test();      
  }      
  public int getMax(int[] data)throws Exception{      
    if(data.length<=0){      
       Exception e=new Exception("数组长度不能小于或等于0");      
       throw e;      1
    }      1
    int max=data[0];      1
    for(int i=1;i<data.length;i++){      1
       if(data[i]>max)      1
         max=data[i];      1
    }      1
    return max;      1
  }      1
  public void test(){      1
    int[] data=new int[0];      2
    //传统if判断      2
//  if(data.length>0){      2
//     int max=getMax(data);      2
//     System.out.println("最大值是"+max);      2
//  }      2
//  else{      2
//     System.out.println("错误：数组长度不能为0");      2
//  }      2
    //使用异常处理机制      2
    try{      3
       int max=getMax(data);      3
       System.out.println("最大值是"+max);      3
    }catch(Exception e){      3
       System.out.println("错误："+e.getMessage());      3
    }      3
  }      3
}
```


 **异常处理机制实例二：**

```
public class AfDate {      
  public int year,month,day;      
  public static void main(String[] args) {      
    AfDate my=new AfDate();      
    try {      
       my.parse("2018-5-18");      
       System.out.println(my.toString());      
    } catch (Exception e) {      
       System.out.println(e.getMessage());      1
    }       1
  }      1
  public void parse(String data) throws Exception{       1
    //判断输入的字符有没有错      1
    for(int i=0;i<data.length();i++){      1
       char c=data.charAt(i);      1
       if(c>='0'&&c<='9'){}      1
       else if(c=='-'){}      2
       else{      2
         throw new Exception("错误：非法字符 position="+i);      2
       }      2
    }      2
    //判断是否有横杠      2
    int p1=data.indexOf("-");      2
    if(p1<0){      2
       throw new Exception("找不到第一个横杆");      2
    }      2
    int p2=data.indexOf("-",p1+1);      3
    if(p2<0){      3
       throw new Exception("找不到第二个横杠");      3
    }      3
    //判断年月日的长度      3
    String a1=data.substring(0,p1);      3
    String b1=data.substring(p1+1,p2);      3
    String c1=data.substring(p2+1);      3
    if(a1.length()!=4){      3
       throw new Exception("格式不对，年错误");      3
    }      4
    if(b1.length()!=1&&b1.length()!=2){      4
       throw new Exception("格式不对，月错误");      4
    }      4
    if(c1.length()!=1&&c1.length()!=2){      4
       throw new Exception("格式不对，日错误");      4
    }      4
    //有效性检测      4
    year=Integer.valueOf(a1);      4
    month=Integer.valueOf(b1);      4
    day=Integer.valueOf(c1);      5
    if(month<0||month>12){      5
       throw new Exception("月份长度错误");      5
    }      5
    if(day<0||day>31){      5
       throw new Exception("日长度错误");      5
    }       5
  }      5
  public String toString() {      5
    return "日期为："+year+"年"+month+"月"+day+"日";      6
  }      6
}
```


### Java 内置异常类

Java 语言定义了一些异常类在 java.lang 标准包中。

标准运行时异常类的子类是最常见的异常类。由于 java.lang 包是默认加载到所有的 Java 程序的，所以大部分从运行时异常类继承而来的异常都可以直接使用。Java 根据各个类库也定义了一些其他的异常，下面的表中列出了 Java 的非检查性异常。

**非检查性异常类**

| **异常** | **描述** |
| --- | --- |
| ArithmeticException | 当出现异常的运算条件时，抛出此异常。例如，一个整数"除以零"时，抛出此类的一个实例。 |
| ArrayIndexOutOfBoundsException | 用非法索引访问数组时抛出的异常。如果索引为负或大于等于数组大小，则该索引为非法索引。 |
| ArrayStoreException | 试图将错误类型的对象存储到一个对象数组时抛出的异常。 |
| ClassCastException | 当试图将对象强制转换为不是实例的子类时，抛出该异常。 |
| IllegalArgumentException | 抛出的异常表明向方法传递了一个不合法或不正确的参数。 |
| IllegalMonitorStateException | 抛出的异常表明某一线程已经试图等待对象的监视器，或者试图通知其他正在等待对象的监视器而本身没有指定监视器的线程。 |
| IllegalStateException | 在非法或不适当的时间调用方法时产生的信号。换句话说，即 Java 环境或 Java 应用程序没有处于请求操作所要求的适当状态下。 |
| IllegalThreadStateException | 线程没有处于请求操作所要求的适当状态时抛出的异常。 |
| IndexOutOfBoundsException | 指示某排序索引（例如对数组、字符串或向量的排序）超出范围时抛出。 |
| NegativeArraySizeException | 如果应用程序试图创建大小为负的数组，则抛出该异常。 |
| NullPointerException | 当应用程序试图在需要对象的地方使用 `null` 时，抛出该异常 |
| NumberFormatException | 当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出该异常。 |
| SecurityException | 由安全管理器抛出的异常，指示存在安全侵犯。 |
| StringIndexOutOfBoundsException | 此异常由 `String` 方法抛出，指示索引或者为负，或者超出字符串的大小。 |
| UnsupportedOperationException | 当不支持请求的操作时，抛出该异常。 |

**检查性异常类**

<table><tbody><tr><td>ClassNotFoundException</td><td>应用程序试图加载类时，找不到相应的类，抛出该异常。</td></tr><tr><td>CloneNotSupportedException</td><td>当调用&nbsp;<code style="user-select: auto;" onclick="mdcp.copyCode(event)">Object</code>&nbsp;类中的&nbsp;<code style="user-select: auto;" onclick="mdcp.copyCode(event)">clone</code>&nbsp;方法克隆对象，但该对象的类无法实现&nbsp;<code style="user-select: auto;" onclick="mdcp.copyCode(event)">Cloneable</code>&nbsp;接口时，抛出该异常。</td></tr><tr><td>IllegalAccessException</td><td>拒绝访问一个类的时候，抛出该异常。</td></tr><tr><td>InstantiationException</td><td>当试图使用&nbsp;<code style="user-select: auto;" onclick="mdcp.copyCode(event)">Class</code>&nbsp;类中的&nbsp;<code style="user-select: auto;" onclick="mdcp.copyCode(event)">newInstance</code>&nbsp;方法创建一个类的实例，而指定的类对象因为是一个接口或是一个抽象类而无法实例化时，抛出该异常。</td></tr><tr><td>InterruptedException</td><td>一个线程被另一个线程中断，抛出该异常。</td></tr><tr><td>NoSuchFieldException</td><td>请求的变量不存在</td></tr><tr><td>NoSuchMethodException</td><td>请求的方法不存在</td></tr></tbody></table>

### 自定义异常类

定义一个类继承 Exception

```
public class BadFormatException extends Exception{      
  String reason;      
  public BadFormatException(String reason) {      
    this.reason=reason;      
  }      
  public String getMessage() {      
    return "格式错误:("+reason+")";      
  }      
}
```


抛出异常类 

```
//抛出这个异常类      
throw new BadFormatException("找不到第1个横杠!");       
throws用于声明本函数可能产生的异常的种类，各异常类以逗号分隔      
public void parse(String data) throws IllegalCharException,BadFormatException,BadRangeException{      
}
```


**异常的匹配：**根据抛出的异常对象的类型，匹配catch语句

*   若匹配成功，则其他的catch不在匹配
*   若全不匹配，则继承向上层函数抛出

怎样才算匹配成功？  
假设抛出的对象类型为EA  
catch(EX e){  
}  
如果EA和EX的类型相同，或者EA是EX的子类，则匹配成功。  
catch(Exception e){  
}  
由于Exception是所有异常的父类，所以这个肯定能捕获异常。一般这个catch放在最后

### 异常方法

下面的列表是 Throwable 类的主要方法:

| **序号** | **方法及说明** |
| --- | --- |
| 1 | **public String getMessage()**  
返回关于发生的异常的详细信息。这个消息在Throwable 类的构造函数中初始化了。 |
| 2 | **public Throwable getCause()**  
返回一个Throwable 对象代表异常原因。 |
| 3 | **public String toString()**  
使用getMessage()的结果返回类的串级名字。 |
| 4 | **public void printStackTrace()**  
打印toString()结果和栈层次到System.err，即错误输出流。 |
| 5 | **public StackTraceElement \[\] getStackTrace()**  
返回一个包含堆栈层次的数组。下标为0的元素代表栈顶，最后一个元素代表方法调用堆栈的栈底。 |
| 6 | **public Throwable fillInStackTrace()**  
用当前的调用栈层次填充Throwable 对象栈层次，添加到栈层次任何先前信息中。 |

### throws/throw 关键字

如果一个方法没有捕获到一个检查性异常，那么该方法必须使用 [throws](https://so.csdn.net/so/search?q=throws&spm=1001.2101.3001.7020) 关键字来声明。throws 关键字放在方法签名的尾部。

也可以使用 throw 关键字抛出一个异常，无论它是新实例化的还是刚捕获到的。

下面方法的声明抛出一个 RemoteException 异常：

```
import java.io.*;      
public class className      
{      
  public void deposit(double amount) throws RemoteException      
  {      
    // Method implementation      
    throw new RemoteException();      
  }      
  //Remainder of class definition      1
}
```


一个方法可以声明抛出多个异常，多个异常之间用逗号隔开。

例如，下面的方法声明抛出 RemoteException 和 InsufficientFundsException：

```
import java.io.*;      
public class className      
{      
   public void withdraw(double amount) throws RemoteException,      
                              InsufficientFundsException      
   {      
       // Method implementation      
   }      
   //Remainder of class definition      1
}
```


###  finally关键字

finally 关键字用来创建在 try 代码块后面执行的代码块。

无论是否发生异常，finally 代码块中的代码总会被执行。

在 finally 代码块中，可以运行清理类型等收尾善后性质的语句。

finally 代码块出现在 catch 代码块最后，语法如下：

```
try{      
  // 程序代码      
}catch(异常类型1 异常的变量名1){      
  // 程序代码      
}catch(异常类型2 异常的变量名2){      
  // 程序代码      
}finally{      
  // 程序代码      
}
```
