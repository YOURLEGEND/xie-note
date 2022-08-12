在实际工作项目过程中，经常会碰到[乱码](https://so.csdn.net/so/search?q=%E4%B9%B1%E7%A0%81&spm=1001.2101.3001.7020)的情况。本节来简单讲一下java的编码问题

如下代码

```
import java.io.UnsupportedEncodingException;      
import java.nio.charset.Charset;      
import java.nio.charset.StandardCharsets;      
public class Main {      
    public static void main(String[] args) throws UnsupportedEncodingException {      
        //获取文件编码格式,默认为UTF-8      
        System.out.println("系统默认编码:"+System.getProperty("file.encoding"));      
        //获取系统默认字符编码,默认为UTF-8      
        System.out.println("系统默认字符编码:"+Charset.defaultCharset());      1
        //操作系统用户使用的语言      1
        System.out.println("系统默认语言:"+ System.getProperty("user.language"));       1
        String a ="中国";       1
        byte[] b = a.getBytes(StandardCharsets.UTF_8);     //以UTF-8的格式将字符串转为字节数组      1
        String c = new String(b, StandardCharsets.UTF_8);   //以UTF-8的格式将字节数组转为字符串      1
        System.out.println(c);       1
        b = a.getBytes("GBK");     //以GBK的格式将字符串转为字节数组      2
        c = new String(b, StandardCharsets.UTF_8);   //以UTF-8的格式将字节数组转为字符串      2
        System.out.println(c);       2
        b = a.getBytes(StandardCharsets.UTF_8);     //以UTF-8的格式将字符串转为字节数组      2
        c = new String(b, "GBK");   //以GBK的格式将字节数组转为字符串      2
        System.out.println(c);      2
    }      2
}
```


运行结果，可以看出，当我们将字符串转为字节数组和将字节数组转为字符串的过程中，编码必须相同。

![](https://img-blog.csdnimg.cn/20200713234639239.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

还有我们在执行系统命令的过程中，输出是乱码的。这种情况经常发生，这主要是由于系统的编码和我们当前项目编码不一致造成的。项目编码默认为UTF-8，而我们计算机的默认编码为GBK，所以导致这种问题。

![](https://img-blog.csdnimg.cn/20200713234938440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

所以这种情况下解决乱码是读取的时候。设置编码为GBK。如下

```
package com.company;       
import java.io.BufferedReader;      
import java.io.IOException;      
import java.io.InputStreamReader;      
import java.nio.charset.Charset;      
public class Main {      
    public static void main(String[] args) throws IOException, InterruptedException {      
        Process p = Runtime.getRuntime().exec("ipconfig");      1
        java.io.InputStream is = p.getInputStream();      1
        BufferedReader reader = new BufferedReader(new InputStreamReader(is, Charset.forName("GBK"))); //设置读取的时候的编码为GBK      1
        p.waitFor();      1
        if(p.exitValue()!=0){      1
            //说明命令执行失败      1
        }else{      1
            String s = null;      1
            while((s=reader.readLine())!=null){      1
                System.out.println(s);      1
            }      2
        }      2
    }      2
}
```


![](https://img-blog.csdnimg.cn/20200713235108376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)