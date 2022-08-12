**目录**

[Java中的类](#t0)

[类的构造方法](#t1)

[类的实例化(创建对象)](#t2) 

[访问对象的变量和方法](#t3) 

[类的继承](#t4)

[类的多态性](#t5)

[内部类](#t6)

[静态内部类](#t7)

[私有内部类](#t8)

[方法内部类](#t9)

[匿名内部类](#t10)

[抽象类abstract](#t11)

* * *

**类**：类是一个模板，它描述一类对象的行为和状态。

**对象**：对象是类的一个实例，有状态和行为。

例如：一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等。比如，男孩、女孩为类，而具体的每个人是该类的实例化，也就是每个人是该类的对象。

Java中的类
-------

一个Java类可以包含以下类型变量：

*   **类变量(静态变量)**：类变量也声明在类中，方法体之外，必须声明为static类型。
*   **成员变量(实例变量)**：成员变量是定义在类中，方法体之外的变量。这种变量在创建对象的时候实例化。成员变量可以被构造方法、类中方法和特定类的语句块访问。
*   **局部变量**：在方法、构造方法或者语句块中定义的变量被称为局部变量。变量声明和初始化都是在方法中，方法结束后，变量就会自动销毁。

类的成员变量的访问修饰符有public / protected / private 和缺省（不写访问修饰符）

*   如果缺省的话，那么这个变量只允许同一个包中的类访问，对其他包的类不可见，包括其他包的类的子类都不可见。
*   public的话，则对所有的类都可见。
*   protected的话，对本类，本包中的类，以及该类的所有子类可见。
*   private的话，则只对本类可见。

开放性： public > protected > 缺省 > private

### 类的构造方法

每个类都有构造方法。如果没有显式地为类定义构造方法，Java编译器将会为该类提供一个默认构造方法。

在创建一个对象的时候，至少要调用一个构造方法。构造方法的名称必须与类同名，一个类可以有多个构造方法

```
public class Puppy{          
    public Puppy(){                //无参构造方法      
    }      
    public Puppy(String name){     //有参构造方法      
    }      
}
```


### 类的实例化(创建对象) 

对象是根据类创建的。在Java中，使用关键字 new 来创建一个新的对象。创建对象需要以下三步：

*   **声明**：声明一个对象，包括对象名称和对象类型。
*   **实例化**：使用关键字 new 来创建一个对象。
*   **初始化**：使用 new 创建对象时，会调用构造方法初始化对象。

```
public class Puppy{                //声明      
   public String name_;      
   public Puppy(String name){      //这个构造器仅有一个参数：name      
      name_=name;      
   }      
   public static void main(String[] args){      
      Puppy myPuppy = new Puppy( "tommy" );      // 这条语句将创建一个Puppy对象      
   }      
}
```


### 访问对象的变量和方法 

通过已创建的对象来访问成员变量和成员方法。

![](https://img-blog.csdnimg.cn/2019092923140616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

类的继承
----

*   Java中所有类只能继承一个父类，不支持多重继承 ，所有的类都是 Object 类的直接或间接子类 ，如果一个类没有指定父类，则默认继承于 Object 类。
*   子类不能重写父类的构造函数。子类自己的构造函数中，一定会先调用父类的构造函数，如果你没有写，编译器会自动帮你调用父类的无参构造函数。但是当父类有好几个不同参数的构造函数时，子类可以选择的调用父类的构造函数。
*   子类Child继承于父类Father， public、protected成员被自动继承，private成员不被继承。
*   子类中调用父类的函数或变量用 super.xx()  或 super.xx ，但是不能调用父类用private修改的变量或函数

**父类**

```
public class Father {      
    public int id = 1;           //子类自动继承      
    protected  int id2 = 2;      //子类自动继承      
    int id3 = 3;      
    private String name = "father";  //不被子类继承      
    public Father(){                 //父类的无参构造函数      
    }      
    public Father(int id){          //父类的有参构造函数      
        this.id = id;      1
    }      1
    public void test(){            //父类的普通函数      1
        System.out.println("父类的普通函数");      1
    }      1
}
```


**子类** 

```
public class Child extends Father {      
    public Child(){     //子类的构造函数      
        super();        //调用父类的无参构造函数      
    }      
    public void test(){            //子类的普通函数      
        System.out.println("调用父类的成员变量id2和普通函数：");      
        System.out.println(super.id2);      
        super.test();      
    }      1
}
```


**主函数**

```
public class Main {      
    public static void main(String[] args) {      
        Child c = new Child();      
        System.out.println(c.id);      
        System.out.println(c.id2);      
        System.out.println(c.id3);      
        c.test();      
    }      
}
```


运行结果

![](https://img-blog.csdnimg.cn/20200709233946620.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

类的多态性
-----

多态是 java 的一个很重要的特性。多态就是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编译时并不确定，而是在程序运行期间才确定，即一个引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。因为在程序运行时才确定具体的类，这样，不用修改源程序代码，就可以让引用变量绑定到各种不同的类实现上，从而导致该引用调用的具体方法随之改变，即不修改程序代码就可以改变程序运行时所绑定的具体代码，让程序可以选择多个运行状态，这就是多态性。  
     当父类引用指向子类对象时，它可以使用父类中的所有属性和方法，它也可以使用子类中的方法（前提是父类中已经定义，子类重新修改。此时，调用的是子类的方法，而不是父类中的），但是对于子类中定义而父类中没有定义的方法，是用不了的！！

    不能使用子类引用指向父类对象，这是错误的！！！

```
public class Father {      
    public void print(){      
        System.out.println("父亲");      
    }      
    public void syso(){      
        System.out.println("父类有而子类没有");      
    }      
}      
public class Child extends Father {      1
    public void print(){      1
        System.out.println("孩子");      1
    }      1
    public void printf(){      1
        System.out.println("子类有而父类没有");      1
    }      1
}      1
public class Main {      1
    public static void main(String[] args) {      1
        Father sc=new Child();       //父类引用指向子类对象      2
        sc.print();      //父类中有这个方法，子类中对父类的这个方法重写，此时调用的是子类的方法      2
        sc.syso();       //父类中有这个方法，子类中没有，此时调用的是父类的方法      2
        //sc.printf();   //父类中没有这个方法，这个方法是在子类中定义的，此时用不了此方法       2
        //Child c = new Father();  //不能使用子类引用指向父类对象，这是错误的！！！      2
    }      2
}
```


运行结果

![](https://img-blog.csdnimg.cn/2020070923483411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**向上转型**

向上转型都是成功的，向上转型就是父类引用指向子类对象

```
father a=new child();
```


**向下转型**

向下转型却不一定能成功，这时 b 可调用父类中未被重写的方法和子类中的所有方法（包括重写父类的方法）。

注：向下转型相比于父类引用指向子类对象，最主要是可调用父类中没有而子类中有的方法。

```
Father b = new Child();      
Child  c = (Child)b;   //这时c可调用父类中未被重写的方法和子类中的所有方法(包括重写父类的方法)      
c.print();    //子类重写了该方法，所以调用的是子类的方法      
c.syso();    //子类未重写该方法，所以调用的是父类的方法      
c.printf();  //父类中无该方法，仅子类中有该方法，所以调用的是子类的方法
```


 ![](https://img-blog.csdnimg.cn/20200710000008201.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**不成功的向下转型(语法错误)**

```
father a = new father();      
child  b = (child) a;      //这是错误的
```


内部类
---

写在一个类内部的类，称为内部类 。如下，In 是 Out 的内部类。

```
public class Out{          
    System.out.println("外部类");       
    public class In{      
         System.out.println("内部类");       
    }      
}
```


**内部类的创建**

```
//方法一：      
Out out = new Out();      
Out.In in = out.new In();      
//方法二：      
Out.In in = new Out().new In();
```


**内部类和外部类的关系**

在内部类里可以访问自己的成员，也可以访问外部类的所有成员。若外部类和内部类成员重名，访问时需添加访问修饰符Example.this. ，若成员不重名，则直接访问。

内部类中有一个指针指向外部类，所以可以访问外部类的成员  
![](https://img-blog.csdnimg.cn/20190930212656532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**内部类和继承的关系**

*   从文件个数来说继承可以是2个 .java 文件 ，而内部类只能是一个。从代码利用率来说，继承就是为了多次使用 ，而内部类则只能内部类的外类使用，一般来说Java的继承用的比较多，内部类一般用在 GUI 中的事件监听器。
*   继承是面向对象的一个特性，内部类是写在类中的类 ，用来完成一些特别的需要，它们是完全两个概念，各自有各自的功能

**内部类中的变量访问形式**

从中可以发现，内部类在没有同名成员变量和局部变量的情况下，内部类可以直接访问外部类的成员变量，而无需指定Out.this.属性名。否则，内部类中的局部变量会覆盖外部类的成员变量。而访问内部类本身的成员变量可用  this.属性名，访问外部类的成员变量需要使用 Out.this.属性名

```
class Out {      
    private int age = 12;      
    class In {      
        private int age = 13;      
        public void print() {      
            int age = 14;      
            System.out.println("局部变量：" + age);      
            System.out.println("内部类变量：" + this.age);      
            System.out.println("外部类变量：" + Out.this.age);      1
        }      1
    }      1
}
```


### 静态内部类

可以看到，如果用static 将内部类静态化，那么内部类就只能访问外部类的静态成员变量，具有局限性。其次，因为内部类被静态化，因此Out.In可以当做一个整体看，可以直接new 出内部类的对象（通过类名访问static，生不生成外部类对象都没关系)

```
class Out {      
    private static int age = 12;      
    static class In {      
        public void print() {      
            System.out.println(age);      
        }      
    }      
}      
public class Demo {      1
    public static void main(String[] args) {      1
        Out.In in = new Out.In();      1
        in.print();      1
    }      1
}
```


### 私有内部类

如果一个内部类只希望被外部类中的方法操作，那么可以使用 private 声明内部类。  
下面的代码中，我们必须在 Out 类里面生成 In 类的对象进行操作，而无法再使用Out.In in = new Out().new In() 生成内部类的对象。也就是说，此时的内部类只有外部类可控制。如同是，我的心脏只能由我的身体控制，其他人无法直接访问它

```
class Out {      
    private int age = 12;      
    private class In {      
        public void print() {      
            System.out.println(age);      
        }      
    }      
    public void outPrint() {      
        new In().print();      1
    }      1
}      1
public class Demo {      1
    public static void main(String[] args) {      1
        //此方法无效      1
        /*      1
        Out.In in = new Out().new In();      1
        in.print();      1
        */      1
        Out out = new Out();      2
        out.outPrint();      2
    }      2
}
```


### 方法内部类

在下面的代码中，我们将内部类移到了外部类的方法中，然后在外部类的方法中再生成一个内部类对象去调用内部类方法

```
class Out {      
    private int age = 12;      
    public void Print(int x) {      
        class In {      
            public void inPrint() {      
                System.out.println(x);      
                System.out.println(age);      
            }      
        }      1
        new In().inPrint();      1
    }      1
}      1
public class Demo {      1
    public static void main(String[] args) {      1
        Out out = new Out();      1
        out.Print(3);      1
    }      1
}
```


匿名内部类
-----

匿名内部类也就是没有名字的内部类，简称内部类，正因为没有名字，所以匿名内部类只能使用一次，它通常用来简化代码编写，但使用匿名内部类还有个前提条件：必须继承一个父类或实现一个接口。匿名内部类也是内部类，所以语法上和内部类一样使用，可以访问外部类的成员。this.访问自己的成员，Out.this.访问外部类的成员(名字不冲突时，可以省略Out.this)  
匿名内部类和内部类的差别：[java中内部类和匿名内部类的区别](https://www.jb51.net/article/42324.htm)

**匿名内部类的使用**

```
public class Father{      
  protected int id = 0;      
  public Father(int id){      
    this.id = id;      
  }      
  public void print(){      
    System.out.println("base type, id = " + id);      
  }      
}      1
public class Hello{      1
  public static void main(String[] args){      1
    Hello t = new Hello();      1
    t.test1();         1
  }      1
  public void test1(){      1
    // 匿名类的写法      1
    Father t = new Father(123){      1
    public void print(){      1
         System.out.println("child, id=" + id);      2
    }            2
  };       2
    t.print();       2
   // 普通内部类写法      2
   //  Base s = new Child(123);      2
   //  s.print();      2
  }       3
  // 普通写法: 定义一个内部类      3
//class Child extends Father      3
//{      3
//  public Child(int id)      3
//  {      3
//     super(id);           3
//  }       3
//  public void print()      3
//  {      4
//     System.out.println("haha...");      4
//  }         4
//}      4
}
```


**抽象类的匿名内部类的使用**

抽象类的匿名内部类的使用方法和普通匿名内部类使用方法一样，无任何差别

```
public abstract class Image {      
  public int width,height;      
  public abstract void display();      
}      
public class Hello {      
  public static void main(String[] args) {      
    Hello he=new Hello();      
    he.test();       
  }      1
  public void test()      1
  {      1
    //匿名内部类的使用      1
    Image im=new Image() {       1
       public void display() {      1
       System.out.println("匿名内部类");       1
       }       1
    };      2
    im.display();      2
//  普通内部类的使用      2
//  Image im=new Child();      2
//  im.display();      2
  }       2
  //   普通内部类的使用      2
//class Child extends Image{      2
//      2
//  public void display() {      3
//      System.out.println("普通内部类");      3
//  }       3
//}       3
}
```


**接口的匿名内部类的使用**

```
public interface AfCompare {      
  int compare(Object a,Object b);      
  //比较两个对象a,b的大小;      
  //若a>b,返回1; a==b返回0 ; a< b 返回 -1      
}      
public class Student {      
  int id;      
  String name;      
  public Student(int id, String name) {      1
    this.id = id;      1
    this.name = name;      1
  }      1
}public class AfSort {      1
  //用选择排序法对数组进行排序      1
  public static void doSort(Object[] objs,AfCompare c)      1
  {      1
    int N=objs.length;      1
    for (int i = 0; i <N-1; i++)      1
    {      2
       int pos=i;      2
       for(int j=i+1;j<N;j++){      2
         //找到最小值的那个元素的下标，比较大小      2
         if(c.compare(objs[j], objs[pos])<0)      2
         {      2
           pos=j;      2
         }      2
       }      2
       //把最小值换到最前面      2
       if(pos!=i){      3
         Object temp=objs[pos];      3
         objs[pos]=objs[i];      3
         objs[i]=temp;      3
       }            3
    }      3
  }      3
}      3
public class Hello {       3
  public static void main(String[] args) {       4
    Hello he=new Hello();      4
    he.test();      4
  }      4
  public void test()      4
  {      4
    Student data[]=new Student[4];      4
    data[0] = new Student(1001, "Wang");      4
    data[1] = new Student(1032, "Li");      4
    data[2] = new Student(1029, "Shao");      5
    data[3] = new Student(1087, "Chen");      5
    //使用匿名内部类      5
    AfCompare byId=new AfCompare() {       5
       public int compare(Object a, Object b) {         //按分数进行排序       5
         Student a1=(Student) a;      5
         Student b1=(Student) b;      5
         if(a1.id>b1.id)      5
           return 1;      6
         else if (a1.id==b1.id)      6
           return 0;      6
         else      6
           return -1;      6
       }       6
    };      6
    AfSort.doSort(data, byId);      6
    for (int i = 0; i < 4; i++) {      6
       Student s = data[i];      7
       System.out.println(s.id+"---"+s.name);       7
    }       7
    AfCompare byName=new AfCompare(){                //按名字进行排序      7
       public int compare(Object a, Object b) {       7
         Student a1=(Student)a;      7
         Student b1=(Student)b;      7
         return a1.name.compareTo(b1.name);      8
       }      8
    };      8
    AfSort.doSort(data,byName);      8
    for (int i = 0; i < 4; i++) {      8
       Student s = data[i];      8
       System.out.println(s.id+"---"+s.name);        8
    }       8
    //使用普通内部类      8
//  AfCompare af=new Child();      9
//  AfSort.doSort(data,af);      9
//  for (int i = 0; i < 4; i++) {      9
//     Student s = data[i];      9
//     System.out.println(s.id+"---"+s.name);       9
//  }      9
//        9
//  AfCompare af2=new Child2();      9
//  AfSort.doSort(data,af2);      9
//  for (int i = 0; i < 4; i++) {      9
//     Student s = data[i];      10
//     System.out.println(s.id+"---"+s.name);           10
//  }       10
// }       10
//class Child implements AfCompare{            //按ID进行排序      10
//  public int compare(Object a, Object b) {      10
//     Student a1=(Student) a;      10
//     Student b1=(Student) b;      10
//     if(a1.id>b1.id)      10
//       return 1;      11
//     else if (a1.id==b1.id)      11
//       return 0;      11
//     else      11
//       return -1;      11
//  }      11
//}       11
//class Child2 implements AfCompare{      11
//  public int compare(Object a, Object b) {      11
//     Student a1=(Student)a;      12
//     Student b1=(Student)b;      12
//     return a1.name.compareTo(b1.name);      12
//           12
//  }      12
//        12
//}       12
//}
```


抽象类abstract
-----------

类名前加abstract，函数名前加abstract，不写函数体，并不要求一定全是abstract的函数，部分abstract，部分非abstract也是可以的

```
abstract class Image{      
       public int width, heigth;      
       public abstract void load(String filepath);      
       public abstract void display();      
       public void print(){      
       };      
}
```


一个图像类，对JPG, PNG, BMP等格式的图像文件进行抽象，它规定了所有的子类的特征：高度和宽带信息， 应该有一个load函数，用于从文件中加载 ，应该有一个display函数，用于显示画图。   
   
抽象类不能被实例化！抽象类是抽象的，只是对事物的性质进行抽象。它规定了它的子类应该有什么。 

```
Image s = new Image();     // 语法错！
```


抽象类可以定义子类，实现所有规定要实现的函数。

```
public class JpgImage extends Image{      
    public void load(String filepath){      
    }      
    public void display(){      
    }      
}
```


在Eclilpse中，可以自动提示完成，添加函数框架。。。 

*   提示add umimplemented methods 
*   右键 Source | Override / Implement Methods