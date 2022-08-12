**目录**

[I/O流](#t0)

[输入流(FileInputStream)](#t1)

[字节流的读取](#t2)

[字符流的读取](#t3)

[输出流(OutputStream)](#t4)

[带缓冲区的输入输出流(BufferedInput/OutputStream)](#t5)

* * *

I/O流
----

I/O流是数据传送的通道，I/O流分为输入流（Input）和输出流（Ouput）。程序从外部读取称为输入（Input），程序向外部写成为输出（Output）。

*   Java.io 包中几乎包含了所有操作 输入、输出需要的类。所有这些类代表了输入源和输出目标。
*   Java.io 包中的流支持很多种格式，比如：基本类型、对象、本地化字符集等等。

一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。

Java 为 I/O 提供了强大的而灵活的支持，使其更广泛地应用到文件传输和网络编程中。

根据操作类型的不同可以分为：字节流 和 字符流

输入流(FileInputStream)
--------------------

父类：InputStream

常用的字节输入流：FileInputStream ，继承于InputStream

```
File file = new File("1.txt");             
FileInputStream ff = new FileInputStream(file);
```


如果要读取的文件不存在，会报错： java.io.FileNotFoundException 

### 字节流的读取

read()方法，读取一个字节，返回该字节的值，如果到达文件末尾，则返回-1。read()方法和迭代器一样，会自动下移。

### 字符流的读取

read(byte\[\])方法，从输入流中读取至多一个数组长度的内容，如果到达文件末尾，则返回-1。read()方法和迭代器一样，会自动下移。

*   数组称为缓冲区数组，大小一般取值为1024的整数倍。
*   转换为字符时，使用String（byte \[ \]   bytes，int offset，int length）
*   available（）没有读取的剩余字节数，如果该文件还从未被读取，就返回该文件的长度。
*   close（） 关闭流并释放资源

```
import java.io.File;      
import java.io.FileInputStream;      
import java.io.IOException;      
public class Main {      
    public static void main(String[] args) throws IOException {      
        File file = new File("1.txt");       
        System.out.println("读取字节流的第一个字符：");      
        FileInputStream ff = new FileInputStream(file);      1
        int a = ff.read();  //读取的是1.txt文件的第一个字符的ascii值      1
        System.out.println(a);      1
        ff.close();       1
        //字节流的循环读取      1
        System.out.println("读取字节流：");      1
        FileInputStream mm = new FileInputStream(file);      1
        int b;      1
        while( (b=mm.read())!=-1){      1
            System.out.print(b+"-");      2
        }      2
        System.out.println("");      2
        mm.close();       2
        //字符流的循环读取      2
        System.out.println("读取字符流：");      2
        FileInputStream nn = new FileInputStream(file);      2
        byte[] by = new byte[1024];      2
        int c;      2
        while((c= nn.read(by))!=-1){      3
            String str = new String(by,0,c);      3
            System.out.println(str);      3
        }      3
        nn.close();      3
    }      3
}
```


输出流(OutputStream)
-----------------

父类：OutputStream

常用的字节输出流：FileOutputStream ，OutputStream

```
File file = new File("1.txt");      
//覆盖      
FileOutputStream ff = new FileOutputStream(file,false);      
//追加      
FileOutputStream ff = new FileOutputStream(file,true);
```


如果父目录不存在，会报FileNotFoundException异常，如果父目录存在，会创建一个新的文件，如果此时已经有文件存在，会覆盖原文件

常用方法：

```
//向文件中写入一个字节的值      
write(int x)       
//向文件中写入一个数组的数据      
write(byte[])       
//将偏移量为offset的索引位置长度为len的数据写入文件中      
write(byte[],offset,len)       
//关闭该输出流      1
close()
```


示例代码

```
import java.io.*;      
public class Main {      
    public static void main(String[] args) throws IOException {      
        File file = new File("1.txt");   //如果本目录下无1.txt文件，则会新建一个1.txt文件      
        //覆盖      
        FileOutputStream ff = new FileOutputStream(file,false);      
        ff.write("hello,word!".getBytes());      
        ff.close();      
        //追加      1
        FileOutputStream fff = new FileOutputStream(file,true);      1
        fff.write("\n".getBytes());          //写入换行符      1
        fff.write("加油，中国！".getBytes());      1
        fff.close();       1
        //文件复制，将1.txt的内容复制到2.txt内容去      1
        FileInputStream aa = new FileInputStream(file);      1
        FileOutputStream bb = new FileOutputStream(new File("2.txt"));      1
        byte[] cc = new byte[1024];      1
        int count;      2
        while((count=aa.read(cc))!=-1){      2
            bb.write(cc,0,count);      2
        }      2
        aa.close();      2
        bb.close();      2
    }      2
}
```


 带缓冲区的输入输出流(BufferedInput/OutputStream)
---------------------------------------

带缓冲区的输入输出流读写文件速度快于字节流，java 上提供了专门带缓冲区的字节流，用以提高读写速度。

BufferedInputStream / BufferedOutputStream 带缓冲区的字节输入 / 输出流

代码：

```
import java.io.*;      
public class Main {      
    public static void main(String[] args) throws IOException {      
        //读取      
        File f = new File("1.txt");       
            //读取字节      
        BufferedInputStream fff = new BufferedInputStream(new FileInputStream(f));      
        int b;      1
        while( (b=fff.read())!=-1){      1
            System.out.print(b+"-");      1
        }      1
        System.out.println("");      1
            //读取字符      1
        BufferedInputStream mmm = new BufferedInputStream(new FileInputStream(f));      1
        byte[] by = new byte[1024];      1
        int c;      1
        while((c= mmm.read(by))!=-1){      1
            String str = new String(by,0,c);      2
            System.out.println(str);      2
        }       2
        //写入字符      2
        File f2 = new File("2.txt");       2
        BufferedOutputStream nnn = new BufferedOutputStream(new FileOutputStream(f2,false));      2
        nnn.write("hello,word".getBytes());      2
        nnn.close();      2
        BufferedOutputStream ppp = new BufferedOutputStream(new FileOutputStream(f2,true));      3
        ppp.write("\n".getBytes());          //写入换行符      3
        ppp.write("加油，中国！".getBytes());      3
        ppp.close();       3
        //文件复制      3
        BufferedInputStream  rrr = new BufferedInputStream(new FileInputStream(f2));      3
        BufferedOutputStream qqq = new BufferedOutputStream(new FileOutputStream(new File("3.txt")));      3
        byte[] cc = new byte[1024];      3
        int count;      3
        while((count=rrr.read(cc))!=-1){      4
            qqq.write(cc,0,count);      4
        }      4
        rrr.close();      4
        qqq.close();      4
    }      4
}
```


参考文章：[JAVA中IO流详解](https://www.cnblogs.com/ywzbky/p/10692238.html)

相关文章：[Java文件系统操作](https://blog.csdn.net/qq_36119192/article/details/107526104)