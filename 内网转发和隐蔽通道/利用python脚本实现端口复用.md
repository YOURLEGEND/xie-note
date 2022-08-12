**目录**

[端口复用](#t0 "端口复用")

[端口复用的简单实现(python脚本)](#t1 "端口复用的简单实现(python脚本)")

* * *

端口复用
----

端口复用就是在一个开放的端口上，通过对输入的信息进行字符匹配，来运行不同的服务。端口复用只对输入的信息进行字符匹配，不对网络数据进行任何拦截、复制类操作，所以对网络数据的传输性能丝毫不受影响。端口复用常被黑客用来制作后门。在winsock的实现中，对于服务器的绑定是可以多重绑定的，在确定多重绑定使用谁的时候，根据一条原则是谁的指定最明确则将包递交给谁，而且没有权限之分，也就是说低级权限的用户是可以重绑定在高级权限用户启动的端口上的，这是非常重大的一个安全隐患。

### 端口复用的简单实现(python脚本)

这是一个TCP端口复用的脚本，使用python3开发，当运行该脚本后，你访问指定的端口，脚本中的路由会自动将你的流量转发到对于的路由上。该项目是github上的一个脚本。

### ![](https://img-blog.csdnimg.cn/2020022816423074.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

访问目标主机的2222端口，脚本会自动将路由转到80端口

![](https://img-blog.csdnimg.cn/20200228164255801.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

mstsc访问目标主机的2222端口，脚本会自动将路由转到3389端口

![](https://img-blog.csdnimg.cn/20200228164307850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SSH访问目标主机的2222端口，脚本会自动将路由转到22端口

![](https://img-blog.csdnimg.cn/20201115083132121.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

关注公众号：xie\_sec  ，回复：端口复用   得此脚本。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)