**目录**

[管道](#t0 "管道")

[命名管道Names Pipes](#t1 "命名管道Names Pipes")

[查看管道列表](#t2 "查看管道列表")

[命名管道的创建与访问](#t3 "命名管道的创建与访问")

[命名管道的利用](#t4 "命名管道的利用")

[绕过防火墙](#t5 "绕过防火墙")

[模拟令牌(System权限)](#t6 "模拟令牌(System权限)")

[C2 信道](#t7 "C2 信道")

[MS17010和Named Pipe](#t8 "MS17010和Named Pipe")

* * *

管道
--

      讲命令管道之前先来讲下管道。管道并不是什么新鲜事物，它是一项古老的技术，可以在很多操作系统（Unix、Linux、Windows 等）中找到，其本质是是用于进程间通信的**共享内存区域**，确切的说应该是**线程**间的**通信方法**（IPC）。

      管道是一个有两端的对象。一个进程向管道写入信息，而另外一个进程从管道读取信息。进程可以从这个对象的一个端口写数据，从另一个端口读数据。创建管道的进程称为管道服务器（**Pipe Server**），而连接到这个管道的进程称为管道客户端（**Pipe Client**）。

在 Windows 系统中，存在两种类型的管道:：

*   匿名管道Anonymous pipes：匿名管道是基于字符和半双工的（即单向），只能本地使用
*   命名管道Named pipes：命名管道则强大的多，它是面向消息和全双工的，同时还允许网络通信，用于创建客户端/服务器系统。可通过名称引用；支持多客户端连接；支持双向通信；支持异步重叠 I/O

由于匿名管道单向通信，且只能在本地使用的特性，一般用于程序输入输出的重定向，如一些后门程序获取 cmd 内容等等，在实际攻击过程中利用不多，因此就不过多展开讨论。本文的主要内容还是命名管道Named Pipes。

命名管道Names Pipes
---------------

       **命名管道**是一个具有名称，可在同一台计算机的不同进程之间或在跨越一个网络的不同计算机的不同进程之间，支持**可靠的**、**单向或双向**的数据通信管道。命名管道的所有实例拥有**相同的名称**，但是每个实例都有其自己的缓冲区和句柄，用来为不同客户端提供独立的管道。任何进程都可以访问命名管道，并接受安全权限的检查，通过命名管道使相关的或不相关的进程之间的通讯变得异常简单。

      用命名管道来设计跨计算机应用程序实际非常简单，并不需要事先深入掌握底层网络传送协议（如TCP、UDP、IP、IPX）的知识。这是由于命名管道利用了微软网络提供者（MSNP）重定向器通过同一个网络在各进程间建立通信，这样一来，应用程序便不必关心网络协议的细节。

     任何进程都可以成为服务端和客户端双重角色，这使得点对点双向通讯成为可能。在这里，管道服务端进程指的是创建命名管道的一端，而管道客户端指的是连接到命名管道某个实例的一端。

总结：

*   命名管道的名称在本系统中是唯一的。
*   命名管道可以被任意符合权限要求的进程访问。
*   命名管道只能在本地创建。
*   命名管道是双向的，所以两个进程可以通过同一管道进行交互。
*   多个独立的管道实例可以用一个名称来命名。例如几个客户端可以使用名称相同的管道与同一个服务器进行并发通信。
*   命名管道的客户端可以是本地进程（本地访问：`\.\pipe\PipeName`）或者是远程进程（访问远程： `\ServerName\pipe\PipeName`）。
*   命名管道使用比匿名管道灵活，服务端、客户端可以是任意进程，匿名管道一般情况下用于父子进程通讯。

### 查看管道列表

想要查看管道列表需要具有一定的本地操作权限。以下查看管道列表方法均是在本地查看。

**Process Explorer查看所有程序的命名管道**

Find——>Find Handle or DLL ——> \\Device\\NamedPipe

![](https://img-blog.csdnimg.cn/20210106160217797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**powershell查看命名管道**

```
PowershellV3以下      
[System.IO.Directory]::GetFiles("\\.\\pipe\\")       
PowershellV3以上      
[System.IO.Directory]::GetFiles("\\.\\pipe\\")      
或      
Get-ChildItem \\.\\pipe\\
```


![](https://img-blog.csdnimg.cn/20210106153119790.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210106153400280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**chrome中查看命名管道**

```
file://.//pipe//
```


![](https://img-blog.csdnimg.cn/20210106153613320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

正常情况下，管道客户端是无法直接获取管道服务列表的。因此管道客户端**Pipe Client**要想连接管道服务端**Pipe Server**，就必须得知道服务端的管道名称。

### 命名管道的创建与访问

创建命名管道只能在本机上创建，且得具有一定的权限。管道名称字符串可以包含反斜杠以外的任何字符，包括数字和特殊字符。整个管道名称字符串最多可以包含 256 个字符。管道名称**不区分大小写**。

**整个创建过程如下：**

1.  服务端进程调用 CreateNamedPipe 函数来创建一个有名称的命名管道，在创建命名管道的时候必须指定一个命名管道名称（pipe name）。因为 Windows 允许同一个本地的命名管道名称有多个命名管道实例。所以，服务器进程在调用 CreateNamedPipe 函数时必须指定最大允许的实例数（0 -255）， CreateNamedPipe 函数成功返回后，服务器进程得到一个指向一个命名管道实例的句柄。
2.  然后，服务器进程就可以调用 ConnectNamedPipe 来等待客户的连接请求，这个 ConnectNamedPipe 既支持同步形式，又支持异步形式。若服务器进程以同步形式调用 ConnectNamedPipe 函数，（同步方式也就是如果没有得到客户端的连接请求，则会一直等到有客户端的连接请求）那么，当该函数返回时，客户端与服务器之间的命名管道连接也就已经建立起来了。
3.  在已经建立了连接的命名管道实例中，服务端进程就会得到一个指向该管道实例的句柄，这个句柄称之为服务端句柄。

管道的访问方式相当于指定管道服务端句柄的读写访问，下表列出了可以使用 **CreateNamedPipe** 指定的每种访问方式的等效常规访问权限：

| 
访问方式

 | 

访问权限

 | 说明 |
| --- | --- | --- |
| 

PIPE\_ACCESS\_INBOUND

 | 

GENERIC\_READ

 | 如果管道服务器使用 PIPE\_ACCESS\_INBOUND 创建管道，则该管道对于管道服务器是只读的，对于管道客户端是只写的。 |
| 

PIPE\_ACCESS\_OUTBOUND

 | 

GENERIC\_WRITE

 | 如果管道服务器使用 PIPE\_ACCESS\_OUTBOUND 创建管道，则该管道对于管道服务器是只写的，对于管道客户端是只读的。 |
| 

PIPE\_ACCESS\_DUPLEX

 | 

GENERIC\_READ | GENERIC\_WRITE

 | 如果管道服务器使用 PIPE\_ACCESS\_DUPLEX 创建管道，则该管道对于管道服务器和管道客户端都是可以读/写的。 |

同时，管道客户端使用 CreateFile 函数连接到命名管道时必须在 dwDesiredAccess 参数中指定一个和管道服务端（创建管道时指定的访问模式）相兼容的访问模式。

例如，当管道服务端创建管道时指定了 PIPE\_ACCESS\_OUTBOUND 访问模式，那么，管道客户端就必须指定 GENERIC\_WRITE 访问模式。

**使用powershell创建管道**

```
$PipeSecurity = New-Object System.IO.Pipes.PipeSecurity      
$AccessRule = New-Object System.IO.Pipes.PipeAccessRule( "Everyone", "ReadWrite", "Allow" )      
$PipeSecurity.AddAccessRule($AccessRule)      
$pipe = New-Object System.IO.Pipes.NamedPipeServerStream("test","InOut",10, "Byte", "None", 1024, 1024, $PipeSecurity)      
write "Named Pipes Create Success!"      
$pipe.WaitForConnection()      
$pipeReader = new-object System.IO.StreamReader($pipe)      
$Null = $pipereader.ReadToEnd()
```


![](https://img-blog.csdnimg.cn/20210106162959796.png)

创建完成之后，使用 \[System.IO.Directory\]::GetFiles("\\\\.\\\\pipe\\\\") 命令可以查看到我们创建的 test 命名管道

![](https://img-blog.csdnimg.cn/2021010616302019.png)

**命名管道的访问**

客户端访问（连接）服务端的过程如下：

客户端进程调用 CreateFile 函数连接到一个正在等待连接的命名管道上，在这里客户端需要指定将要连接的命名管道的名称，当 CreateFile 成功返回后，客户进程就得到了一个指向已经建立连接的命名管道实例的句柄，到这里，服务器进程的 ConnectNamedPipe 也就完成了其建立连接的任务。

虽然命名管道支持跨计算机跨网的访问连接，但是会受到访问控制列表（ACL）或者说本地策略限制。

在 windows server 2003 及以下的版本中，默认开启了匿名管道通信；win2003之后的系统默认禁止匿名管道通信。

windows server 2003 的默认本地策略，默认允许部分管道匿名访问。

![](https://img-blog.csdnimg.cn/20210106164956436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

而windows server 2008 的默认本地策略，完全禁止匿名访问管道。

![](https://img-blog.csdnimg.cn/20210106164726669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)  
也就是说，在win2003以后，或者说在禁止匿名访问命名管道的系统中，如果想要实现远程管道访问，与管道进行通信，就必须需要一个有效的身份进行验证。比如建立 smb 连接，或者建立 IPC 连接等。

如下，我们在192.168.10.132机器上使用脚本创建了一个命名管道test，在另一台机器上向管道写入数据提示用户名或密码不正确。

![](https://img-blog.csdnimg.cn/20210106165306263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是当我们与192.168.10.132建立IPC连接后，就不会提示用户名或密码不正确了

![](https://img-blog.csdnimg.cn/20210106165619268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

命名管道的利用
-------

### 绕过防火墙

这里说的防火墙，是指 windows 系统自带的防火墙，类似于 UAC，需要用户点击允许访问才可放行。当尝试使用 `Bind()` 绑定一个 TCP Socket 时，Defender 就会自动弹窗提示是否允许此程序进行网络连接，在高权限下，通过修改防火墙规则，可以轻松的绕过这一限制，但是，当权限不足时，就需要另外想办法了。

这个时候我们还有另外的办法就是利用命名管道，命名管道网络通信使用了未加密的 SMB 协议（端口 445）或 DCE\\RPC（端口 135）。在 Windows 中，通常默认允许 SMB 协议出入站（如果当年没有因为 WannaCry 主动做策略限制的情况下），因此，如果有什么功能或机制可以用于与外部机器进行通信的，SMB 协议无疑是一种很好的选择。所以我们可以基于命名管道与外部机器进行通信，从而建立控制通道。CobaltStrike中的SMB Beacon就是利用了命名管道。

### 模拟令牌(System权限)

这也是命名管道中常见的一种方法，一般可以用来提权操作，Metasploit 中的 getsystem 也就是这个原理，官方给出的内容为：

> **Technique 1** creates a named pipe from Meterpreter. It also creates and runs a service that runs cmd.exe /c echo “some data” >\\.\\pipe\[random pipe here\]. When the spawned cmd.exe connects to Meterpreter’s named pipe, Meterpreter has the opportunity to impersonate that security context. Impersonation of clients is a named pipes feature. The context of the service is SYSTEM, so when you impersonate it, you become SYSTEM.

大体意思也就是说，msf 会创建一个命名管道，然后创建一个服务去运行`cmd.exe /c echo “some data” >\\.\pipe\[random pipe here]`，当 cmd 连接到 Meterpreter 的命名管道的时候，因为服务是 system 权限，msf 也就得到了一个 system 的 shell。

使用之前的 ps 脚本也可以完成此操作。

具体过程可以参考以下两篇文章：

> https://www.anquanke.com/post/id/190207#h3-7
> 
> https://decoder.cloud/2019/03/06/windows-named-pipes-impersonation/

但是需要强调的是，该功能只能作为本地使用，模拟令牌产生的用户进程无法用于任何远程认证。模拟客户端产生进程，是通过提取当前进程 token 产生的，而 token 中只存在 sid 和 acl 等信息，其中不包含认证所需要的密码、hash，所以只能用于本地权限认证。如果用于远程认证就会出现权限鉴定出错的情况 失败的情况。

如果客户端采用 SECURITYDELEGATION 权限连接服务端，则允许服务端任意模拟客户端权限，包括本地和远程认证，但是根据官网文档，服务端要接受 SECURITYDELEGATION 权限的委派。

开启委派后也可以实现委派级别的模拟。具体就不在这里展开了。

### **C2 信道**

命名管道还常被用作 C2 信道，通讯执行命令。

![](https://img-blog.csdnimg.cn/20210106173521479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如图所示，每个终端将为每个直接连接的子终端提供一个命名管道服务器和一个命名管道客户端。服务器监听管道名称，并等待客户端的连接。客户端连接到特定主机名和管道名称的服务器，从而创建命名管道。管道的每一个终端都有从另一个终端读取和写入的能力，即，将 `Payload` 运行（注入）后，创建了自定义命名管道（作服务端），等待连接即可，这一过程被称为 " 绑定 "（Bind）连接。

这种连接方式很常见，如 Metasploit 和 Cobalt Strike 都有类似功能。

MS17010和Named Pipe
------------------

我们经常在打MS17010的时候，会看到提示如下。主要原因是zzz\_exploit.py 以及 MSF中的 auxiliary/admin/smb/ms17\_010\_command 和 exploit/windows/smb/ms17\_010\_psexec 模块都是依赖于Named Pipe进行攻击的。而大于win2003的机器，默认是关闭了所有的可匿名访问的命名管道，所有用这些工具打win2003之后的系统会提示找不到Named Pipe。换个工具或模块打即可。

```
Not found accessible named pipe      
或      
- Unable to find accessible named pipe!
```


![](https://img-blog.csdnimg.cn/20210106191107225.png)

![](https://img-blog.csdnimg.cn/20210106191208927.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210106191302753.png)

![](https://img-blog.csdnimg.cn/20210106191537865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[浅谈 windows 命名管道 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1625924 "浅谈 windows 命名管道 - 云+社区 - 腾讯云")

                [Windows 命名管道研究初探 - 安全客，安全资讯平台](https://www.anquanke.com/post/id/190207#h3-7 "Windows 命名管道研究初探 - 安全客，安全资讯平台")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-e66e052f98274a60b0eb83273cd089e7)[Linux实用命令](https://edu.csdn.net/skill/gml/gml-e66e052f98274a60b0eb83273cd089e7)[管道](https://edu.csdn.net/skill/gml/gml-e66e052f98274a60b0eb83273cd089e7)11176 人正在系统学习中