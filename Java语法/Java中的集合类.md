**目录**

[集合类](#t0)

[Collection](#t1)

[List](#t2)

[ArrayList](#t3)

[LinkedList](#t4)

[Vector](#t5)

[Stack](#t6)

[Set](#t7)

[集合类的遍历](#t8)

[Map](#t9)

[HashMap](#t10)

[​HashTable](#t11)

[WeakHashMap](#t12)

[comparator和comparable的区别](#t13)

* * *

![](https://img-blog.csdnimg.cn/2018121914311521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)
==================================================================================================================================================================================================

集合类
===

我们都知道[数组](https://so.csdn.net/so/search?q=%E6%95%B0%E7%BB%84&spm=1001.2101.3001.7020)，数组中存放了相同数据类型的数据，数组一旦定义好，数组的长度就不可变化了。但是如果我们需要保存一个可以动态变化的数据(在编译时无法确定具体的数量)，java的**集合类**就是一个很好的设计方案了。  
[集合](https://so.csdn.net/so/search?q=%E9%9B%86%E5%90%88&spm=1001.2101.3001.7020)类位于 java.util 包下，集合类主要负责保存、盛装其他数据，因此集合类也被称为**容器类**。

Java容器类类库可划分为两个类型：  
**Map**： 一组成对的"键值对"元素  
**Collection**：一组"对立"的元素，通常这些元素都服从某种规则 　

*   List 必须保持元素特定的顺序 　　
*   Set 不能有重复元素

![](https://img-blog.csdnimg.cn/20181219142930231.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Collection
==========

[Collection](https://so.csdn.net/so/search?q=Collection&spm=1001.2101.3001.7020)是最基本的集合接口，一个Collection代表一组Object，即Collection的元素（Elements）。一些 Collection允许相同的元素而另一些不行。一些能排序而另一些不行。

所有实现Collection接口的类都必须提供两个标准的构造函数：

*   无参数的构造函数用于创建一个空的 Collection
*   有参数的 Collection 构造函数用于创建一个新的Collection，这个新的Collection与传入的Collection有相同的元素。后一个构造函数允许用户复制一个Collection。

List
----

List是有序的Collection，使用此接口能够精确的控制每个元素插入的位置。用户能够使用索引（元素在List中的位置，类似于数组下标）来访问List中的元素，这类似于Java的数组。 和下面要提到的Set不同，List允许有相同的元素。  
   
除了具有Collection接口必备的 iterator() 方法外，List 还提供一个listIterator()方法，返回一个 ListIterator 接口，和标准的iterator接口相比，ListIterator多了一些add()之类的方法，允许添加，删除，设定元素，还能向前或向后遍历。   
实现List接口的常用类有LinkedList，ArrayList，Vector和Stack。

### ArrayList

ArrayList实现了可变大小的数组。它允许所有元素，包括null。ArrayList没有同步。 

Student类

```
public class Student implements Comparable<Student> {      
    int id;      
    public Student(){           //无参构造函数      
    }      
    public Student(int id){     //有参构造函数      
        this.id=id;      
    }       
    public int compareTo(Student o){      1
        return this.id-o.id;      1
    }      1
}
```


主类 

```
import java.util.*;      
public class Main {      
    public static void main(String[] args) {      
//        ArrayList<T> list = new ArrayList<T>();      
        ArrayList<Student> ss = new ArrayList<Student>();      
        ss.add(new Student(5));      //增加一个元素      
        ss.add(new Student(3));      //增加一个元素      
        ss.add(new Student(2));      //增加一个元素      
        System.out.println("ss为："+ss);      1
        System.out.println("ss的长度为："+ss.size());      //打印出数组的长度      1
        ss.add(1,new Student(4));  //往索引为1的地方添加元素      1
        System.out.println("往索引为1处添加元素后,ss为："+ss);      1
        System.out.println("ss中索引为1的值为："+ss.get(1));   //获得索引为1的数据      1
        ss.set(1,new Student(7));         //替换索引为1的数据      1
        ss.remove(1);             //删除索引为1的数据      1
        System.out.println("ss为："+ss);       1
        ArrayList<Student> ss2 = new ArrayList<Student>();      1
        ss2.add(new Student(2));      //增加一个元素      2
        System.out.println("ss2为："+ss2);      2
        ss.addAll(ss2);              //将ss2中的所有元素加到ss中      2
        System.out.println("ss加上ss2为："+ss);      2
        List ss3 = ss.subList(2,3);  //取得ss中索引2到3的元素,左闭右开      2
        System.out.println("ss3为："+ss3);      2
       // int index=ss.indexOf(Student obj);   //查找ss中值是obj的那个元素的索引，需要我们自己重新Student的equals函数，决定如何比较      2
        ss3.clear();      2
        if(ss3.isEmpty()){      2
            System.out.println("ss3是空的");      2
        }      3
        System.out.println("排序前ss为:");      3
        //对数组的遍历一：      3
        for(Student s:ss){      3
            System.out.println(s.id);      3
        }      3
        //对数组的遍历二      3
//        for(int i=0;i<ss.size();i++){      3
//            Student s = ss.get(i);      3
//            System.out.println(s.id);      3
//        }      4
        //对数组的遍历三，使用迭代器Iterator遍历      4
//        Iterator it =ss.iterator();      4
//        while(it.hasNext()){      4
//            Student a= (Student) it.next();      4
//            System.out.println(a.id);      4
//        }       4
        //由于Student内部实现了Comparable接口，所以可以直接进行比较      4
        Collections.sort(ss);       5
        //对数组链表中的数据排序,写一个比较器      5
//        ss.sort(new Comparator<Student>() {      5
//            @Override      5
//            public int compare(Student o1, Student o2) {   //如果要按照升序排序,则o1小于o2，返回-1（负数），相等返回0，01大于02返回1（正数） ;如果要按照降序排序,则o1 小于o2，返回1（正数），相等返回0，01大于02返回-1（负数）      5
//                if(o1.id>o2.id){      5
//                    return 1;      5
//                }else{      5
//                    return -1;      5
//                }      5
//            }      6
//        });      6
        System.out.println("排序后ss为:");      6
        for(int i=0;i<ss.size();i++){      6
            Student s = ss.get(i);      6
            System.out.println(s.id);      6
        }      6
    }      6
}
```


**运行截图**

![](https://img-blog.csdnimg.cn/20200715003508660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### LinkedList

LinkedList 实现了List接口，允许null元素。此外LinkedList提供额外的get，remove，insert方法在 LinkedList的首部或尾部。这些操作使LinkedList可被用作堆栈（stack），队列（queue）或双向队列（deque）。   
需要注意的是LinkedList没有同步方法。如果多个线程同时访问一个List，则必须自己实现访问同步。一种解决方法是在创建List时构造一个同步的List： List list = Collections.synchronizedList(new LinkedList(...));

### Vector

Vector非常类似ArrayList，但是Vector是同步的。由Vector创建的Iterator，虽然和ArrayList创建的 Iterator是同一接口，但是，因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时将抛出ConcurrentModificationException，因此必须捕获该异常。  
Vector的方法都是同步的(Synchronized)，是线程安全的(thread-safe)，而ArrayList的方法不是，由于线程的同步必然要影响性能，因此，ArrayList的性能比Vector好。   
当Vector或ArrayList中的元素超过它的初始大小时,Vector会将它的容量翻倍,而ArrayList只增加50%的大小，这样,ArrayList就有利于节约内存空间。 

### Stack

Stack继承自Vector，实现一个后进先出的堆栈。Stack提供5个额外的方法使得Vector得以被当作堆栈使用。基本的push和pop 方法，还有peek方法得到栈顶的元素，empty方法测试堆栈是否为空，search方法检测一个元素在堆栈中的位置。Stack刚创建后是空栈。

Set
---

Set是一种不包含重复的元素的Collection，即任意的两个元素e1和e2都有e1.equals(e2)=false，Set最多有一个null元素。   
很明显，Set的构造函数有一个约束条件，传入的Collection参数不能包含重复的元素。 

**请注意**：必须小心操作可变对象（Mutable Object）。如果一个Set中的可变元素改变了自身状态导致 Object.equals(Object)=true将导致一些问题。

集合类的遍历
------

```
Iterator it = collection.iterator();     // 获得一个迭代子类      
while(it.hasNext()) {       
　　Object obj = it.next();     // 得到下一个元素       
}
```


Map
===

请注意，Map没有继承Collection接口，Map提供key到value的映射。一个Map中不能包含相同的key，每个key只能映射一个 value。Map接口提供3种集合的视图，Map的内容可以被当作一组key集合，一组value集合，或者一组key-value映射

### HashMap

     HashMap 和 Hashtable 类似，不同之处在于HashMap是非同步的，并且允许null，即null value和null key。但是将HashMap视为 Collection 时（values()方法可返回Collection），其迭代子操作时间开销和HashMap 的容量成比例。因此，如果迭代操作的性能相当重要的话，不要将HashMap的初始化容量设得过高，或者load factor过低。添加数据使用put(key, value)，取出数据使用get(key)，删除数据使用remove(key)  
    哈希表 HashMap 中可以存储N个对象，每个对象由一个唯一的key值关联。哈希表中每个key值互不相同，通过key值可以定位到相应的数据，key的类型可以是Integet、String、Long等引用类型，而不可以是int，long这种基本数据类型。

```
HashMap<key,Vaule> ss=new HashMap<key,T>();
```


注意：在put一个对象时，如果这个key已经存在，则会替换原有的对象 

```
import java.util.HashMap;      
public class Main {      
    public static void main(String[] args) {      
        HashMap<Integer,Student> hh = new HashMap<Integer, Student>();       
        hh.put(1000,new Student(1000,"xie"));   //添加一个元素      
        hh.put(2000,new Student(2000,"gong"));      
        hh.put(3000,new Student(3000,"zi"));      
        hh.put(4000,new Student(4000,"ai"));       1
        //哈希表的遍历      1
        for(Student t:hh.values()){      1
            System.out.println("id为:"+t.id+",name为:"+t.name);      1
        }       1
        Student ss = hh.get(2000);   //通过key值获取value      1
        System.out.println("id为:"+ss.id+",name为:"+ss.name);       1
        hh.remove(2000);       //通过key值删除数据      2
        //哈希表的遍历      2
        for(Student t:hh.values()){      2
            System.out.println("id为:"+t.id+",name为:"+t.name);      2
        }      2
    }      2
}
```


**运行结果**

### ![](https://img-blog.csdnimg.cn/20200715224900920.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
HashTable

     Hashtable继承Map接口，实现一个key-value映射的哈希表。任何非空（non-null）的对象都可作为key或者value。 添加数据使用put(key, value)，取出数据使用get(key)，这两个基本操作的时间开销为常数。 Hashtable通过initial capacity和load factor两个参数调整性能。通常缺省的load factor 0.75较好地实现了时间和空间的均衡。增大load factor可以节省空间但相应的查找时间将增大，这会影响像get和put这样的操作。   
    由于作为key的对象将通过计算其散列函数来确定与之对应的value的位置，因此任何作为key的对象都必须实现hashCode和equals方法。hashCode和equals方法继承自根类Object，如果你用自定义的类当作key的话，要相当小心，按照散列函数的定义，如果两个对象相同，即obj1.equals(obj2)=true，则它们的hashCode必须相同，但如果两个对象不同，则它们的hashCode不一定不同，如果两个不同对象的hashCode相同，这种现象称为冲突，冲突会导致操作哈希表的时间开销增大，所以尽量定义好hashCode()方法，能加快哈希表的操作。   
　如果相同的对象有不同的hashCode，对哈希表的操作会出现意想不到的结果（期待的get方法返回null），要避免这种问题，只需要牢记一条：要同时复写equals方法和hashCode方法，而不要只写其中一个。   
　Hashtable是同步的。

```
import java.util.Hashtable;      
public class Main {      
    public static void main(String[] args) {      
        Hashtable<Integer,Student> hh = new Hashtable<Integer, Student>();       
        hh.put(1000,new Student(1000,"xie"));   //添加一个元素      
        hh.put(2000,new Student(2000,"gong"));      
        hh.put(3000,new Student(3000,"zi"));      
        hh.put(4000,new Student(4000,"ai"));       1
        //哈希表的遍历      1
        for(Student t:hh.values()){      1
            System.out.println("id为:"+t.id+",name为:"+t.name);      1
        }       1
        Student ss = hh.get(2000);   //通过key值获取value      1
        System.out.println("id为:"+ss.id+",name为:"+ss.name);       1
        hh.remove(2000);       //通过key值删除数据       2
        //哈希表的遍历      2
        for(Student t:hh.values()){      2
            System.out.println("id为:"+t.id+",name为:"+t.name);      2
        }      2
    }      2
}
```


运行截图

![](https://img-blog.csdnimg.cn/20200715230703718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### WeakHashMap

WeakHashMap是一种改进的HashMap，它对key实行“弱引用”，如果一个key不再被外部所引用，那么该key可以被GC回收。

comparator和comparable的区别
========================

*   Comparable 是排序接口，若一个类实现了 Comparable 接口，就意味着 “该类支持排序”。
*   而 Comparator 是比较器；

我们若需要控制某个类的次序，可以建立一个 “该类的比较器” 来进行排序。 Comparable应该比较固定，和一个具体类相绑定，而 Comparator 比较灵活，它可以被用于各个需要比较功能的类使用。可以说 Comparable属于 “静态绑定”，而Comparator可以 “动态绑定”。   
  我们不难发现：Comparable 相当于 “内部比较器”，而 Comparator 相当于 “外部比较器”。

关于Comparable和Comparator，可以查看上面 ArrayList 这节的代码。

相关文章：[Java中集合类的操作类Collections](https://xie1997.blog.csdn.net/article/details/107373035)