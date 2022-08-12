**目录**

[if else循环](#t0)

[for循环](#t1)

[while循环](#t2)

[and、or、not](#t3) 

[in、not in](#t4)

* * *

if else循环
=========

任何 非0 、非 "" 和 非空（None）值为 True

0 或者 None 为 False

```
age=20      
if age > 17:      
    print("YES")                
elif age>15:      
    print("yes")      
else:      
    print("no")      
## YES      
因为20>17,所以打印出YES，就不判断下面的了
```


for循环
=====

```
for i in range(5):      
    if i>2:      
        print("yes")      
        break      
    else:      
        print("no")      
#no      
#no      
#no      1
#yes
```


**写一个九九乘法表**

```
for i in range(1,10):      
    for j in range(1,i+1):      
        print("{}*{}={}".format(j,i,i*j),end=' ')      
    print("")       
或      
for i in range(1,10):      
    j=1      
    while True:      1
        if j>i:      1
            break      1
        else:      1
             print("{}*{}={}".format(j,i,i*j),end=' ')      1
             j+=1      1
    print(" ")
```


![](https://img-blog.csdnimg.cn/20201007105516161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

while循环
=======

```
a=3      
while a<5:      
    a=a+1      
    print(a)      
## 4      
## 5
```


while  else

当while中的执行完了，就执行else中的

```
a=3      
while a<5:      
    a=a+1      
    print(a)      
else:      
    print("yes")       
## 4      
## 5      1
## yes
```


and、or、not 
===========

```
a=1      
b=0      
c=1       
#与，只要一个为真即为真      
if a or b:      
	print("true")      
else:      
	print("false")      1
print("*"*50)       1
#或，只要一个为假即为假      1
if a and b:      1
	print("true")      1
else:      1
	print("false")      1
print("*"*50)       1
#非      2
if c:      2
	print("true")      2
else:      2
	print("false")       2
if not c:      2
	print("true")      2
else:      2
	print("false")
```


![](https://img-blog.csdnimg.cn/20201007105444466.png) 

in、not in
=========

```
a="aa"      
b=["aa","bb","cc"]      
if a in b:      
	print("true")      
else:      
	print("false")       
print("*"*50)       1
if a not in b:      1
	print("true")      1
else:      1
	print("false")       1
print("*"*50)
```


![](https://img-blog.csdnimg.cn/20201007105141745.png)