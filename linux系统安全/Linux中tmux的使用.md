**目录**

[Tmux](#t0)

[会话操作](#t1)

[新建会话](#t2)

[退出会话](#t3)

[查看会话列表](#t4)

[进入会话](#t5)

[销毁会话](#t6)

[重命名会话](#t7)

[窗口操作](#t8)

[新建窗口](#t9)

[切换窗口](#t10)

[关闭窗口](#t11)

[窗格](#t12)

[垂直分屏](#t13)

 [水平分屏](#t14)

 [切换窗格](#t15)

[关闭窗格](#t16)

* * *

Tmux
----

我们在linux服务器上的工作一般都是通过一个远程的终端连接软件连接到远端系统进行操作，例如使用[xshell](https://so.csdn.net/so/search?q=xshell&spm=1001.2101.3001.7020)或者SecureCRT工具通过ssh进行远程连接。在使用过程中，如果要做比较耗时的操作，例如有时候进行编译，或者下载大文件需要比较长的时间，一般情况下是下班之后直接运行希望第二天早上过来运行完成，这样就不用耽误工作时间。但是网络有时候不稳定，可能在半夜会出现连接断掉的情况，一旦连接断掉，我们所执行的程序也就中断，我们当然可以写一个脚本后台运行，但是还是不方便。那么有没有一种工具可以解决这样的问题呢。这就是我们这里要提到的tmux了。其实类似tmux的工具还有很多。例如gnu screen等。tmux刚好可以解决我们描述的问题，当我们在tmux中工作的时候，即使关掉SecureCRT的连接窗口，再次连接，进入tmux的会话我们之前的工作仍然在继续。

[tmux](https://so.csdn.net/so/search?q=tmux&spm=1001.2101.3001.7020)中有3种概念，会话，窗口(window)，窗格(pane)。会话有点像是tmux的服务，在后端运行，我们可以通过tmux命令创建这种服务，并且可以通过tmux命令查看，附加到后端运行的会话中。一个会话可以包含多个窗口，一个窗口可以被分割成多个窗格(pane)。

tmux的安装方法很简单，可直接通过apt安装

```
sudo apt-get install tmux
```


会话操作
----

### 新建会话

```
tmux new -s [会话名]
```


### 退出会话

```
ctrl+b d
```


### 查看会话列表

```
tmux ls
```


![](https://img-blog.csdnimg.cn/20210628172133216.png)

如果是在某个会话环境中想查看会话列表，可以用以下指令来获得，然后用键盘选择就可进入

```
ctrl+b s
```


### 进入会话

```
tmux a -t [会话名]
```


### 销毁会话

```
tmux kill-session -t [会话名]
```


![](https://img-blog.csdnimg.cn/20210628172245750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

### 重命名会话

```
tmux rename -t [旧会话名] [新会话名]
```


窗口操作
----

一个tmux的会话中可以有多个窗口(window)，每个窗口又可以分割成多个pane(窗格)。我们工作的最小单位其实是窗格。默认情况下在一个window中，只有一个大窗格，占满整个窗口区域。我们在这个区域工作。

### 新建窗口

```
ctrl+b c
```


默认情况下创建出来的窗口由窗口序号+窗口名字组成，窗口名字可以由上面提到的方法修改，可以看到新创建的窗口后面有\*号，表示是当前窗口。

### 切换窗口

在同一个会话的多个窗口之间可以通过如下快捷键进行切换：

*   ctrl+b p (previous的首字母) 切换到上一个window。
*   ctrl+b n (next的首字母) 切换到下一个window。
*   ctrl+b 0 切换到0号window，依次类推，可换成任意窗口序号
*   ctrl+b w (windows的首字母) 列出当前session所有window，通过上、下键切换窗口
*   ctrl+b l (字母L的小写)相邻的window切换

### 关闭窗口

```
ctrl+b &
```


窗格
--

tmux的一个窗口可以被分成多个pane(窗格)，可以做出分屏的效果。

### 垂直分屏

```
ctrl+b %
```


###  水平分屏

```
ctrl+b "
```


###  切换窗格

*   ctrl+b o 依次切换当前窗口下的各个pane。
*   ctrl+b Up|Down|Left|Right 根据按箭方向选择切换到某个pane。
*   ctrl+b Space (空格键) 对当前窗口下的所有pane重新排列布局，每按一次，换一种样式。
*   ctrl+b z 最大化当前pane。再按一次后恢复。

### 关闭窗格

*   ctrl+b x 关闭当前使用中的pane，操作之后会给出是否关闭的提示，按y确认即关闭。

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中