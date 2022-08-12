以下是Linux下一些查询的命令，可用于在Linux后渗透信息收集。

```
#系统信息      
hostname 或 uname -n     #主机名      
history      #查看命令历史，有可能可以查看到管理员的一些重要命令，包括密码等      
uname -a     #所有版本      
uname -r     #内核版本信息       
uname -m     #Linux内核架构      
cat /proc/version   #内核信息      
cat /proc/cpuinfo   #CPU信息      1
cat /etc/*-release  #发布信息      1
cat /etc/issue      #发布信息       1
#用户和组信息      1
whoami           #查看当前用户身份      1
who              #查看当前登录系统的所有用户      1
w                #显示已经登陆系统的用户列表，并显示用户正在执行的指令      1
users            #显示当前登录系统的所有用户的用户列表      1
id               #当前用户信息      1
id || (whoami && groups) 2>/dev/null     #当前用户信息      2
cat /etc/passwd | cut -d: -f1            #查看所有的用户名      2
cat /etc/passwd | grep "sh$"             #查看拥有bash的用户      2
awk '/\$1|\$6/{print $1}' /etc/shadow   #查看可以远程登录的用户      2
cat /etc/shadow  #查看密码      2
cat /etc/group   #查看组信息      2
last             #查看登录历史      2
more /etc/sudoers | grep -v "^#\|^$" | grep "ALL=(ALL)"    #查看拥有sudo权限的用户      2
sudo -l             #列出目前用户可执行与无法执行的指令       2
awk -F: '$3==0{print $1}' /etc/passwd   #查看超级用户(uid=0),有些黑客将普通用户的uid改为0，则该普通用户也拥有了超级权限       3
#登录信息      3
last             #查看最近登录成功的用户及信息，查看的是 /var/log/wtmp 文件      3
lastb            #查看最近登录失败的用户及信息，查看的是 /var/log/btmp 文件      3
lastlog          #显示系统中所有用户最近一次登录信息，读取的是 /var/log/lastlog 文件      3
cat /var/log/secure* | grep Accepted   #查看登录成功的记录      3
cat /var/log/secure* | grep Failed     #查看登录失败的记录      3
grep "Accepted " /var/log/secure* | awk '{print $1,$2,$3,$9,$11}'  #查看登录成功的日期、用户名及ip      3
grep "Failed password for root" /var/log/secure | awk '{print $11}'  #查看有哪些ip在爆破主机的root账号      3
grep "Failed password" /var/log/secure | awk {'print $9'} | sort | uniq -c | sort -nr  #查看爆破用户名字典       4
#网络信息      4
ifconfig      4
arp -a      4
cat /etc/network/interfaces   #查看网络接口信息      4
route       #查看路由信息      4
traceroute baidu.com    #查看路由节点      4
cat /etc/hosts   #查看hosts文件      4
cat /etc/resolv.conf  #查看dns信息      4
cat /etc/services     #查看端口服务映射       5
#服务、端口和进程信息      5
ps aux  /  ps -ef            #静态查看进程      5
pstree                       #查看进程树      5
top                          #动态查看进程      5
ps aux --sort -pcpu          #静态查看进程，根据cpu使用情况排行，从高到低      5
ps aux --sort -pmem          #静态查看进程，根据内存使用情况排行，从高到低      5
netstat -pantu               #查看端口信息      5
netstat -pantu | grep 18176  #查看端口连接情况，过滤含有18176端口的行，就可以查看连接的端口      5
cat /etc/inetd.conf          #由inetd管理的服务列表      5
cat /etc/xinetd.conf         #由xinetd管理的服务列表      6
cat /etc/exports             #nfs服务器的配置      6
cat /etc/services            #查看端口服务映射      6
ls -l /proc/18176/exe        #查看PID为18176的进程的可执行程序      6
lsof -p 18176                #查看PID为18176的进程打开的文件      6
lsof -c sshd                 #查看进程sshd打开的文件      6
lsof -i:33946                #查看33946端口对应的一些进程      6
ps -p PID -o lstart          #查看进程的启动时间点      6
fuser -n tcp 33946           #查看33946端口对应的进程PID       6
#环境信息      7
env   #打印系统环境信息      7
set   #打印系统环境信息      7
echo  $PATH   #环境变量中的路径信息       7
history       #历史命令      7
cat /etc/profile   #显示默认系统遍历      7
cat /etc/shells   #显示可用的shell
```


未完待续。。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)