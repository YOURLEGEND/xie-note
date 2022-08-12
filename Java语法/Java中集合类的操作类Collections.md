Collections 是集合类的一个工具类，其提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作  
1) 排序(Sort)  
    使用sort方法可以根据元素的自然顺序，对指定列表进行排序。列表中的所有元素都必须实现 Comparable 接口。或此列表内的所有元素都必须是使用指定比较器可相互比较的   Collections.sort(list , new Comparator(){ } );  
2) 混排（Shuffling）  
    混排算法所做的正好与 sort 相反: 它打乱在一个 List 中可能有的任何排列的踪迹。也就是说，基于随机源的输入重排该 List, 这样的排列具有相同的可能性（假设随机源是公正的）。这个算法在实现一个碰运气的游戏中是非常有用的。例如，它可被用来混排代表一副牌的 Card 对象的一个 List 。另外，在生成测试案例时，它也是十分有用的。Collections.Shuffling(list)  
3) 反转(Reverse)  
    使用Reverse方法可以将元素进行反转。Collections.reverse(list)  
4) 替换所有的元素(Fill)  
     使用指定元素替换指定列表中的所有元素。Collections.fill(li,"aaa");  
5) 拷贝(Copy)  
    用两个参数，一个目标 List 和一个源 List, 将源的元素拷贝到目标，并覆盖它的内容。目标 List 至少与源一样长。如果它更长，则在目标 List 中的剩余元素不受影响。Collections.copy(list,li): 后面一个参数是目标列表 ,前一个是源列表  
6) 返回Collections中最小元素(min)  
    根据指定比较器产生的顺序，返回给定 collection 的最小元素。collection 中的所有元素都必须是通过指定比较器可相互比较的  
Collections.min(list)  
7) 返回Collections中最大元素(max)  
     根据指定比较器产生的顺序，返回给定 collection 的最大元素。collection 中的所有元素都必须是通过指定比较器可相互比较的  
  Collections.max(list)  
8) lastIndexOfSubList  
    返回指定源列表中最后一次出现指定目标列表的起始位置，int count = Collections.lastIndexOfSubList(list,li);  
9) IndexOfSubList  
    返回指定源列表中第一次出现指定目标列表的起始位置，int count = Collections.indexOfSubList(list,li);  
10) Rotate  
根据指定的距离循环移动指定列表中的元素，Collections.rotate(list,-1)；如果是负数，则正向移动，正数则方向移动

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
import java.util.ArrayList;      
import java.util.Collections;       
public class Main {      
    public static void main(String[] args) {      
        ArrayList<Student> ss = new ArrayList<Student>();      
        ss.add(new Student(5));      //增加一个元素      
        ss.add(new Student(3));      //增加一个元素      
        ss.add(new Student(7));      //增加一个元素      1
        ss.add(new Student(1));      //增加一个元素      1
        for(Student s:ss){      1
            System.out.println(s.id);      1
        }       1
        System.out.println("-----------排序-----------");      1
        Collections.sort(ss);      1
        for(Student s:ss){      1
            System.out.println(s.id);      1
        }       2
        System.out.println("-----------反转-----------");      2
        Collections.reverse(ss);      2
        for(Student s:ss){      2
            System.out.println(s.id);      2
        }       2
        System.out.println("-----------混排-----------");      2
        Collections.shuffle(ss);      2
        for(Student s:ss){      3
            System.out.println(s.id);      3
        }       3
        System.out.println("-----------返回最小元素-----------");      3
        Student min = Collections.min(ss);      3
        System.out.println(min.id);       3
        System.out.println("-----------返回最大元素-----------");      3
        Student max = Collections.max(ss);      3
        System.out.println(max.id);       4
    }      4
}
```


运行截图

![](https://img-blog.csdnimg.cn/20200716000558476.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[Java中的集合类](https://xie1997.blog.csdn.net/article/details/85083391)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Java技能树](https://edu.csdn.net/skill/java/java-c13b32f668a246cc8e41e04d0b66ef15)[深入研究容器](https://edu.csdn.net/skill/java/java-c13b32f668a246cc8e41e04d0b66ef15)[Collections实用方法](https://edu.csdn.net/skill/java/java-c13b32f668a246cc8e41e04d0b66ef15)44190 人正在系统学习中