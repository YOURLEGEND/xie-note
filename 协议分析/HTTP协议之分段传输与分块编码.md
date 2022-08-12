**目录**

[数据的分段传输](#t0)

[数据的分块编码(transfer-encoding)](#t1)

* * *

前置知识：[HTTP协议](https://blog.csdn.net/qq_36119192/article/details/82942185)

数据的分段传输
-------

我们都知道http协议是由TCP协议封装而来的应用层协议。我们和服务器之间的每次http交互都要进行三次握手和四次挥手。那么，服务器端怎么判断客户端传来的数据已经发送完了，然后断开这次tcp连接呢？我们客户端在发送给服务器端报文中有一个Connection 字段，一般这个值为 close。也就是说这次数据传输完成了，服务器就会断开这次tcp连接。但是，当我们要传送的数据量比较大，一次传输不能传输完成时，该如何办呢？这就要用到数据的分段传输了。

当使用分段传输时，请求头中的 Connection字段的值为：keep-alive ，最后一个数据包的Connection字段值为：close。

当服务器端收到Connection值为keep-alive的数据包时，会先将它存储在一个缓冲区中，当收到 Connection 值为 close 的数据包时，即表明这次数据传输完成！

通常，Content-Length 消息头字段表示数据的长度。数据的长度很重要，因为服务器端需要知道哪里是应答消息的结束，以及后续应答消息的开始。然而，使用分段传输，数据分解成一系列数据块，并以一个或多个块发送，这样可以发送数据而不需要预先知道发送内容的总大小。通常数据块的大小是一致的，但也不总是这种情况。于是我们分段进行传输数据的每一次请求的Content-Length是我们这次请求的大小，当服务器端收到 Connection值为close的请求消息后，就会把之前的Content-Length加起来，即是我们请求的数据的总大小了。

数据的分块编码(transfer-encoding)
--------------------------

数据的分块编码就是将完整的请求数据，分块进行编码传输。在请求头中加入这么一个字段：  Transfer-Encoding: chunked ，即表示这个报文采用了分段编码，分段编码只适用于 **POST** 提交方式。

这时，POST请求报文中的数据部分需要改为用一系列分块来传输。每个分块包含十六进制的长度值和数据，空格也算一个长度值，长度值独占一行，最后需要用0独占一行表示编码结束。并在0后空两行表示数据包结束，不然点击提交按钮后会看到一直处于waiting状态。

![](https://img-blog.csdnimg.cn/20190323125235304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[在HTTP协议层面绕过WAF](https://www.freebuf.com/news/193659.html)

                  [利用分块传输吊打所有WAF](https://www.freebuf.com/articles/web/194351.html)

                  [HTTP 协议中的 Transfer-Encoding](https://imququ.com/post/transfer-encoding-header-in-http.html)