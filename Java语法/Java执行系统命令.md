在Java的反序列化远程代码执行过程中，最终就是通过调用[Runtime类](https://so.csdn.net/so/search?q=Runtime%E7%B1%BB&spm=1001.2101.3001.7020)的exec函数来执行系统命令。

如下代码，通过执行Runtime.getRuntime().exec()函数执行 calc.exe 命令

```
package com.company;      
import java.io.BufferedReader;      
import java.io.IOException;      
import java.io.InputStreamReader;      
import java.nio.charset.Charset;      
public class Main {      
    public static void main(String[] args) throws IOException, InterruptedException {      
        Process p = Runtime.getRuntime().exec("calc.exe");      
        java.io.InputStream is = p.getInputStream();      1
        BufferedReader reader = new BufferedReader(new InputStreamReader(is, Charset.forName("GBK"))); //设置读取的时候的编码为GBK      1
        p.waitFor();      1
        if(p.exitValue()!=0){      1
            //说明命令执行失败      1
        }else{      1
            String s = null;      1
            while((s=reader.readLine())!=null){      1
                System.out.println(s);      1
            }      1
        }      2
    }      2
}
```


![](https://img-blog.csdnimg.cn/20200713233853201.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)