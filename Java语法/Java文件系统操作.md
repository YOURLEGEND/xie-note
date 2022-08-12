**目录**

[Java FileSystem](#t0)

[Java IO 文件系统](#t1)

[Java NIO文件系统](#t2)

[Java IO/NIO多种读写文件方式](#t3)

[使用Java IO对文件读写](#t4)

[使用Java NIO对文件读写(java.nio.file.Files)](#t5)

* * *

众所周知Java是一个跨平台的语言，不同的操作系统有着完全不一样的文件系统和特性。JDK会根据不同的操作系统(`AIX,Linux,MacOSX,Solaris,Unix,Windows`)编译成不同的版本。在Java语言中对文件的任何操作最终都是通过`JNI`调用`C语言`函数实现的。Java为了能够实现跨操作系统对文件进行操作抽象了一个叫做FileSystem的对象出来，不同的操作系统只需要实现起抽象出来的文件操作方法即可实现跨平台的文件操作了。

Java FileSystem
---------------

### Java IO 文件系统

Java抽象出了一个叫做文件系统的对象：`java.io.FileSystem`，不同的操作系统有不一样的文件系统,例如`Windows`和`Unix`就是两种不一样的文件系统： `java.io.UnixFileSystem`、`java.io.WinNTFileSystem`。

![image-20191203163038813](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9qYXZhc2VjLm9yZy9pbWFnZXMvaW1hZ2UtMjAxOTEyMDMxNjMwMzg4MTMucG5n?x-oss-process=image/format,png)

`java.io.FileSystem`是一个抽象类，它抽象了对文件的操作，不同操作系统版本的JDK会实现其抽象的方法从而也就实现了跨平台的文件的访问操作。

![image-20191203164105238](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9qYXZhc2VjLm9yZy9pbWFnZXMvaW1hZ2UtMjAxOTEyMDMxNjQxMDUyMzgucG5n?x-oss-process=image/format,png)

示例中的`java.io.UnixFileSystem`最终会通过JNI调用[native](https://so.csdn.net/so/search?q=native&spm=1001.2101.3001.7020)方法来实现对文件的操作:

![](https://img-blog.csdnimg.cn/2020072223363422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

由此我们可以得出Java只不过是实现了对文件操作的封装而已，最终读写文件的实现都是通过调用native方法实现的。

不过需要特别注意一下几点：

1.  并不是所有的文件操作都在`java.io.FileSystem`中定义,文件的读取最终调用的是`java.io.FileInputStream#read0、readBytes`、`java.io.RandomAccessFile#read0、readBytes，`而写文件调用的是`java.io.FileOutputStream#writeBytes`、`java.io.RandomAccessFile#write0`。
2.  Java有两类文件系统API！一个是基于`阻塞模式的IO`的文件系统，另一是JDK7+基于`NIO.2`的文件系统。

### Java NIO文件系统

Java 7提出了一个基于NIO的文件系统，这个NIO文件系统和阻塞IO文件系统两者是完全独立的。`java.nio.file.spi.FileSystemProvider` 对文件的封装和 `java.io.FileSystem`同理。

![image-20191203181206243](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9qYXZhc2VjLm9yZy9pbWFnZXMvaW1hZ2UtMjAxOTEyMDMxODEyMDYyNDMucG5n?x-oss-process=image/format,png)

NIO的文件操作在不同的系统的最终实现类也是不一样的，比如Mac的实现类是: `sun.nio.fs.UnixNativeDispatcher`,而Windows的实现类是`sun.nio.fs.WindowsNativeDispatcher`。

合理的利用NIO文件系统这一特性我们可以绕过某些只是防御了`java.io.FileSystem`的`WAF`/`RASP。`

Java IO/NIO多种读写文件方式
-------------------

上面我们提到了Java 对文件的读写分为 基于阻塞模式的IO和非阻塞模式的NIO，下面将列举一些我们常用于读写文件的方式。

我们通常读写文件都是使用的阻塞模式，与之对应的也就是`java.io.FileSystem`。`java.io.FileInputStream`类提供了对文件的读取功能，Java的其他读取文件的方法基本上都是封装了`java.io.FileInputStream`类，比如：`java.io.FileReader`。

Java内置的文件读取方式大概就是这三种方式，其他的文件读取API可以说都是对这几种方式的封装而已(依赖数据库、命令执行、自写JNI接口不算)。本章我们通过深入基于IO和NIO的Java文件系统底层API。

### 使用Java IO对文件读写

传送门：[Java中的I/O流](https://xie1997.blog.csdn.net/article/details/107328377)

### 使用Java NIO对文件读写(java.nio.file.Files)

上面提到了JDK7新增的NIO.2的`java.nio.file.spi.FileSystemProvider，`利用`FileSystemProvider`我们可以利用支持异步的通道(`Channel`)模式读取文件内容。`java.nio.file.Files`是JDK7开始提供的一个对文件读写取非常便捷的API，其底层是调用了`java.nio.file.spi.FileSystemProvider`来实现对文件的读写的。

**java.nio.file.Filels 读写文件内容示例:**

```
import java.io.File;      
import java.io.IOException;      
import java.nio.file.Files;      
import java.nio.file.Path;      
import java.nio.file.Paths;       
public class Main {      
    public static void main(String[] args) {      
        //通过File对象定义读取的文件路径      1
//        File file = new File("test.txt");      1
//        Path path = file.toPath();       1
        //定义读取的文件路径      1
        Path path = Paths.get("test.txt");       1
        //写文件      1
        String content = "Hello,word!";      1
        try{      1
            Files.write(path,content.getBytes());      2
        } catch (IOException e) {      2
            e.printStackTrace();      2
        }       2
        //读文件      2
        try{      2
            byte[] bytes = Files.readAllBytes(path);      2
            System.out.println(new String(bytes));      2
        } catch (IOException e) {      2
            e.printStackTrace();      3
        }      3
    }      3
}
```


原文：[Java 文件系统](https://javasec.org/javase/FileSystem/)

相关文章：[Java中的I/O流](https://xie1997.blog.csdn.net/article/details/107328377)