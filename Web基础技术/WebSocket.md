在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)过程中，好几次碰到了WebSockets。使用BurpSuite抓包，服务器端一直会返回这种类型的数据包。

![](https://img-blog.csdnimg.cn/20200526141445128.png)

本着学习的精神，百度了一番，结果如下：

WebSocket
---------

什么是WebSocket？看过[html5](https://so.csdn.net/so/search?q=html5&spm=1001.2101.3001.7020)的同学都知道，WebSocket protocol 是HTML5一种新的协议。它是实现了浏览器与服务器全双工通信(full-duplex)。HTML5定义了WebSocket协议，能更好的节省服务器资源和带宽并达到实时通讯。现在我们来探讨一下html5的WebSocket

**概念**  
  
HTML5作为下一代WEB标准，拥有许多引人注目的新特性，如Canvas、本地存储、多媒体编程接口、WebSocket 等等。今天我们就来看看具有“Web TCP”之称的WebSocket.  
  
WebSocket的出现是基于Web应用的实时性需要而产生的。这种实时的Web应用大家应该不陌生，在生活中都应该用到过，比如新浪微博的评论、私信的通知，腾讯的WebQQ等。让我们来回顾下实时 Web 应用的窘境吧。  
  
在WebSocket出现之前，一般通过两种方式来实现Web实时用：轮询机制和流技术；其中轮询有不同的轮询，还有一种叫Comet的长轮询。  
  
轮询：这是最早的一种实现实时 Web 应用的方案。客户端以一定的时间间隔向服务端发出请求，以频繁请求的方式来保持客户端和服务器端的同步。这种同步方案的缺点是，当客户端以固定频率向服务 器发起请求的时候，服务器端的数据可能并没有更新，这样会带来很多无谓的网络传输，所以这是一种非常低效的实时方案。  
  
长轮询：是对定时轮询的改进和提高，目地是为了降低无效的网络传输。当服务器端没有数据更新的时候，连接会保持一段时间周期直到数据或状态改变或者 时间过期，通过这种机制来减少无效的客户端和服务器间的交互。当然，如果服务端的数据变更非常频繁的话，这种机制和定时轮询比较起来没有本质上的性能的提 高。  
  
流：常就是在客户端的页面使用一个隐藏的窗口向服务端发出一个长连接的请求。服务器端接到这个请求后作出回应并不断更新连接状态以保证客户端和服务 器端的连接不过期。通过这种机制可以将服务器端的信息源源不断地推向客户端。这种机制在用户体验上有一点问题，需要针对不同的[浏览器](http://www.111cn.net/list-209/)设计不同的方案来改进 用户体验，同时这种机制在并发比较大的情况下，对服务器端的资源是一个极大的考验。  
  
上述方式其实并不是真正的实时技术，只是使用了一种技巧来实现的模拟实时。在每次客户端和服务器端交互的时候都是一次 HTTP 的请求和应答的过程，而每一次的 HTTP 请求和应答都带有完整的 HTTP 头信息，这就增加了每次传输的数据量。但这些方式最痛苦的是开发人员，因为不论客户端还是服务器端的实现都很复杂，为了模拟比较真实的实时效果，开发人员 往往需要构造两个HTTP连接来模拟客户端和服务器之间的双向通讯，一个连接用来处理客户端到服务器端的数据传输，一个连接用来处理服务器端到客户端的数 据传输，这不可避免地增加了编程实现的复杂度，也增加了服务器端的负载，制约了应用系统的扩展性。  
  
基于上述弊端，实现Web实时应用的技术出现了，WebSocket通过浏览器提供的API真正实现了具备像C/S架构下的桌面系统的实时通讯能 力。其原理是使用JavaScript调用浏览器的API发出一个WebSocket请求至服务器，经过一次握手，和服务器建立了TCP通讯，因为它本质 上是一个TCP连接，所以数据传输的稳定性强和数据传输量比较小。  
 

**WebSocket 协议**  
  
WebSocket 协议本质上是一个基于 TCP 的协议。为了建立一个 WebSocket 连接，客户端浏览器首先要向服务器发起一个 HTTP 请求，这个请求和通常的 HTTP 请求不同，包含了一些附加头信息，其中附加头信息”Upgrade: WebSocket”表明这是一个申请协议升级的 HTTP 请求，服务器端解析这些附加的头信息然后产生应答信息返回给客户端，客户端和服务器端的 WebSocket 连接就建立起来了，双方就可以通过这个连接通道自由的传递信息，并且这个连接会持续存在直到客户端或者服务器端的某一方主动的关闭连接。  
  
下面我们来详细介绍一下 WebSocket 协议，由于这个协议目前还是处于草案阶段，版本的变化比较快，我们选择目前最新的 draft-ietf-hybi-thewebsocketprotocol-17 版本来描述 WebSocket 协议。因为这个版本目前在一些主流的浏览器上比如 Chrome,、FireFox、Opera 上都得到比较好的支持。通过描述可以看到握手协议

客户端发到服务器的内容:

```
GET /chat HTTP/1.1      
Host: server.example.com      
Upgrade: websocket      
Connection: Upgrade      
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==      
Origin: http://example.com      
Sec-WebSocket-Protocol: chat, superchat      
Sec-WebSocket-Version: 13
```


从服务器到客户端的内容:

```
HTTP/1.1 101 Switching Protocols      
Upgrade: websocket      
Connection: Upgrade      
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=      
Sec-WebSocket-Protocol: chat
```


这些请求和通常的 HTTP 请求很相似，但是其中有些内容是和 WebSocket 协议密切相关的。我们需要简单介绍一下这些请求和应答信息，”Upgrade:WebSocket”表示这是一个特殊的 HTTP 请求，请求的目的就是要将客户端和服务器端的通讯协议从 HTTP 协议升级到 WebSocket 协议。其中客户端的Sec-WebSocket-Key和服务器端的Sec-WebSocket-Accept就是重要的握手认证信息了，这些内容将在服 务器端实现的博文中讲解。

参考文章：[https://www.cnblogs.com/zxtceq/p/6860000.html](https://www.cnblogs.com/zxtceq/p/6860000.html)