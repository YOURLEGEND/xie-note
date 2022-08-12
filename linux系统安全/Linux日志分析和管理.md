**目录**

[日志的作用、分类、管理、轮转和级别](#t0)

[rsyslog服务](#t1)

[Journal守护进程](#t2)

[/var/log下相关的日志文件](#t3)

[日志服务器的建立](#t4)

* * *

### 日志的作用、分类、管理、轮转和级别

日志的作用：

*   用于记录系统、程序运行中发生的各种事件
*   通过阅读日志，有助于诊断，解决系统故障

日志文件的分类：

*   内核及系统日志：由系统服务 rsyslog 统一管理，日志格式相似
*   用户日志：记录系统用户登录及退出系统的相关信息
*   程序日志：由各种应用程序独立管理的日志文件，格式不统一

日志管理策略  
日志也并不是完全可靠的，高级的黑客在入侵系统后，会删除相应的日志记录，因此需要做好日志的管理工作：

*   日志的备份和归档
*   延长日志的保存期限
*   控制日志的访问权限
*   集中管理日志。比如，将服务器的日志文件发送到统一到日志文件服务器，这样便于日志信息的统一收集、整理和分析，还可以杜绝日志信息的意外丢失、恶意修改和删除

日志的轮转和切割  
随着时间的推移，日志文件肯定会越来越大，而且这个趋势是呈线性增长。所以，需要对之前的日志文件做一些处理。日志轮转和切割指的是实现对当前日志归档，开始新的日志，删除早期的日志。Linux中，日志轮转和切割这个服务是由 logrotate 提供的。logrotate这个程序的目录：/etc/cron.daily/logrotate  。logrotate 是作为 corn 的一个每日任务，周期性执行的。它具备自动轮转、压缩、搬迁 和 邮件通知到日志系统的多项功能。每一个日志文件都可以按照每天、每周、每月周期性处理，或是增长到多大而触发处理。

日志消息的级别

日志消息的级别
| level等级 | 状况 |
| --- | --- |
| 0 EMERG（紧急） | 会导致主机系统不可用的情况 |
| 1 ALERT （警告） | 必须马上采取措施解决的情况 |
| 2 CRIT （严重） | 比较严重的情况 |
| 3 ERR （错误） | 运行出现错误 |
| 4 WARNING (提醒) | 可能会影响系统功能的事件 |
| 5 NOTICE (注意) | 不会影响系统但值得注意 |
| 6 INFO （注意） | 一般信息 |
| 7 DEBUG（调试） | 程序或系统调试信息等 |

### rsyslog服务

rsyslog服务是由 rsyslogd 程序提供的。rsyslogd 程序负责收集和管理与系统有关的日志

*   程序目录：/sbin/rsyslogd  
*   rsyslogd配置文件所在目录：/etc/rsyslog.conf 
*   rsyslogd相关文件：/etc/sysconfig/rsyslog  
*   和日志轮转(切割)相关文件：/etc/logrotate.d/syslog

系统中的程序，在收集日志的时候，都是调用 syslog() 函数，这个函数的作用是发送日志消息给系统的 logger，然后由logger 根据 rsyslogd 程序的配置文件 /etc/rsyslog.conf 中的规则，将日志消息按照不同的格式写入不同的文件中。

syslog() 函数内部有不同的设备，不同的程序有可能对应相同的设备，也有可能对应不同的设备。通过 man  3  [syslog](https://so.csdn.net/so/search?q=syslog&spm=1001.2101.3001.7020)  可查看syslog中的不同设备和不同预警级别。程序所对应的设备大部分不可修改，都已经编译好。但是有的程序的配置文件中允许修改设备。比如sshd程序，在其配置文件 /etc/ssh/sshd\_config 中可修改设备。LOCAL0 ~ LOCAL6 是自定义设备。不建议修改程序所对应的默认设备。程序所对应的设备这个不是我们所关心的，是开发者已经配置好的。

rsyslogd 中有不同的规则，指定了不同的设备的不同预警级别对应的不同文件。logger 就是根据  rsyslogd 中的规则进行日志写入。rsyslogd 中的规则我们自己可以定义修改增加。所以如果我们关闭 rsyslogd 程序的话，logger就不知道该将日志文件写入哪里，所以就会产生错误。 

一些程序所对应的设备
| 程序 Program | 设备 Facility |
| --- | --- |
| /usr/sbin/sshd | LOG\_AUTHPRIV |
| /usr/bin/su | LOG\_AUTHPRIV |
| /usr/bin/login | LOG\_AUTHPRIV |
| /usr/sbin/vsftpd | LOG\_FTP |
| /usr/bin/at | LOG\_CRON |
| /usr/sbin/crond | LOG\_CRON |
| /usr/sbin/portfix | LOG\_MAIL |

rsyslogd的配置文件 /etc/rsyslog.conf 中的一些规则
| 设备 Facility 和 级别 | 对应的文件 |
| --- | --- |
| \*.info; mail.none; authpriv.none; cron.none (任何设备的这些级别) | /var/log/messages |
| authpriv.\*  (authpriv设备的所有级别) | /var/log/secure |
| mail.\* (mail设备的所有级别) | \-/var/log/maillog |
| cron.\*  (cron设备的所有级别) | /var/log/cron |
| \*.emery (所有设备的emery级别) | :omusrmsg:\*   （打印到终端） |
| local0.\*   ~  local6.\* | 自己可定义文件 |

![](https://img-blog.csdn.net/20181001084739311?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

但是像我们很多自己的应用,不是由 rsyslogd进程收集和管理日志，因为他们不调用 syslog()函数。比如apache、nginx、mysql、httpd 都有自己的日志进程负责收集和管理日志。  
日志文件默认都是保存在 /var/log/ 目录下。

常见的日志文件目录
| 名称 | 路径 |
| --- | --- |
| 内核、公共消息日志、系统主日志文件 | /var/log/messages |
| 用户登录日志 | /var/log/secure  |
| 计划任务日志 | /var/log/cron |
| 跟yum安装有关 | /var/log/yum.log |
| 系统引导日志 | /var/log/dmesg |
| 邮件系统日志，跟postfix有关 | /var/log/maillog |
| 当前登录的用户，二进制文件，可用 last 查看 | /var/log/wtmp |
| 最近登录的用户 , 二进制文件  ，可用 lastb 查看 | /var/log/btmp |
| 所有用户的登录情况，二进制文件，可用 lastlog 查看 | /var/log/lastlog |

这个是  /var/log/secure 里的登录日志 ，第一行说明root用户登录xie登录成功。第二三行说明xie用户想登录bob用户然后认证失败了，也就是密码错误。第四行说明xie用户退出登录了。

![](https://img-blog.csdn.net/20180911190332711?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 grep 'Fail' /var/log/secure | awk '{print $11}' | sort |uniq -c | sort -k1 -n -r | head -5     查看登录失败的那一行，然后打印出第11列(从后数)， 然后排序，然后去除重复，然后按第一列排序 ，然后查看前五个

### Journal守护进程

在Rhel7中，syslog 消息除了由 rsyslog 服务处理外，还新加了一个 journal 监听，日志文件在 /run/log/journal 目录中。  
systemd-journald [守护进程](https://so.csdn.net/so/search?q=%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B&spm=1001.2101.3001.7020)提供了一个改进的日志管理服务。在使用其它工具时，日志往往被分散在整套系统当中，由不同的守护进程及进程负责处理，这意味着我们很难跨越多种应用程序对其内容进行解读。而 systemd 尝试提供一套集中化管理方案，从而统一打理全部内核及用户级进程的日志信息，这也就是我们journal。这套系统能够收集并管理日志内容，它从内核中收集消息：**启动过程的早期阶段、标准输出和守护进程的错误。当它们启动和运行时，以及 syslog** ，它将这些消息写入到结构化的事件日志。syslog消息也由 systemd-journald 转发到 rsyslog 服务，然后按类型(或设备)和优先级对消息进行排序，并将他们写入到 /var/log/ 日志目录中的持久文件中。

但是，我们的 /run/log/journal 目录下的日志信息并不能直接查看，该文件是二进制文件，需要借助命令查看

![](https://img-blog.csdnimg.cn/20181205082449769.png)

journal日志的查看

*   journalctl                                   显示所有的日志信息
*   journalctl  -n  10                        显示最近10条的日志信息
*   journalctl  -u   nginx.service       查看nginx服务的日志自信息
*   journalctl  \_PID=1234                查看PID进程为1234的日志信息
*   journalctl  \_UID=0                     查看UDI为0的用户的日志信息
*   journalctl  /usr/bin/bash             查看指定路径可执行文件的日志信息
*   journalctl  --since  today            查看今天以来的日志的信息

### /var/log下相关的日志文件

![](https://img-blog.csdnimg.cn/20190712220757767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   /var/log/message:几乎所有的开机系统发生的错误都会在此记录;
*   /var/log/secure：记录登录系统存取数据的文件，例如：ssh、pop3、telnet，ftp等都会记录在此.。
*   /var/log/wtmp：记录ssh登录成功的记录，二进制文件
*   /var/log/btmp：记录ssh登录失败的记录，二进制文件
*   /var/log/lastlog 记录每个用户最后的登录信息;
*   /var/log/boot.log：记录一些开机或者关机启动的一些服务显示的启动或者关闭的信息;
*   /var/log/maillog：记录邮件的存取和往来;
*   /var/log/cron：用来记录crontab定时任务的记录;
*   /var/log/httpd：http服务的记录
*     /var/log/audit：包含被 Linux audit daemon储存的信息
*   /var/log/dmesg：内核日志;
*   /var/log/cpus： CPU的处理信息；
*   /var/log/anaconda.log：在安装Linux时，所有的安装信息记录
*   /var/log/syslog： 事件记录监控程序日志；
*   /var/log/auth.log： 用户认证日志；
*   /var/log/daemon.log： 系统进程日志；
*   /var/log/mail.err ：邮件错误信息；
*   /var/log/mail.info： 邮件信息；
*   /var/log/mail.warn： 邮件警告信息；
*   /var/log/daemon.log： 系统监控程序产生的信息;
*   /var/log/kern： 内核产生的信息;
*   /var/log/lpr ：  行打印机假脱机系统产生的信息;
*   /var/log/sa：包含每日由sysstat软件包收集的sar文件。
*   /var/log/yum.log：使用yum安装的软件包的记录

### [日志服务器的建立](https://blog.csdn.net/qq_36119192/article/details/82906799#%E5%BB%BA%E7%AB%8B%E6%97%A5%E5%BF%97%E6%9C%8D%E5%8A%A1%E5%99%A8)

文章：[建立日志服务器](https://blog.csdn.net/qq_36119192/article/details/82906799#%E5%BB%BA%E7%AB%8B%E6%97%A5%E5%BF%97%E6%9C%8D%E5%8A%A1%E5%99%A8)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中