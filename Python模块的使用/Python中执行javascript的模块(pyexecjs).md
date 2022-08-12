**目录**

[直接在python中构造js函数并执行](#t0)

[读取JS文件并执行](#t1)

* * *

首先安装该模块：**pip install pyexecjs**

### **直接在python中构造js函数并执行**

```
import execjs      
a = "function add(x,y){  return x+y; }"   #一个有参函数      
b = "function out(){  return 'hello'; }"  #一个无参函数       
com1 = execjs.compile(a)      #将js加载进execjs      
com2 = execjs.compile(b)      #将js加载进execjs       
print(com1.call("add",10,20) )   #调用有参函数      
print(com2.call("out"))          #调用无参函数
```


![](https://img-blog.csdnimg.cn/20200414085440265.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 读取JS文件并执行

**1.js**

```
function add(x,y){      
	return x+y;      
}      
function out(){      
	return "hello";      
}
```


**1.py**

```
import execjs      
f = open("1.js",encoding="utf8")      
com = execjs.compile(f.read())      
x = com.call("add",10,20)    #调用有参函数      
y = com.call("out")          #调用无参函数      
print(x)      
print(y)
```


![](https://img-blog.csdnimg.cn/20200414085048968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200414085104943.png)