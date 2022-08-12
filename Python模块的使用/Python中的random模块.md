**目录**

[random](#t0)

[random()方法](#t1)

[uniform(start,end)方法](#t2)

[randint(start,end) 方法](#t3)

[randrange(start,end)方法](#t4)

[choice()方法](#t5)

[shuffle()方法](#t6)

[sample()方法](#t7)

* * *

random
------

random模块用于生成随机数。

### random()方法

该方法用于生成 \[0,1) 的随机浮点数，精确到小数点后16/17 位，返回 float 类型的数据

```
>>import random      
>>for i in range(5):      
>>    a=random.random()    #0~1内的浮点数      
>>    print(a)       
0.23722264714483243      
0.5150174850564413      
0.05832113522192983      
0.8573245832945965      1
0.6266143144580136
```


### uniform(start,end)方法

该方法用于生成指定范围内的浮点数，start 和 end 参数必须指定，返回 float 类型的数据

```
>>import random      
>>for i in range(5):      
>>    a=random.uniform(10,20)  #10~20内的浮点数      
>>    print(a)      
12.891860048693538      
19.67541906627014      
12.752648953257308      
17.983984929987      
10.102975002564161
```


### randint(start,end) 方法

该方法用于生成指定范围内的整数，必须指定 start 和 end 参数，返回 int 类型的数据

```
>>import random      
>>for i in range(10):      
>>    a=random.randint(10,20)    //返回10-20内的整数，包含10和20      
>>    print(a)
```


### randrange(start,end)方法

该方法也是用于生成指定范围内的整数，end参数必须指定，start参数可以不指定。返回 int 类型的数据

```
>>import random      
>>for i in range(30):      
>>    a=random.randrange(10)        //返回0-9内的整数,[0,10)      
>>    b=random.randrange(10,20)     //返回10-19的整数,[10,20)
```


### choice()方法

choice()方法接收一个可迭代的数据，可以是字符串、列表、元组，然后随机返回数据中的一项

```
import random      
print(random.choice("hello"))      
print(random.choice([1,2,3,4,5,6]))      
print(random.choice(('a','b','c','d','e','f','g')))      
######################      
o      
3      
d
```


### shuffle()方法

shuffle()方法将序列 (可以是列表或者元组) 的所有元素随机排序，是在原序列的基础上修改

```
>>import random      
>>list=[1,2,3,4,5]      
>>random.shuffle(list)      
>>print(list)      
[4, 3, 1, 5, 2]
```


### sample()方法

sample( seq, n ) 方法从指定的序列中，随机的截取指定长度的片断并返回，不在原序列的基础上修改

```
>>import random      
>>list=[1,2,3,4,5]      
>>print(random.sample(list,3))      
[5, 1, 4]
```
