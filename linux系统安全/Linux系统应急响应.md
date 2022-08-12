**目录**

[排查用户相关的信息](#t0)

[排查进程端口相关的信息](#t1)

[查找恶意程序并杀掉](#t2)

[斩草除根](#t3)

[判断入侵方式，修复漏洞](#t4)

* * *

>  当我们被告知一台Linux服务器被黑客入侵，黑客利用该服务器进行挖矿，并且在该服务器上放置了[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)后门。现在我们需要对该服务器做排查，关闭和清除掉挖矿程序以及木马后门，探测出黑客是通过什么方式入侵该服务器的，并且最后要将该漏洞进行修补，以确保服务器的正常运行。

首先，当我们登陆主机后做的第一件事，应该先使用 **history** 查看主机的历史命令，虽然大部分黑客在入侵后会删除使用过的命令，但是不排除有些黑客没有删除使用过的命令。如果没有删除的话，我们就可以通过历史命令，知道黑客做了哪些操作，这样后续工作就非常简单了。传送门——> [Linux中的.bash\_ 文件详解](https://blog.csdn.net/qq_36119192/article/details/95881479)

### 排查用户相关的信息

```
whoami           #查看当前用户身份      
who              #查看当前登录系统的所有用户      
w                #显示已经登陆系统的用户列表，并显示用户正在执行的指令      
users            #显示当前登录系统的所有用户的用户列表      
id               #当前用户信息      
id || (whoami && groups) 2>/dev/null     #当前用户信息      
cat /etc/passwd | cut -d: -f1            #查看所有的用户名      
cat /etc/passwd | grep "sh$"             #查看拥有bash的用户      
awk '/\$1|\$6/{print $1}' /etc/shadow   #查看可以远程登录的用户      1
cat /etc/shadow  #查看密码      1
cat /etc/group   #查看组信息      1
last             #查看登录历史      1
more /etc/sudoers | grep -v "^#\|^$" | grep "ALL=(ALL)"    #查看拥有sudo权限的用户      1
sudo -l             #列出目前用户可执行与无法执行的指令       1
awk -F: '$3==0{print $1}' /etc/passwd   #查看超级用户(uid=0),有些黑客将普通用户的uid改为0，则该普通用户也拥有了超级权限
```


相关文章：[Linux系统登录相关](https://blog.csdn.net/qq_36119192/article/details/94396392)

                  [Linux下的用户、组和权限](https://blog.csdn.net/qq_36119192/article/details/82228791) 

### 排查进程端口相关的信息

```
top                          #动态查看进程      
ls -l /proc/18176/exe        #查看PID为18176的进程的可执行程序      
lsof -p 18176                #查看PID为18176的进程打开的文件      
lsof -c sshd                 #查看进程sshd打开的文件      
lsof -i:33946                #查看33946端口对应的一些进程      
ps -p PID -o lstart          #查看进程的启动时间点      
netstat -pantu | grep 18176  #查看端口连接情况，过滤含有18176的行，就可以查看连接的端口      
fuser -n tcp 33946           #查看33946端口对应的进程PID      
ps aux  /  ps -ef            #静态查看进程      1
pstree                       #查看进程树      1
ps aux --sort -pcpu          #静态查看进程，根据cpu使用情况排行，从高到低      1
ps aux --sort -pmem          #静态查看进程，根据内存使用情况排行，从高到低
```


![](https://img-blog.csdnimg.cn/20190820125523135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190820130241630.png)

![](https://img-blog.csdnimg.cn/20190820130104619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190820130134323.png)

![](https://img-blog.csdnimg.cn/20190820125605625.png)

![](https://img-blog.csdnimg.cn/20190820125821187.png)

![](https://img-blog.csdnimg.cn/20190820125957958.png)

![](https://img-blog.csdnimg.cn/20190820125640307.png)

相关文章：[Linux下性能监控、守护进程与计划任务管理](https://blog.csdn.net/qq_36119192/article/details/82191051) 

### 查找恶意程序并杀掉

由上面我们可知，恶意进程的 PID 为 18176，进程占用的本地端口是33946，进程名字为 vvpKI1

当使用下面命令查找恶意进程的可执行程序时，发现恶意进程的可执行程序被删了，可知该木马是个无文件内存木马

```
ls -l /proc/18176/exe
```


![](https://img-blog.csdnimg.cn/20190820130527480.png)

查看该进程打开的文件，有时候，恶意程序会打开一些隐藏的字典库，对内网其他主机进行爆破

```
lsof -p 18176
```


![](https://img-blog.csdnimg.cn/20190820130616328.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

跟踪异常进程运行情况

```
strace -tt -T -e trace=all -p 1389       #跟踪1389进程运行情况
```


![](https://img-blog.csdnimg.cn/20190820130641515.png)

杀掉该恶意进程，但是大多数情况下，该进程还会再启动，因为有守护进程。

```
kill -9 18176
```


### 斩草除根

当我们发现，杀掉了恶意进程后，过一段时间，该进程又会重新启动，则说明该进程有守护进程。则我们第一想到了就是crontab定时任务了。

可以使用以下命令查看定时任务

```
crontab -l  或者  cat /var/spool/cron/root
```


![](https://img-blog.csdnimg.cn/20190820162314916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们也可以查看定时任务的日志文件 

```
more   /var/log/cron
```


![](https://img-blog.csdnimg.cn/20190820162422308.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
#查找cron文件中是否存在恶意脚本       
/var/spool/cron/*    /etc/crontab          /etc/cron.d/*       /etc/cron.daily/* /etc/cron.hourly/*   /etc/cron.monthly/*   /etc/cron.weekly/   /etc/anacrontab     /var/spool/anacron/*       
#查看指定目录最近3天内被修改的文件      
find /etc/ /usr/bin/ /usr/sbin/ /bin/ /usr/local/bin/  -type f -mtime -3 | xargs ls -la       
#按时间排序，确认最近是否有命令被替换，可以结合rpm -Va命令      
ls -alt /usr/bin /usr/sbin /bin /sbin /usr/local/bin | rpm -Va>rpm.log       1
#查看是否有异常开机启动项      1
cat /etc/rc.local | chkconfig --list
```


我们就可以分析定时任务的命令，根据该命令找到恶意文件的源头，斩草除根！

还有一个我们要想到的地方就是 /root/.ssh/authorized\_keys 这个文件，大多数黑客入侵后，会在该文件中写入ssh 私钥，以便于下次登录时直接登录。我们查看该文件，如果存在黑客写入的私钥，删除！

**借助工具查杀病毒和rootkit**

```
#查杀rootkit      
chkrootkit (下载地址-http://www.chkrootkit.org)rkhunter (下载地址-http://rkhunter.sourceforge.net)       
#查杀病毒      
clamav(下载地址-http://www.clamav.net/download.html)         
#查杀webshell      
cloudwalker(下载地址-http://github.com/chaitin/cloudwalker)
```


### 判断入侵方式，修复漏洞

在将黑客放入的恶意程序和木马删除干净后，我们就大概可以知道黑客是如何入侵该主机的，进而修改该漏洞，防止黑客再次入侵。

查看 /var/log/secure文件

```
cat /var/log/secure* | grep Accepted   #查看登录成功的记录      
cat /var/log/secure* | grep Failed     #查看登录失败的记录       
grep "Accepted " /var/log/secure* | awk '{print $1,$2,$3,$9,$11}'  #查看登录成功的日期、用户名及ip      
grep "Failed password for root" /var/log/secure | awk '{print $11}'  #查看有哪些ip在爆破主机的root账号      
grep "Failed password" /var/log/secure | awk {'print $9'} | sort | uniq -c | sort -nr  #查看爆破用户名字典
```


![](https://img-blog.csdnimg.cn/2019072209582543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190722095858503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190722101749543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

总的来说，黑客入侵主机有下列几种情况：

*   通过 redis 未授权漏洞入侵(好多挖矿程序是通过这个)
*   ssh 弱口令暴力破解
*   Web 程序漏洞入侵

参考文章:   [记一次Linux木马清除过程](https://mp.weixin.qq.com/s/NkleRQknAbvwvkYtNCo-Ww)

相关文章：[Redis未授权访问漏洞](https://blog.csdn.net/qq_36119192/article/details/84620648)

                  [Linux挖矿病毒的清除与分析](https://www.freebuf.com/vuls/200289.html)

                  [Linux下性能监控、守护进程与计划任务管理](https://blog.csdn.net/qq_36119192/article/details/82191051#%E5%9B%9B%EF%BC%9A%E8%AE%A1%E5%88%92%E4%BB%BB%E5%8A%A1%E7%AE%A1%E7%90%86)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中