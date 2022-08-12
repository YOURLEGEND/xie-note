**目录**

[序列化和反序列化](#t0)

[JAVA WEB中的序列化和反序列化](#t1)

[对象序列化和反序列示例](#t2)

[利用反序列化执行系统命令](#t3)

* * *

序列化和反序列化
--------

**序列化** (Serialization)：将对象的状态信息转换为可以存储或传输的形式的过程。在序列化期间，对象将其当前状态写入到临时或持久性存储区。

**反序列化**：从存储区中读取该数据，并将其还原为对象的过程，称为反序列化。

简单的说，序列化和反序列化就是：

*   把对象转换为字节序列的过程称为对象的序列化
*   把字节序列恢复为对象的过程称为对象的反序列化

对象序列化的用途：

*   把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中
*   在网络上传送对象的字节序列

当两个进程在进行远程通信时，彼此可以发送各种类型的数据。无论是何种类型的数据，最终都会以二进制的形式在网络上传送。发送方需要把这个Java对象序列化；接收方收到数据后把数据反序列化为Java对象。

通常，对象实例的所有字段都会被序列化，这意味着数据会被表示为实例的序列化数据。这样，能够解释该格式的代码就能够确定这些数据的值，而不依赖于该成员的可访问性。类似地，反序列化从序列化的表示形式中提取数据，并直接设置对象状态。 

对于任何可能包含重要的安全性数据的对象，如果可能，应该使该对象不可序列化。如果它必须为可序列化的，请尝试生成特定字段来保存重要数据。如果无法实现这一点，则应注意该数据会被公开给任何拥有序列化权限的代码，并确保不让任何恶意代码获得该权限。

在很多应用中，需要对某些对象进行序列化，让它们离开内存空间，入住物理硬盘，以便长期保存。比如最常见的是Web服务器中的Session对象，当有10万用户并发访问，就有可能出现10万个Session对象，内存可能吃不消，于是Web容器就会把一些seesion先序列化到硬盘中，等要用了，再把保存在硬盘中的对象还原到内存中。

JAVA WEB中的序列化和反序列化
------------------

*   java.io.ObjectOutputStream 代表对象输出流，它的 **writeObject()** 方法可对参数指定的对象进行序列化，把得到的字节序列写到一个目标输出流中
*   java.io.ObjectInputStream 代表对象输入流，它的 **readObject()** 方法从一个源输入流中读取字节序列，再把它们反序列化为一个对象，并将其返回

只有实现了 **Serializable** 和 **Externalizable** 接口的类的对象才能被序列化和反序列化。Externalizable 接口继承自 Serializable 接口，实现 Externalizable 接口的类完全由自身来控制反序列化的行为，而实现 Serializable 接口的类既可以采用默认的反序列化方式，也可以自定义反序列化方式。通过重写 readObject() 方法可以控制反序列化的行为。  
**对象序列化包括如下步骤：**

1.  创建一个对象输出流，它可以包装一个其他类型的目标输出流，如文件输出流
2.  通过对象输出流的 writeObject() 方法将对象进行序列化

**对象反序列化的步骤如下：**

1.  创建一个对象输入流，它可以包装一个其他类型的源输入流，如文件输入流
2.  通过对象输入流的 readObject() 方法将字节序列反序列化为对象

### **对象序列化和反序列示例**

定义一个User类，实现Serializable接口

```
import java.io.IOException;      
import java.io.Serializable;      
public class User implements Serializable{      
	private String name;      
	public String getName(){      
		return name;      
	}      
	public void setName(String name){      
		this.name=name;      1
	}      1
}
```


定义主类，对User对象进行序列化和反序列化

```
import java.io.*;      
public class Main {      
    public static void main(String[] args) {      
        Main m = new Main();      
        try{      
            m.run();   //序列化      
            m.run2();  //反序列化      
        } catch (IOException |ClassNotFoundException e) {      
            e.printStackTrace();      1
        }      1
    }      1
    public void run() throws IOException{      1
        FileOutputStream out = new FileOutputStream("test.txt");  //实例化一个文件输出流      1
        ObjectOutputStream obj_out=new ObjectOutputStream(out);   //实例化一个对象输出流      1
        User u = new User();      1
        u.setName("谢公子");      1
        obj_out.writeObject(u);   //利用writeObject方法将序列化对象存储在本地      1
        obj_out.close();      1
        System.out.println("User对象序列化成功！");      2
    }      2
    public void run2() throws IOException, ClassNotFoundException {      2
        FileInputStream in = new FileInputStream("test.txt");  //实例化一个文件输入流      2
        ObjectInputStream ins = new ObjectInputStream(in);  //实例化一个对象输入流      2
        User u = (User)ins.readObject();      //利用readObject方法将序列化对象转为对象      2
        System.out.println("User对象反序列化成功！");      2
        System.out.println(u.getName());      2
        ins.close();      2
    }      2
}
```


**运行结果**

![](https://img-blog.csdnimg.cn/20200719223444302.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

同时，会在当前文件夹生成一个 test.txt 用来存储序列化的对象，内容如下：

![](https://img-blog.csdnimg.cn/20200719223422887.png)

### 利用反序列化执行系统命令

我们上面说到了可以通过重写 readObject() 方法来自定义类的反序列化方式。所以，我们将User类的 readObject() 进行重写

```
import java.io.*;      
public class User2 implements Serializable{      
    private String name;      
    public String getName(){      
        return name;      
    }      
    public void setName(String name){      
        this.name=name;      
    }      1
    private  void readObject(ObjectInputStream in) throws InterruptedException, IOException, ClassNotFoundException {      1
        //先调用默认的readObject()方法      1
        in.defaultReadObject();       1
        //重写，执行系统命令calc.exe      1
        Process p = Runtime.getRuntime().exec("calc.exe");      1
        InputStream is = p.getInputStream();      1
        BufferedReader reader = new BufferedReader(new InputStreamReader(is));      1
        p.waitFor();      1
        if(p.exitValue()!=0){      2
            //说明执行系统命令失败      2
        }      2
        String s = null;      2
        while((s=reader.readLine())!=null){      2
            System.out.println(s);      2
        }       2
    }      2
}
```


主类中的代码不变，我们再来执行序列化和反序列化过程。可以看到，除了执行了对象的序列化和反序列化之外，还执行了我们自定义的系统命令的代码。 

![](https://img-blog.csdnimg.cn/20200719225132577.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Java执行系统命令](https://xie1997.blog.csdn.net/article/details/107326695)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-be6c0bba441f4c53bd8a274e2a7cb917)[IO](https://edu.csdn.net/skill/java/java-be6c0bba441f4c53bd8a274e2a7cb917)[对象序列化](https://edu.csdn.net/skill/java/java-be6c0bba441f4c53bd8a274e2a7cb917)44190 人正在系统学习中