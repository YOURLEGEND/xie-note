**目录**

[FRP](#t0 "FRP")

[使用FRP建立隧道](#t1 "使用FRP建立隧道")

[服务端](#t2 "服务端")

[客户端](#t3 "客户端")

[使用FRP映射Web服务](#t4 "使用FRP映射Web服务")

[服务端](#%E6%9C%8D%E5%8A%A1%E7%AB%AF "服务端")

[客户端](#%E5%AE%A2%E6%88%B7%E7%AB%AF "客户端")

[使用FRP映射3389端口](#t5 "使用FRP映射3389端口")

[服务端](#%E6%9C%8D%E5%8A%A1%E7%AB%AF "服务端")

[客户端](#%E5%AE%A2%E6%88%B7%E7%AB%AF "客户端")

* * *

FRP
===

**FRP(Fast Reverse Proxy)** 是一款简单，好用，稳定的隧道工具。FRP 使用 Go 语言开发，它是一款高性能的**反向代理**应用，可以轻松地进行[内网穿透](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F&spm=1001.2101.3001.7020)，对外网提供服务。FRP 支持 TCP、UDP、KCP、HTTP、HTTPS等协议类型，并且支持 Web 服务根据域名进行路由转发。在进行内网渗透中，FRP是常用的一款隧道工具。

使用FRP建立隧道
---------

现在有这么一个场景，我们获得了一个位于内网的通过NAT方式对外提供服务的主机的权限，现在我们需要对其所在的内网继续进行渗透。于是，我们就需要通过FRP建立一个隧道，让我们的主机可以通过隧道访问其内网。

![](https://img-blog.csdnimg.cn/20190812174621372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **服务端**

我们的vps作为服务端，服务端启动：frps.exe  -c  frps.ini    ，服务端的配置  frps.ini  如下

```
[common]      
bind_addr = 0.0.0.0        #绑定的ip，为本机      
bind_port = 17000          #绑定的端口      
dashboard_addr = 0.0.0.0   #管理地址      
dashboard_port = 27500     #管理端口      
dashboard_user = root      #管理的用户名      
dashboard_pwd  = 123456    #管理用户的密码      
token = 1q2w3e             #客户端服务端连接的密码      
heartbeat_timeout = 90     #心跳超时时间      1
max_pool_count = 5         #最大同时连接数
```


### **客户端**

被控的主机作为客户端，客户端启动：frpc.exe  -c  frpc.ini ，客户端的配置文件 frpc.ini  如下

```
[common]      
server_addr = 100.20.14.14      
server_port = 17000           #服务器绑定的端口      
token = 1q2w3e                #连接的密码      
pool_count = 5              
protocol = tcp                #协议类型      
health_check_type = tcp      
health_check_interval_s = 100      
[test]       1
remote_port = 10000           #代理的端口      1
plugin = socks5               #使用的协议      1
use_encryption = true         #是否加密      1
use_compression = true      1
plugin_user= admin             #socks5用户名      1
plugin_passwd= admin           #socks5密码
```


访问  http://100.20.14.14:27500  ，用  root   123456 登录， 查看TCP。说明隧道搭建完成

![](https://img-blog.csdnimg.cn/20190812175016467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们本机设置代理： socks5   100.20.14.14   10000

使用FRP映射Web服务
------------

现在有这么一个场景，位于内网的主机需要对外提供Web服务，于是将内网主机的80端口映射到公网主机的80端口上

![](https://img-blog.csdnimg.cn/20190812175631783.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 服务端

位于公网的主机作为服务端，服务端启动：frps.exe  -c  frps.ini    ，服务端的配置  frps.ini  如下

```
[common]      
bind_addr = 0.0.0.0        #绑定的ip，为本机      
bind_port = 17000          #绑定的端口      
dashboard_addr = 0.0.0.0   #管理地址      
dashboard_port = 27500     #管理端口      
dashboard_user = root      #管理的用户名      
dashboard_pwd  = 123456    #管理用户的密码      
token = 1q2w3e             #客户端服务端连接的密码      
heartbeat_timeout = 90     #心跳超时时间
```


### 客户端

位于内网的主机作为客户端，客户端启动：frpc.exe  -c  frpc.ini ，客户端的配置文件 frpc.ini  如下

```
[common]      
server_addr = 100.20.14.14      
server_port = 17000           #服务器绑定的端口      
token = 1q2w3e                #连接的密码      
[HTTP]       
type=tcp      
local_ip = 127.0.0.1          #本地ip地址      
local_port = 80               #要映射的本地端口      
remote_port = 80              #要映射的服务端端口      1
custom_domains=www.test.com   #这个必须要写
```


访问  http://100.20.14.14:27500  ，用  root   123456 登录， 查看TCP。说明隧道搭建完成

![](https://img-blog.csdnimg.cn/20190812180611354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后访问http://100.20.14.14 就是相当于访问位于内网的主机 http://192.168.10.10 了

使用FRP映射3389端口
-------------

现在有这么一个场景，我们获得了位于内网的一台主机的权限，并且知道了他的登录用户名和密码。他的3389端口只对内网开放，现在我们需要将该主机的3389端口映射到公网我们的VPS的3389端口，那样，我们连接我们VPS的3389端口就相当于连接内网主机的3389端口了

![](https://img-blog.csdnimg.cn/20190812205348811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 服务端

位于公网的VPS作为服务端，服务端启动：frps.exe  -c  frps.ini    ，服务端的配置  frps.ini   如下：

```
[common]      
bind_addr = 0.0.0.0        #绑定的ip，为本机      
bind_port = 17000          #绑定的端口      
dashboard_addr = 0.0.0.0   #管理地址      
dashboard_port = 27500     #管理端口      
dashboard_user = root      #管理的用户名      
dashboard_pwd  = 123456    #管理用户的密码      
token = 1q2w3e             #客户端服务端连接的密码      
heartbeat_timeout = 90     #心跳超时时间
```


### 客户端

位于内网的主机作为客户端，客户端启动：frpc.exe  -c  frpc.ini ，客户端的配置文件 frpc.ini   如下：

```
[common]      
server_addr = 100.20.14.14      
server_port = 17000      
token = 1q2w3e      
[test]      
type=tcp      
local_ip = 127.0.0.1      
local_port = 3389      
remote_port = 3389
```


访问  http://100.20.14.14:27500  ，用  root   123456 登录， 查看TCP。说明隧道搭建完成

![](https://img-blog.csdnimg.cn/20190812204935899.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

远程连接100.20.14.14的3389端口即可。

![](https://img-blog.csdnimg.cn/20190812205029541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**注：使用FRP映射其他服务，例如SSH、Mysql、Redis等服务都是和映射web、RDP服务一样，修改一下要映射的端口即可。**

FRP工具包下载：关注微信公众号：xie\_sec 回复：FRP 即可下载。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

FRP官方文档：[示例 | frp](https://gofrp.org/docs/examples/ "示例 | frp")

相关文章：[内网穿透工具NPS的使用](https://xie1997.blog.csdn.net/article/details/107768568 "内网穿透工具NPS的使用")