**目录**

[SSP](#t0 "SSP")

[mimikatz注入伪造的SSP](#t1 "mimikatz注入伪造的SSP")

[SSP维持域控制器权限的防御措施](#t2 "SSP维持域控制器权限的防御措施")

* * *

SSP
---

**SSP(Security Support Provider)**是Windows操作系统安全机制的提供者。简单的说，SSP就是一个DLL文件，主要用来实现Windows操作系统的身份认证功能，例如：NTLM、Kerberos、Negotiate、Secure Channel(Schannel)，Digest、Credential(CredSSP)

SSPI(Security Support Provider Interface)安全支持提供接口，是Windows操作系统在执行认证操作时使用的API接口。可以说，SSPI是SSP的API接口。

如果获得了网络中目标机器的System权限，可以使用该方法进行持久化操作。其主要原理是：LSA(Local Security Authority)用于身份验证；lsass.exe作为Windows的系统进程，用于本地安全和登录策略；在系统启动时，SSP将被加载到lsass.exe进程中。但是，假如攻击者对LSA进行了扩展，自定义了恶意的DLL文件，在系统启动时将其加载到lsass.exe进程中，就能够获取lsass.exe进程中的明文密码。这样，即使用户更改密码并重新登录，攻击者依然可以获取该账号的新密码。

### mimikatz注入伪造的SSP

实战中通常两种方法一起使用，这样不管目标主机是否重启，都会记录到登录的账号密码。

**方法一：**使用mimikatz将伪造的SSP注入内存，这样做不会在系统中留下二进制文件，但如果域控重启，被注入的内存的伪造的SSP将会丢失。

```
privilege::debug      
misc::memssp
```


![](https://img-blog.csdnimg.cn/202002181917479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后只要目标机器不重启，在目标机器上登录的用户名和密码将会被记录在 C:\\Windows\\System32\\mimilsa.log 文件中。

![](https://img-blog.csdnimg.cn/20200218192139128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200218193008776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**方法二：**将mimikatz中的mimilib.dll放到系统的C:\\Windows\\System32\\ 目录下，并将 mimilib.dll 添加到注册表中，使用这种方法，即使系统重启，也不会影响持久化效果。

![](https://img-blog.csdnimg.cn/20200218192941989.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改注册表 HKEY\_LOCAL\_MACHINE/System/CurrentControlSet/Control/Lsa 的 Security Packages 项，加载新的DLL文件。![](https://img-blog.csdnimg.cn/20200218193320707.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020021819341937.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

系统重启后，如果DLL被成功加载，则用户在登录时输入的账号密码将会被记录在 C:\\Windows\\System32\\kiwissp.log中。

![](https://img-blog.csdnimg.cn/20200218193812229.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200218193938467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

SSP维持域控制器权限的防御措施
----------------

*   检查 HKEY\_LOCAL\_MACHINE/SYSTEM/CurrentControlSet/Control/Lsa的Security Packages 项目中是否含有可疑的DLL文件。
*   检查C:\\Windows\\System32\\  目录下是否有可疑的DLL文件
*   使用第三方工具检查LSA中是否有可疑的DLL文件

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)