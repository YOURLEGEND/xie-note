**目录**

[Socket](#t0)

[服务端(server.py)](#t1)

[客户端(client.py)](#t2)

[socket中的一些常用方法](#t3)

[Socket 对象(内建)方法](#t4)

[Python Internet 模块](#t5)

* * *

Python3 提供了两个级别访问的网络服务：

*   低级别的网络服务支持基本的 Socket，它提供了标准的 BSD Sockets API，可以访问底层操作系统Socket接口的全部方法。
*   高级别的网络服务模块 SocketServer， 它提供了服务器中心类，可以简化网络服务器的开发。

Socket
------

[Socket](https://so.csdn.net/so/search?q=Socket&spm=1001.2101.3001.7020)又称"套接字"，应用程序通常通过"套接字"向网络发出请求或者应答网络请求，使主机间或者一台计算机上的进程间可以通讯。

Python 中，我们使用 socket 模块的 socket 函数来创建一个 socket 对象。语法格式如下：

> socket.socket ( family ，type ，proto)

参数

*   family: 套接字家族可以使 AF\_UNIX 或者 AF\_INET
*   type: 套接字类型，可以根据是面向连接的 SOCK\_STREAM 还是非连接的 SOCK\_DGRAM
*   protocol: 一般不填，默认为 0

### 服务端(server.py)

```
import socket       
server=socket.socket(socket.AF_INET,socket.SOCK_STREAM)  #创建socket对象，面向连接的      
server.bind(("127.0.0.1",9999))    #绑定主机名和端口号      
server.listen(10)                #设置最大连接数，超过后排队      
clientsock,addr=server.accept()  #建立与客户端的连接，返回(socket object, address info)元组对象      
print("addr=%s %s"%(addr,type(addr)))        
print("客户端ip地址为：%s  端口号为：%s"%addr)      
clientsock.send("欢迎来到服务端".encode('utf-8'))  #python3要求发送byte型的数据，所以我们将它以utf-8的形式转换为bytes类型的      1
msg=clientsock.recv(1024)    #接收客户端发来的消息,msg2为bytes类型的数据      1
print(msg.decode('utf-8'))   #我们将bytes类型数据转换成str字符型的数据，以utf-8的形式      1
clientsock.close()
```


### 客户端(client.py)

```
import socket       
client=socket.socket(socket.AF_INET,socket.SOCK_STREAM)      
client.connect(("127.0.0.1",9999))  #connect函数接收元组型数据      
msg=client.recv(1024)         #接收从服务器发来的消息，为bytes类型的数据，大小为1024字节的缓冲区      
print(msg.decode('utf-8'))    #我们转化为str字符串类型的数据，以utf-8的形式      
client.send("客户端到此一游".encode('utf-8'))  #python3要求发送bytes类型的数据，所以我们得将它转换      
client.close()
```


![](https://img-blog.csdnimg.cn/20181103145812786.png)![](https://img-blog.csdnimg.cn/20181103145725958.png)

socket中的一些常用方法
--------------

```
import socket       
a=socket.gethostname()     #获得本机的主机名，返回str型数据      
print(a,type(a))        
b=socket.gethostbyname(a)  #根据主机名获取ip地址，返回str型数据，也可以是网络上的域名      
print(b,type(b))       
c=socket.gethostbyaddr(b)  #通过ip获得该主机的一些信息，返回tuple元组型数据      
print(c,type(c))      
#############################################      
Win-10 <class 'str'>      1
192.168.56.1 <class 'str'>      1
('bogon', [], ['192.168.56.1']) <class 'tuple'>
```


Socket 对象(内建)方法
---------------

| 函数 | 描述 |
| --- | --- |
| 服务器端套接字 |
| s.bind() | 绑定地址（host,port）到套接字， 在AF\_INET下,以元组（host,port）的形式表示地址。 |
| s.listen() | 开始TCP监听。backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数量。该值至少为1，大部分应用程序设为5就可以了。 |
| s.accept() | 被动接受TCP客户端连接,(阻塞式)等待连接的到来 |
| 客户端套接字 |
| s.connect() | 主动初始化TCP服务器连接，。一般address的格式为元组（hostname,port），如果连接出错，返回socket.error错误。 |
| s.connect\_ex() | connect()函数的扩展版本,出错时返回出错码,而不是抛出异常 |
| 公共用途的套接字函数 |
| s.recv() | 接收TCP数据，数据以字符串形式返回，bufsize指定要接收的最大数据量。flag提供有关消息的其他信息，通常可以忽略。 |
| s.send() | 发送TCP数据，将string中的数据发送到连接的套接字。返回值是要发送的字节数量，该数量可能小于string的字节大小。 |
| s.sendall() | 完整发送TCP数据，完整发送TCP数据。将string中的数据发送到连接的套接字，但在返回之前会尝试发送所有数据。成功返回None，失败则抛出异常。 |
| s.recvfrom() | 接收UDP数据，与recv()类似，但返回值是（data,address）。其中data是包含接收数据的字符串，address是发送数据的套接字地址。 |
| s.sendto() | 发送UDP数据，将数据发送到套接字，address是形式为（ipaddr，port）的元组，指定远程地址。返回值是发送的字节数。 |
| s.close() | 关闭套接字 |
| s.getpeername() | 返回连接套接字的远程地址。返回值通常是元组（ipaddr,port）。 |
| s.getsockname() | 返回套接字自己的地址。通常是一个元组(ipaddr,port) |
| s.setsockopt(level,optname,value) | 设置给定套接字选项的值。 |
| s.getsockopt(level,optname\[.buflen\]) | 返回套接字选项的值。 |
| s.settimeout(timeout) | 设置套接字操作的超时期，timeout是一个浮点数，单位是秒。值为None表示没有超时期。一般，超时期应该在刚创建套接字时设置，因为它们可能用于连接的操作（如connect()） |
| s.gettimeout() | 返回当前超时期的值，单位是秒，如果没有设置超时期，则返回None。 |
| s.fileno() | 返回套接字的文件描述符。 |
| s.setblocking(flag) | 如果flag为0，则将套接字设为非阻塞模式，否则将套接字设为阻塞模式（默认值）。非阻塞模式下，如果调用recv()没有发现任何数据，或send()调用无法立即发送数据，那么将引起socket.error异常。 |
| s.makefile() | 创建一个与该套接字相关连的文件 |

Python Internet 模块
------------------

以下是 Python 网络编程的一些重要模块：

| 协议 | 功能用处 | 端口号 | Python 模块 |
| --- | --- | --- | --- |
| HTTP | 网页访问 | 80 | httplib, urllib, xmlrpclib |
| NNTP | 阅读和张贴新闻文章，俗称为"帖子" | 119 | nntplib |
| FTP | 文件传输 | 20 | ftplib, urllib |
| SMTP | 发送邮件 | 25 | smtplib |
| POP3 | 接收邮件 | 110 | poplib |
| IMAP4 | 获取邮件 | 143 | imaplib |
| Telnet | 命令行 | 23 | telnetlib |
| Gopher | 信息查找 | 70 | gopherlib, urllib |

文章已被收录至官方知识档案

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-97)[其他](https://edu.csdn.net/skill/python/python-3-97)[网络编程](https://edu.csdn.net/skill/python/python-3-97)84711 人正在系统学习中