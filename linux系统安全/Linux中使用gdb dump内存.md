在应急响应中，我们往往会有[dump](https://so.csdn.net/so/search?q=dump&spm=1001.2101.3001.7020)出某一块内存下来进行分析的必要。今天要讲的是利用gdb命令dump出sshd进程的内存。

按照 Linux 系统的设计哲学，内核只提供dump[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)的机制，用户想要dump什么样的内存，dump多少内存是属于策略问题，由用户来决定。

在真实的使用场景中，主要有两种使用方式：

*   一种是dump某一个进程的地址空间来供用户在进程挂掉之后debug分析，也就是通常所说的coredump
*   另一种就是dump整个系统的内存空间，以便于系统管理员debug分析系统挂掉的原因，也就是通常所说的 kdump，由于dump内存的逻辑依然需要系统可以正常工作，管理系统的各种资源，所以kdump整个过程依赖kexec和一个额外的dump内核来保证整个流程正确的执行。

以下演示的是dump出某一个进程(sshd)的内存。

```
ps aux | grep sshd    #过滤出sshd进程,记录其PID      
cat /proc/1431/maps   #查看1431进程的内存地址
```


![](https://img-blog.csdnimg.cn/20190719095831320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

启动gdb，将sshd进程attach到gdb上

```
gdb attach 1431
```


![](https://img-blog.csdnimg.cn/20190719100023112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

dump指定内存地址到指定的目录下

```
dump memory /tmp/sshd.dump 0x7f25c2a4e000 0x7f25c2a59000  #这里只dump sshd进程的第一块内存       
dump memory /tmp/sshd.dump 0x7f25c2a4e000 0x7f25c2e61000  #dump了指定的内存块      
# "dump memory"是命令       
# "/tmp/sshd.dump"是我们想保存dump出的内容的路径。       
# 两个hex是内存地址区间，这跟/proc/1431/maps的格式有些不一样。这是以0x开头的16进制表示的。
```


![](https://img-blog.csdnimg.cn/20190719101525152.png)

之后，去/tmp目录下，可以看到dump出的sshd.dmp内存

![](https://img-blog.csdnimg.cn/20190719100624434.png)

```
strings -10 sshd.dump   #查看sshd.dump内存中至少大于10字符的字符
```


![](https://img-blog.csdnimg.cn/20190719102553484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中