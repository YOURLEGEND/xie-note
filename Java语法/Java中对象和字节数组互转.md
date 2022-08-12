首先，创建要转换的类 test ，内容如下。注意，该类一定要实现序列化

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


然后创建 object\_array 类，内容如下

```
package com.company;       
import java.io.*;      
public class object_array   {      
    public static void main(String[] args) throws Exception {      
        test t =new test();      
        System.out.print ( "java class对象转换为字节数组：\n" );      
        byte[] bufobject = getBytesFromObject(t);      
        for(int i=0 ; i<bufobject.length ; i++) {      1
            System.out.print(bufobject[i] + ",");      1
        }      1
        System.out.println ("\n");      1
        System.out.print ("字节数组还原对象：\n");      1
        Object object1 = null;      1
        object1=deserialize(bufobject);      1
        test t1 =(test)object1;      1
        System.out.println ("调用对象的成员变量："+t1.name);      1
        System.out.println ("调用对象的成员函数："+t1.example());      1
    }      2
    public static byte[] getBytesFromObject(Serializable obj) throws Exception {      2
        if (obj == null) {      2
            return null;      2
        }      2
        ByteArrayOutputStream bo = new ByteArrayOutputStream();      2
        ObjectOutputStream oos = new ObjectOutputStream(bo);      2
        oos.writeObject(obj);      2
        return bo.toByteArray();      2
    }      2
    public static Object deserialize(byte[] bytes) {      3
        Object object = null;      3
        try {      3
            ByteArrayInputStream bis = new ByteArrayInputStream(bytes);//      3
            ObjectInputStream ois = new ObjectInputStream(bis);      3
            object = ois.readObject();      3
            ois.close();      3
            bis.close();      3
        } catch (IOException ex) {      3
            ex.printStackTrace();      3
        } catch (ClassNotFoundException ex) {      4
            ex.printStackTrace();      4
        }      4
        return object;      4
    }      4
}
```


运行结果如下：

![](https://img-blog.csdnimg.cn/20200630111841236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

本文项目代码：链接: [https://pan.baidu.com/s/1jaqBdvcrAPuqqdUxJJKKvQ](https://pan.baidu.com/s/1jaqBdvcrAPuqqdUxJJKKvQ) 提取码: v8ij

参考文章：[https://blog.csdn.net/qq\_38376348/article/details/107021408](https://blog.csdn.net/qq_38376348/article/details/107021408)