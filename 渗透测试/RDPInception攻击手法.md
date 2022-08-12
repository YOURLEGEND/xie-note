在讲RDPInception攻击手段之前，我们先了解一下RDP远程桌面(Remote Desktop Protocol)协议。RDP远程桌面协议（Remote Desktop Protocol）是一个多通道（multi-channel）的协议，让网站管理员通过网络连接远程操控服务器。RDP协议默认使用的端口是 3389。

由于RDPInception攻击要求的条件比较苛刻，在现实生产环境中几乎不会有这种环境。RDPInception只是一种理论上可以攻击成功的手法！

**RDPInception要求的条件**：远程主机在远程桌面控制服务器时，开启共享C盘。但是由于这个选项默认是不勾选的，所以现实生产环境人几乎没有人会去勾选这个选项。

![](https://img-blog.csdnimg.cn/201904281539559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)![](https://img-blog.csdnimg.cn/20190428154020331.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### RDPInception攻击过程：

黑客控制了外网的一台web服务器A，但是，他现在的目标是要控制位于内网的管理员主机C。而管理员主机C控制web服务器A的方法是，先通过RDP协议登录内网跳板机B，在跳板机B上再通过RDP协议登录外网web服务器。黑客现在在外网web服务器A上发起了RDPInception攻击，一旦web服务器A受到攻击“感染”，接下来只需静静等待管理员发起RDP连接请求，其余的后续攻击可由程序自动完成。这种攻击技术无需用户凭证或其它漏洞利用，可在受限网络环境的横向渗透测试中发挥作用。

条件：主机C和跳板机B在RDP远程登录的时候，开启了共享C盘!

![](https://img-blog.csdnimg.cn/20190428161547428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

未完待续。。。。。

参考文章：[RDPInception：一种利用远程桌面的新型攻击方式](https://www.freebuf.com/articles/system/139156.html)