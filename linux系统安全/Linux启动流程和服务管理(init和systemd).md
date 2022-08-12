**目录**

[一：Linux启动流程](#t0)

       [init和Systemd的区别](#init%E5%92%8CSystemd%E7%9A%84%E5%8C%BA%E5%88%AB)

[二：Linux服务管理(service,systemctl)](#t1)

* * *

**一：Linux启动流程**
---------------

Rhel6启动过程：

![](https://img-blog.csdn.net/20180905095900236?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)  
Rhel7启动过程：

![](https://img-blog.csdn.net/20180905100122333?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 GRUB2相较于GRUB一代的提升：更健壮、可移植、更强大。支持[BIOS](https://so.csdn.net/so/search?q=BIOS&spm=1001.2101.3001.7020)、EFI和OpenFirmware，支持GPT和MBR分区表。支持非Linux系统，如苹果HFS文件系统和Windows的NTFS文件系统

systemd 被设计用来改进 sysvinit 的缺点，它和ubuntu的upstart是竞争对手，预计会取代它们。

systemd的目标是：尽可能启动更少进程；尽可能将更多进程并行启动。systemd尽可能减少对shell脚本的依赖。传统sysvinit使用inittab来决定运行哪些shell脚本，大量使用shell脚本被认为是效率低下无法并行的原因。systemd使用了Linux专属技术，不再顾及POSIX兼容。

### **init和Systemd的区别**

init： 

*   一是启动时间长，init是串行启动，只有前一个进程启动完，才会启动下一个进程
*   二是启动脚本复杂，Init进程只是执行启动脚本，不管其他事情，脚本需要自己处理各种情况，这往往使得脚本变得很长
*   由Linux内核加载运行，位于 /sbin/init   ,是系统中第一个进程，PID永远为1

![](https://img-blog.csdnimg.cn/20181113080232971.png)

对于支持 service 的程序，安装的时候，会自动的在 /etc/init.d 目录添加一个配置文件。当我们使用 service 控制程序时，比如执行开启httpd的服务：service httpd  start  。那么我们的 service 就会开启 /etc/init.d/httpd 配置文件里面指向的 /usr/sbin/httpd 可执行文件

![](https://img-blog.csdnimg.cn/20181114083642541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

systemd：

*   按需启动服务，减少系统资源消耗。
*   尽可能并行启动进程，减少系统启动等待时间
*   由Linx内核加载运行，位于 /usr/lib/systemd/systemd  ，是系统中第一个进程，PID永远为1

![](https://img-blog.csdnimg.cn/20181113080358969.png)

对于支持 systemd 的程序，安装的时候，会自动的在 /usr/lib/systemd/system 目录添加一个配置文件。当我们使用 systemctl 控制该程序时，比如执行开启httpd服务：systemctl  start  httpd.service 。那么我们的 systemctl 就会开启 httpd.service 配置里面指向的 /usr/sbin/httpd 可执行文件

![](https://img-blog.csdnimg.cn/20181114082845857.png)

如果我们想让该程序开机启动，我们可以执行命令 systemctl enable  httpd，这个命令相当于在 /etc/systemd/system 目录添加一个软链接，指向 /usr/lib/systemd/system 目录下的 httpd.service 文件。这是因为开机时，`Systemd`只执行 `/etc/systemd/system` 目录里面的配置文件。

![](https://img-blog.csdnimg.cn/2018111308234855.png)

**Init 进程的配置文件**

| 参数 | 说明 |
| --- | --- |
| /etc/init.d/ | 服务启动脚本配置文件存放目录 |
| /etc/inittab | 默认运行级别配置文件 |
| /etc/init/rcS.conf | 系统初始化配置文件 |
| /etc/init/rc.conf | 各运行级别初始化的配置文件 |
| /etc/init/rcS-sulogin.conf | 单用户模式启动 /sbin/sushell 环境的配置文件 |
| /etc/init/control-alt-delete.conf | 终端下的 ctrl+alt+del 热键操作的配置文件 |
| /etc/sysconfig/init | tty终端的配置文件 |
| /etc/init/start-ttys.conf | 配置tty终端的开启数量、设备文件 |
| /etc/init/tty.conf  或  /etc/init/serial.conf | 控制tty终端的开启 |

**Systemd进程的配置文件**

| 参数 | 说明 |
| --- | --- |
| /etc/systemd/system/default.target | 取代/etc/inittab文件配置，通常符号链接到 /lib/systemd/system/graphical.target |
| /run/systemd/system/ | 系统执行过程中所产生的服务脚本所在目录 |
| /etc/systemd/system/ | 里面存放着不同级别的开启自启服务  |
| /usr/lib/systemd/system/ 和 /lib/systemd/system/ 和，两个文件完全一样，因为lib是/usr/lib的软链接 | 每个服务最主要的启动脚本设置，类似于之前的 /etc/init.d/ |

 **运行级别和说明**

| 运行级别 | 说明 | Rehl 6/7 命令 | Rhel7 命令 |
| --- | --- | --- | --- |
| 0 | 关机状态，使用该级别将会关机 | init  0 |  poweroff |
| 1 | 系统救援模式，多用于系统维护 | init  1 | systemctl  isolate  rescue.target |
| 2 | 字符界面的多用户模式(不可访问网络) | init  2 | systemctl  isolate  mutil-user.target |
| 3 | 字符界面的完整多用户模式，大多数服务器主机运行此级别 | init  3 | systemctl  isolate  mutil-user.target |
| 4 | 未分配使用 | init  4 | systemctl  isolate  mutil-user.target |
| 5 | 图形界面的多用户模式，提供了图形桌面操作环境 | init  5 | systemctl  isolate  graphical.target |
| 6 | 重新启动主机 | init  6 | reboot |

查看运行级别：

*   runlevel : 显示切换前的运行级别 和当前运行级别 （6/7）
*   systemctl get-default : 显示当前运行级别 (7)

永久设置开机模式

*    systemctl set-default multi-user.target    开机默认为文本模式
*    systemctl set-default graphical.target     开机默认为图形模式
*    修改 /etc/inittab 默认运行级别配置文件

**二：Linux服务管理(service,systemctl)**
----------------------------------

Rhel6 用 service 和 chkconfig 来管理服务，它是 SystemV 架构下的一个工具。  
Rhel7 是用 systemctl  来管理服务，它融合了之前的 service 和 chkconfig 的功能于一体。可以使用它永久性或只在当前会话中启用/禁用服务。systemctl 是 systemd 架构下的一个工具。

| 动作 | Rhel6 旧指令 | Rhel7新指令 |
| --- | --- | --- |
| 启动某服务 | service  httpd   start | systemctl  start   httpd |
| 停止某服务 | service  httpd   stop  | systemctl  stop   httpd |
| 重启某服务 | service  httpd   restart | systemctl  restart  httpd |
| 检查服务状态 | service  httpd  status | systemctl  status  httpd |
| 删除某服务  | chkconfig  --del  httpd | 停掉应用，删除其配置文件 |
| 使服务开机自启动 | chkconfig  --level   5  httpd   on | systemctl   enable  httpd |
| 使服务开机不自启动 | chkconfig  --level   5  httpd   off | systemctl   disable  httpd |
| 显示所有已启动的服务 | chkconfig  --list | systemctl list-unit-files | grep enabled |
| 加入自定义服务 | chkconfig  --add  test | systemctl   load  test |
| 查询服务是否开机自启 | chkconfig --list | grep httpd | systemctl  is-enabled   httpd |
| 查看启动失败的服务 |   | systemctl  --failed |

systemd的一些常用命令：

列出所有可用单元 ： systemctl  list-unit-files  
列出所有运行的单元： systemctl list-unit-files | grep enabled   
列出所有可用服务：  systemctl list-unit-files  --type=service  
列出所有运行的服务： systemctl list-unit-files  --type=service | grep enabled   
屏蔽httpd服务：systemctl  mask httpd

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中