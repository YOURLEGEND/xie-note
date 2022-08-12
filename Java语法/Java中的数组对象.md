**目录**

[数组的定义](#t0)

[数组的长度](#t1) 

[数组的拷贝](#t2)

[数组的遍历](#t3)

* * *

Java 中提供的[数组](https://so.csdn.net/so/search?q=%E6%95%B0%E7%BB%84&spm=1001.2101.3001.7020)是用来存储固定大小的同类型元素。Java里的数组是个对象，是引用数据类型。

### 数组的定义

```
//数组的定义      
short[] aa = {1,2,3};  //定义一个数组aa,并初始化      
short[] bb = new short[3]; //申请一个数组bb,未初始化
```


### 数组的长度 

```
//数组的长度      
int len = aa.length;   //数组bb的长度
```


### 数组的拷贝

Java中拷贝函数是 System.arraycopy，但是Java中很少进行对象的拷贝，一般只对基本类型对象（byte，short，int，long）数组进行拷贝，无法对class类型对象数组进行拷贝。

```
//数组的拷贝      
System.arraycopy(aa,0,bb,0,3);  //将数组aa中的值拷贝到数组bb中
```


### 数组的遍历

```
//数组的遍历一      
for(byte i=0;i<len;i++){      
    System.out.println(aa[i]);      
}       
//数组的遍历二      
for(short i:aa){      
    System.out.println(i);      
}
```


![](https://img-blog.csdnimg.cn/20200706235757172.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200706235832670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

本文代码：链接: [https://pan.baidu.com/s/1iHSFSktzBWOJp210Gcsrkw](https://pan.baidu.com/s/1iHSFSktzBWOJp210Gcsrkw)   提取码: itk8